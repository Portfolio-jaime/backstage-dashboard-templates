# 🏗️ Architecture Guide

This document details the system architecture of the Backstage Dynamic Dashboard System.

## 🎯 System Overview

The dynamic dashboard system separates configuration from implementation, enabling rapid dashboard deployment without code changes.

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         BACKSTAGE APPLICATION                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐    │
│  │   HomePage.tsx  │────▶│ useDashboard    │────▶│ DashboardCards  │    │
│  │                 │     │ Config.ts       │     │                 │    │
│  └─────────────────┘     └─────────────────┘     └─────────────────┘    │
│           │                        │                        │           │
│           ▼                        ▼                        ▼           │
│  ┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐    │
│  │ Widget System   │     │ Configuration   │     │ Navigation      │    │
│  │ • TeamActivity  │     │ Parser          │     │ System          │    │
│  │ • WorldClock    │     │ • YAML Parser   │     │ • Template      │    │
│  │ • Catalog       │     │ • Validation    │     │   Selection     │    │
│  │ • SystemHealth  │     │ • Caching       │     │ • Routing       │    │
│  └─────────────────┘     └─────────────────┘     └─────────────────┘    │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                    ┌───────────────┴──────────────────┐
                    │         HTTP Requests            │
                    │    (GitHub Raw File API)         │
                    ▼                                  ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                    GITHUB CONFIGURATION REPOSITORY                     │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  registry.yaml                    templates/                           │
│  ├── Dashboard List               ├── ba-main-dashboard/                │
│  ├── Metadata                     │   └── config.yaml                  │
│  └── Configuration Paths          ├── ba-devops-dashboard/              │
│                                   │   └── config.yaml                  │
│                                   ├── ba-platform-dashboard/            │
│                                   │   └── config.yaml                  │
│                                   ├── ba-security-dashboard/            │
│                                   │   └── config.yaml                  │
│                                   └── ba-developer-dashboard/           │
│                                       └── config.yaml                  │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## 🧩 Component Architecture

### Core Components

#### 1. `useDashboardConfig` Hook
**Location**: `backstage-app-devc/backstage/packages/app/src/hooks/useDashboardConfig.ts`

**Responsibilities**:
- Fetches registry from GitHub
- Parses dashboard configurations
- Manages template switching
- Handles error states and fallbacks
- Provides caching mechanism

**Key Functions**:
```typescript
interface UseDashboardConfigResult {
  config: DashboardConfig | null;
  loading: boolean;
  error: string | null;
  availableTemplates: DashboardTemplate[];
  currentTemplate: DashboardTemplate | null;
  switchTemplate: (templateId: string) => Promise<void>;
  refetch: () => Promise<void>;
}
```

#### 2. `HomePage` Component
**Location**: `backstage-app-devc/backstage/packages/app/src/components/home/HomePage.tsx`

**Responsibilities**:
- Main dashboard container
- Renders dynamic content based on configuration
- Manages widget layout and positioning
- Displays navigation cards
- Handles loading and error states

**Key Features**:
- Dynamic welcome messages from YAML
- Conditional widget rendering
- Material-UI theme integration
- Responsive grid layout

#### 3. `DashboardCards` Component
**Location**: `backstage-app-devc/backstage/packages/app/src/components/home/DashboardCards.tsx`

**Responsibilities**:
- Dashboard navigation interface
- Card-based dashboard selection
- Category grouping and filtering
- Visual indicators for current dashboard

#### 4. Widget System

**TeamActivity Widget**:
```typescript
// Dynamic repository loading based on dashboard configuration
const { config, currentTemplate } = useDashboardConfig();
const keywords = config?.spec?.widgets?.github?.filters?.keywords || [];
```

**WorldClock Widget**:
```typescript
// Timezone configuration from YAML
const timezones = config?.spec?.widgets?.worldClock?.timezones || [];
```

**LiveCatalogServices Widget**:
```typescript
// Real Backstage catalog integration
const catalogApi = useApi(catalogApiRef);
const entities = await catalogApi.getEntities(filters);
```

## 🔄 Data Flow

### 1. Initialization Phase
```
User loads Backstage
    ↓
HomePage mounts
    ↓
useDashboardConfig hook activates
    ↓
Fetch registry.yaml from GitHub
    ↓
Parse available templates
    ↓
Load default or stored dashboard
    ↓
Render dashboard content
```

### 2. Template Switching
```
User clicks dashboard card
    ↓
switchTemplate(templateId) called
    ↓
Store selection in localStorage
    ↓
Fetch template config.yaml
    ↓
Parse YAML configuration
    ↓
Update component state
    ↓
Re-render with new configuration
```

### 3. Widget Data Loading
```
Dashboard renders
    ↓
Widgets read configuration
    ↓
Apply dashboard-specific filters
    ↓
Fetch data from APIs (GitHub, Catalog)
    ↓
Display filtered/relevant data
```

## 🗂️ Configuration Schema

### Registry Structure
```yaml
apiVersion: backstage.io/v1
kind: DashboardRegistry
metadata:
  name: ba-dashboards
  version: "1.0.0"

templates:
  - id: string                    # Unique identifier
    name: string                  # Display name
    description: string           # Description text
    category: string              # Category for grouping
    configPath: string            # Path to config.yaml
    icon: string                  # Emoji or icon
    target: string                # Target audience
    isDefault?: boolean           # Default selection
    tags?: string[]               # Filtering tags
```

### Dashboard Configuration Schema
```yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: string                    # Dashboard ID
  title: string                   # Display title
  subtitle: string                # Subtitle text
  version: string                 # Version number
  author?: string                 # Author info

spec:
  widgets:
    github?:
      enabled: boolean
      refreshInterval: number
      filters:
        keywords: string[]
        topics: string[]
        exclude: string[]
        
    catalog?:
      enabled: boolean
      refreshInterval: number
      maxServices: number
      
    worldClock?:
      enabled: boolean
      timezones:
        - name: string
          timezone: string
          flag: string
          
  layout:
    grid:
      columns: number
      spacing: number
      
  theme:
    primaryColor: string
    secondaryColor: string
    backgroundColor: string
```

## 🔧 Extension Points

### Adding New Widget Types

1. **Create Widget Component**:
```typescript
// components/widgets/MyWidget.tsx
export const MyWidget = () => {
  const { config } = useDashboardConfig();
  const widgetConfig = config?.spec?.widgets?.myWidget;
  
  if (!widgetConfig?.enabled) return null;
  
  return <InfoCard title="My Widget">...</InfoCard>;
};
```

2. **Update Configuration Schema**:
```yaml
# In config.yaml
spec:
  widgets:
    myWidget:
      enabled: true
      customProperty: "value"
```

3. **Add to HomePage**:
```typescript
// HomePage.tsx
{spec.widgets.myWidget?.enabled && (
  <Grid item xs={12}>
    <MyWidget />
  </Grid>
)}
```

### Custom Dashboard Types

1. **Create Template Directory**:
```bash
mkdir templates/my-custom-dashboard/
```

2. **Create Configuration**:
```yaml
# templates/my-custom-dashboard/config.yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: my-custom-dashboard
  title: "My Custom Dashboard"
```

3. **Register in Registry**:
```yaml
# registry.yaml
templates:
  - id: my-custom
    name: My Custom Dashboard
    configPath: templates/my-custom-dashboard/config.yaml
```

## 🚦 Error Handling

### Graceful Degradation
- **Network Failures**: Use cached configurations
- **Parse Errors**: Fall back to minimal configuration
- **Missing Files**: Display error message with recovery options
- **API Limits**: Implement retry logic with exponential backoff

### Error Boundaries
```typescript
// Error boundary for widget failures
const ErrorFallback = ({ error, resetErrorBoundary }) => (
  <InfoCard title="Widget Error">
    <Typography>Failed to load widget: {error.message}</Typography>
    <Button onClick={resetErrorBoundary}>Retry</Button>
  </InfoCard>
);
```

## 📈 Performance Considerations

### Caching Strategy
- **Registry Cache**: 5 minutes TTL
- **Config Cache**: 5 minutes TTL  
- **API Cache**: Implement per-widget
- **Local Storage**: User preferences

### Optimization Techniques
- **Lazy Loading**: Load widgets on demand
- **Memoization**: Cache expensive computations
- **Bundle Splitting**: Separate widget bundles
- **Virtual Scrolling**: For large lists

## 🔐 Security Considerations

### Data Access
- **Public APIs Only**: No authentication tokens in configuration
- **CORS Handling**: Proper cross-origin resource sharing
- **Input Validation**: Sanitize all YAML inputs
- **Rate Limiting**: Respect API limits

### Configuration Validation
```typescript
const validateConfig = (config: unknown): DashboardConfig => {
  // JSON Schema validation
  // Type checking
  // Sanitization
  return validatedConfig;
};
```

## 📊 Monitoring and Observability

### Key Metrics
- **Dashboard Load Time**: Time to render
- **API Response Time**: External service performance
- **Error Rate**: Failed requests/total requests
- **User Engagement**: Dashboard usage patterns

### Logging Strategy
```typescript
// Structured logging
console.log('📋 Registry loaded', { 
  templates: templates.length, 
  timestamp: Date.now() 
});
```

---

**🏗️ Architecture designed for scalability, maintainability, and extensibility**