# ⚙️ Configuration Reference

Complete reference for configuring the Backstage Dynamic Dashboard System.

## 📋 Registry Configuration (`registry.yaml`)

The registry file defines all available dashboards and their metadata.

### Schema
```yaml
apiVersion: backstage.io/v1
kind: DashboardRegistry
metadata:
  name: string                    # Registry identifier
  version: string                 # Registry version
  description?: string            # Registry description
  lastUpdated: string            # ISO 8601 timestamp

templates:
  - id: string                    # Unique dashboard ID
    name: string                  # Display name
    description: string           # Description text
    category: string              # Grouping category
    configPath: string            # Path to config.yaml
    icon: string                  # Display icon (emoji)
    target: string                # Target audience
    isDefault?: boolean           # Default selection
    tags?: string[]               # Filter tags
    priority?: number             # Display order
    status?: string               # development|production|deprecated
```

### Example
```yaml
apiVersion: backstage.io/v1
kind: DashboardRegistry
metadata:
  name: ba-dashboards
  version: "2.0.0"
  description: "British Airways dashboard templates"
  lastUpdated: "2025-01-15T10:30:00Z"

templates:
  - id: ba-main
    name: BA Operations Overview
    description: Central hub with general information and navigation to specialized dashboards
    category: Overview
    configPath: templates/ba-main-dashboard/config.yaml
    icon: "🏠"
    target: main
    isDefault: true
    status: production
    
  - id: ba-devops
    name: BA DevOps Dashboard
    description: Operations monitoring and deployment status
    category: Operations
    configPath: templates/ba-devops-dashboard/config.yaml
    icon: "🚀"
    target: devops
    tags: ["devops", "deployment", "ci-cd"]
    status: production
```

## 🎛️ Dashboard Configuration (`config.yaml`)

Individual dashboard configurations define widgets, layout, and behavior.

### Schema
```yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: string                    # Dashboard ID
  title: string                   # Display title
  subtitle: string                # Subtitle text
  version: string                 # Version number
  author?: string                 # Author information
  namespace?: string              # Namespace grouping
  annotations?: object            # Additional metadata

content?:
  welcome?:
    title?: string                # Welcome card title
    message?: string              # Welcome message (multiline)
  quickActions?: QuickAction[]    # Quick action links

spec:
  widgets: WidgetConfig           # Widget configurations
  layout: LayoutConfig            # Grid layout settings
  theme: ThemeConfig              # Visual theming
  branding?: BrandingConfig       # Company branding
  notifications?: NotificationConfig # Alert settings
```

### Widget Configurations

#### GitHub Widget
```yaml
github:
  enabled: boolean                # Enable/disable widget
  refreshInterval: number         # Refresh rate (milliseconds)
  dynamicRepos: boolean           # Auto-discover repositories
  owner: string                   # GitHub username/org
  maxEvents: number               # Max events to display
  
  filters:
    keywords: string[]            # Repository name filters
    topics: string[]              # Repository topic filters
    exclude: string[]             # Exclusion patterns
    sortBy: string               # "updated" | "created" | "stars"
    maxRepos: number              # Max repositories to load
    
  apiConfig?:
    usePublicApi: boolean         # Use public GitHub API
    rateLimit: number             # Rate limit threshold
    timeout: number               # Request timeout (ms)
```

#### Service Catalog Widget
```yaml
catalog:
  enabled: boolean                # Enable/disable widget
  refreshInterval: number         # Refresh rate (milliseconds)
  maxServices: number             # Max services to display
  title?: string                  # Custom widget title
  
  filters:
    kinds: string[]               # Entity kinds to include
    types?: string[]              # Entity types filter
    tags?: string[]               # Tag filters
    labels?: object               # Label selectors
    
  displayOptions:
    showOverview: boolean         # Show summary stats
    showMetrics: boolean          # Show health metrics
    showHealthStatus: boolean     # Show status indicators
    showByCategory: boolean       # Group by category
    showUptime?: boolean          # Display uptime info
    showResponseTime?: boolean    # Display response times
```

#### World Clock Widget
```yaml
worldClock:
  enabled: boolean                # Enable/disable widget
  refreshInterval: number         # Update frequency (milliseconds)
  title?: string                  # Custom widget title
  
  timezones:
    - name: string                # Display name
      timezone: string            # IANA timezone identifier
      flag: string                # Flag emoji
      primary?: boolean           # Highlight primary timezone
      
  displayOptions?:
    format24Hour: boolean         # Use 24-hour format
    showSeconds: boolean          # Show seconds
    showDate: boolean             # Show date
    showTimezone: boolean         # Show timezone abbreviation
```

#### System Health Widget
```yaml
systemHealth:
  enabled: boolean                # Enable/disable widget
  refreshInterval: number         # Refresh rate (milliseconds)
  title?: string                  # Custom widget title
  showOverview: boolean           # Show summary metrics
  
  # Note: Currently displays placeholder when no APIs available
  reason?: string                 # Reason for disabling
```

#### Flight Operations Widget
```yaml
flightOps:
  enabled: boolean                # Enable/disable widget
  reason?: string                 # Reason for disabling (when false)
  
  # Note: Disabled by default due to lack of real API
  # Future implementation would include:
  # refreshInterval: number
  # apiEndpoint: string
  # filters: object
```

#### Cost Dashboard Widget
```yaml
costDashboard:
  enabled: boolean                # Enable/disable widget
  reason?: string                 # Reason for disabling (when false)
  
  # Note: Disabled by default due to lack of real API
  # Future implementation would include:
  # refreshInterval: number
  # budgetThresholds: object
  # costCategories: string[]
```

#### Security Alerts Widget
```yaml
security:
  enabled: boolean                # Enable/disable widget
  reason?: string                 # Reason for disabling (when false)
  
  # Note: Disabled by default due to lack of real API
  # Future implementation would include:
  # refreshInterval: number
  # severityLevels: string[]
  # alertTypes: string[]
```

### Layout Configuration
```yaml
layout:
  grid:
    columns: number               # Grid columns (typically 12)
    spacing: number               # Grid spacing (1-10)
    responsive?: boolean          # Enable responsive breakpoints
    
  positions?:
    search?: GridPosition         # Search bar position
    welcome?: GridPosition        # Welcome card position
    worldClock?: GridPosition     # World clock position
    # ... other widget positions
    
# Grid Position Schema
GridPosition:
  xs: number                      # Extra small screens
  sm?: number                     # Small screens
  md?: number                     # Medium screens
  lg?: number                     # Large screens
  xl?: number                     # Extra large screens
  priority?: number               # Render priority
```

### Theme Configuration
```yaml
theme:
  primaryColor: string            # Primary color (hex)
  secondaryColor: string          # Secondary color (hex)
  backgroundColor: string         # Background color (hex)
  surfaceColor?: string           # Card background color
  errorColor?: string             # Error state color
  warningColor?: string           # Warning state color
  successColor?: string           # Success state color
  cardElevation: number           # Material-UI elevation (0-24)
  borderRadius: number            # Border radius (pixels)
  fontFamily?: string             # Font family name
```

### Branding Configuration
```yaml
branding:
  companyName: string             # Company name
  companyLogo: string             # Logo path/URL
  motto?: string                  # Company motto
  
  colors?:
    primary: string               # Brand primary color
    accent: string                # Brand accent color
    
  links?:
    website?: string              # Company website
    support?: string              # Support link
    documentation?: string        # Documentation link
```

### Content Configuration
```yaml
content:
  welcome?:
    title: string                 # Welcome section title
    message: string               # Welcome message (supports multiline)
    
  quickActions?:
    - title: string               # Action title
      icon: string                # Icon (emoji or Material-UI icon)
      url: string                 # Target URL
      description?: string        # Action description
      external?: boolean          # Open in new tab
```

### Notification Configuration
```yaml
notifications:
  enabled: boolean                # Enable notifications
  
  channels:
    slack?:
      webhook: string             # Slack webhook URL
      channel: string             # Target channel
      username?: string           # Bot username
      
    email?:
      smtp: string                # SMTP server
      recipients: string[]        # Email addresses
      from?: string               # From address
      
  alertThresholds?:
    systemHealth: number          # Health threshold (0-100)
    costBudget: number            # Cost threshold (0-100)
    securityAlerts: number        # Alert count threshold
    deploymentFailure: number     # Failure threshold
    
  alertTypes:
    - critical_system
    - security_alert
    - cost_anomaly
    - deployment_failure
```

## 🔧 Widget-Specific Configurations

### GitHub Repository Filters

#### By Keywords
Match repository names containing specified keywords:
```yaml
filters:
  keywords: 
    - "backstage"
    - "k8s"
    - "terraform"
    - "api"
    - "microservice"
```

#### By Topics
Match repositories with specific GitHub topics:
```yaml
filters:
  topics:
    - "kubernetes"
    - "devops" 
    - "microservices"
    - "infrastructure"
    - "monitoring"
```

#### Exclusion Patterns
Exclude repositories matching patterns:
```yaml
filters:
  exclude:
    - "archived"
    - "private"
    - "fork"
    - "test"
    - "demo"
    - "deprecated"
```

#### Sorting Options
```yaml
filters:
  sortBy: "updated"      # "updated", "created", "stars", "name"
  maxRepos: 8            # Limit number of repositories
```

### Timezone Configuration

#### Comprehensive Global Coverage
```yaml
worldClock:
  timezones:
    # Europe
    - name: "London HQ"
      timezone: "Europe/London"
      flag: "🇬🇧"
      primary: true
    - name: "Madrid Hub"
      timezone: "Europe/Madrid"
      flag: "🇪🇸"
    - name: "Amsterdam"
      timezone: "Europe/Amsterdam"
      flag: "🇳🇱"
    
    # Americas
    - name: "New York"
      timezone: "America/New_York"
      flag: "🇺🇸"
    - name: "Los Angeles"
      timezone: "America/Los_Angeles"  
      flag: "🇺🇸"
    - name: "São Paulo"
      timezone: "America/Sao_Paulo"
      flag: "🇧🇷"
    
    # Asia Pacific
    - name: "Singapore"
      timezone: "Asia/Singapore"
      flag: "🇸🇬"
    - name: "Tokyo"
      timezone: "Asia/Tokyo"
      flag: "🇯🇵"
    - name: "Sydney"
      timezone: "Australia/Sydney"
      flag: "🇦🇺"
```

## 📋 Complete Example Configurations

### DevOps Dashboard Example
```yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: ba-devops-dashboard
  title: "BA DevOps Command Center"
  subtitle: "Development operations and deployment monitoring"
  version: "1.0.0"
  author: "DevOps Team <devops@ba.com>"
  
spec:
  widgets:
    github:
      enabled: true
      refreshInterval: 300000
      dynamicRepos: true
      owner: "Portfolio-jaime"
      filters:
        keywords: ["backstage", "devops", "k8s", "terraform", "gitops"]
        topics: ["backstage", "devops", "kubernetes", "terraform"]
        exclude: ["archived", "private"]
        sortBy: "updated"
        maxRepos: 8
      maxEvents: 8
      
    catalog:
      enabled: true
      refreshInterval: 120000
      maxServices: 8
      filters:
        kinds: ["Component", "API", "Resource"]
      displayOptions:
        showHealthMetrics: true
        showUptime: true
        showResponseTime: true
        
    worldClock:
      enabled: true
      refreshInterval: 1000
      timezones:
        - name: "London"
          timezone: "Europe/London"
          flag: "🇬🇧"
          primary: true
        - name: "New York"
          timezone: "America/New_York"
          flag: "🇺🇸"
          
    # Disabled widgets
    flightOps:
      enabled: false
      reason: "No real API available"
    costDashboard:
      enabled: false
      reason: "Use Management dashboard"
    security:
      enabled: false
      reason: "Use dedicated Security dashboard"
    systemHealth:
      enabled: false
      reason: "No real API available"
      
  layout:
    grid:
      columns: 12
      spacing: 3
      responsive: true
      
  theme:
    primaryColor: "#1976d2"
    secondaryColor: "#ff9800"
    backgroundColor: "#f5f5f5"
    cardElevation: 2
    borderRadius: 8
    
  branding:
    companyName: "British Airways"
    companyLogo: "/static/ba-logo.png"
    motto: "To fly. To serve. To deploy."
    
  content:
    welcome:
      title: "Welcome to BA DevOps Command Center!"
      message: |
        🚀 Your mission control for development operations and deployments.
        
        Monitor CI/CD pipelines, track deployments, and ensure high availability
        across all BA digital infrastructure environments.
        
    quickActions:
      - title: "Deploy Service"
        icon: "🚀"
        url: "/create"
        description: "Deploy new service"
      - title: "Monitoring"
        icon: "📈"
        url: "/catalog?filters=tag:monitoring"
        description: "System monitoring"
```

## ⚠️ Configuration Best Practices

### Security
- ❌ Never include API tokens or secrets in configurations
- ✅ Use public APIs or environment variables for sensitive data
- ✅ Validate all external URLs and inputs
- ✅ Sanitize user-provided content

### Performance  
- ✅ Set appropriate refresh intervals (not too frequent)
- ✅ Limit the number of repositories and services displayed
- ✅ Use caching strategies for expensive operations
- ✅ Implement error boundaries for widget failures

### Maintainability
- ✅ Use descriptive names and documentation
- ✅ Version your configurations
- ✅ Include author information and change reasons
- ✅ Follow consistent naming conventions
- ✅ Test configurations before deploying

### User Experience
- ✅ Provide meaningful error messages
- ✅ Use appropriate loading states
- ✅ Ensure mobile responsiveness
- ✅ Test in both light and dark themes
- ✅ Include helpful descriptions and tooltips

---

**⚙️ Configuration drives behavior - design with intention**