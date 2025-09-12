# 🔧 Development Guide

Complete guide for developers working with the Backstage Dynamic Dashboard System.

## 🚀 Getting Started

### Prerequisites
- **Node.js** 16+ with yarn
- **Git** for version control  
- **Backstage** application running
- **Code editor** with YAML support
- **yamllint** for YAML validation

### Development Environment Setup

#### 1. Clone Repositories
```bash
# Clone the dashboard templates repository
git clone https://github.com/Portfolio-jaime/backstage-dashboard-templates.git
cd backstage-dashboard-templates

# Clone the Backstage application (if needed)
git clone https://github.com/Portfolio-jaime/backstage-app-devc.git
cd backstage-app-devc/backstage
```

#### 2. Install Development Tools
```bash
# Install yamllint for YAML validation
pip install yamllint

# Install jq for JSON processing
brew install jq  # macOS
sudo apt-get install jq  # Ubuntu

# Install VS Code YAML extension (if using VS Code)
code --install-extension redhat.vscode-yaml
```

#### 3. Configure YAML Schema Validation
Create `.vscode/settings.json` in the templates repository:
```json
{
  "yaml.schemas": {
    "./schemas/dashboard-registry.schema.json": "registry.yaml",
    "./schemas/dashboard-config.schema.json": "templates/*/config.yaml"
  },
  "yaml.format.enable": true,
  "yaml.validate": true
}
```

## 📁 Project Structure

### Repository Organization
```
backstage-dashboard-templates/
├── .github/                        # GitHub workflows
│   └── workflows/
│       ├── validate-configs.yml    # YAML validation
│       └── deploy-docs.yml         # Documentation deployment
├── .vscode/                        # VS Code configuration
│   └── settings.json              # YAML schema settings
├── docs/                          # Documentation
│   ├── SETUP_GUIDE.md
│   ├── ARCHITECTURE.md
│   ├── CONFIGURATION.md
│   ├── DEVELOPMENT.md (this file)
│   ├── TROUBLESHOOTING.md
│   └── DEBUG.md
├── examples/                      # Template examples
│   ├── minimal-dashboard/
│   ├── advanced-dashboard/
│   └── custom-widgets/
├── schemas/                       # JSON schemas for validation
│   ├── dashboard-registry.schema.json
│   └── dashboard-config.schema.json
├── scripts/                       # Development scripts
│   ├── validate-all.sh           # Validate all configurations
│   ├── create-dashboard.sh       # New dashboard template
│   └── test-configs.sh           # Test configuration loading
├── templates/                     # Dashboard configurations
│   ├── ba-main-dashboard/
│   │   └── config.yaml
│   ├── ba-devops-dashboard/
│   │   └── config.yaml
│   └── ...
├── registry.yaml                  # Master dashboard registry
├── README.md                      # Main documentation
└── CHANGELOG.md                   # Version history
```

## 🆕 Creating New Dashboards

### Step-by-Step Process

#### 1. Use the Dashboard Creation Script
```bash
# Create new dashboard using the script
./scripts/create-dashboard.sh my-new-dashboard "My New Dashboard" "Description"

# Or manually create the structure
mkdir -p templates/ba-mynew-dashboard
cd templates/ba-mynew-dashboard
```

#### 2. Create Configuration File
```yaml
# templates/ba-mynew-dashboard/config.yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: ba-mynew-dashboard
  title: "My New Dashboard"
  subtitle: "Custom dashboard for specific use case"
  version: "1.0.0"
  author: "Your Name <your.email@ba.com>"
  namespace: ba-mynew
  annotations:
    backstage.io/managed-by-location: url:https://github.com/Portfolio-jaime/backstage-dashboard-templates/blob/main/templates/ba-mynew-dashboard/config.yaml
    backstage.io/created-by: your.email@ba.com
    backstage.io/last-updated: "2025-01-15T10:30:00Z"

spec:
  widgets:
    # Enable real data widgets only
    github:
      enabled: true
      refreshInterval: 300000  # 5 minutes
      dynamicRepos: true
      owner: "Portfolio-jaime"
      filters:
        keywords: ["mynew", "specific", "keywords"]
        topics: ["mynew-topic", "category"]
        exclude: ["archived", "private", "fork"]
        sortBy: "updated"
        maxRepos: 8
      maxEvents: 10
      apiConfig:
        usePublicApi: true
        rateLimit: 5000

    catalog:
      enabled: true
      refreshInterval: 120000  # 2 minutes
      maxServices: 10
      title: "My Services"
      filters:
        kinds: ["Component", "API", "Resource"]
        types: ["service", "library", "tool"]
        tags: ["mynew", "production"]
      displayOptions:
        showOverview: true
        showMetrics: true
        showHealthStatus: true

    worldClock:
      enabled: true
      refreshInterval: 1000
      title: "My Locations"
      timezones:
        - name: "Primary Location"
          timezone: "Europe/London"
          flag: "🇬🇧"
          primary: true
        - name: "Secondary Location"
          timezone: "America/New_York"
          flag: "🇺🇸"
      displayOptions:
        format24Hour: false
        showSeconds: false
        showDate: true

    # Disable simulated widgets
    flightOps:
      enabled: false
      reason: "No real flight operations API available for this dashboard"
    costDashboard:
      enabled: false
      reason: "Cost tracking not applicable for this use case"
    security:
      enabled: false
      reason: "Security monitoring handled by dedicated dashboard"
    systemHealth:
      enabled: false
      reason: "System health not applicable for this dashboard type"

  layout:
    grid:
      columns: 12
      spacing: 3
      responsive: true
    positions:
      search:
        xs: 12
        priority: 1
      welcome:
        xs: 12
        md: 8
        priority: 2
      worldClock:
        xs: 12
        md: 4
        priority: 3
      github:
        xs: 12
        lg: 8
        priority: 4
      catalog:
        xs: 12
        lg: 4
        priority: 5

  theme:
    primaryColor: "#1976d2"      # Customize primary color
    secondaryColor: "#ff9800"    # Customize secondary color
    backgroundColor: "#f5f5f5"
    surfaceColor: "#ffffff"
    errorColor: "#f44336"
    warningColor: "#ff9800"
    successColor: "#4caf50"
    cardElevation: 2
    borderRadius: 8
    fontFamily: "Roboto, sans-serif"

  branding:
    companyName: "British Airways"
    companyLogo: "/static/ba-logo.png"
    motto: "Custom motto for this dashboard"
    colors:
      primary: "#1976d2"
      accent: "#ff9800"

  content:
    welcome:
      title: "Welcome to My New Dashboard!"
      message: |
        🎯 Welcome to My New Dashboard!
        
        This dashboard is specifically designed for [describe purpose].
        
        🔧 KEY FEATURES
        • [Feature 1 description]
        • [Feature 2 description]
        • [Feature 3 description]
        
        📊 DASHBOARD OVERVIEW
        • GitHub repositories filtered for [specific criteria]
        • Service catalog showing [specific services]
        • Global time coordination for [specific locations]
        
        🚀 GETTING STARTED
        [Instructions for users of this dashboard]
        
        ---
        "Custom tagline for this dashboard" - Dashboard Team

    quickActions:
      - title: "Custom Action 1"
        icon: "🎯"
        url: "/custom-url-1"
        description: "Description of action 1"
      - title: "Custom Action 2"
        icon: "⚡"
        url: "/custom-url-2"
        description: "Description of action 2"
      - title: "Documentation"
        icon: "📚"
        url: "/docs/mynew"
        description: "Dashboard-specific docs"

  notifications:
    enabled: true
    channels:
      slack:
        webhook: "https://hooks.slack.com/services/YOUR/WEBHOOK/HERE"
        channel: "#mynew-dashboard-alerts"
      email:
        smtp: "smtp.ba.com"
        recipients: ["mynew-team@ba.com"]
    alertThresholds:
      systemHealth: 85
      securityAlerts: 1
```

#### 3. Register in Registry
```yaml
# Add to registry.yaml
  - id: ba-mynew
    name: My New Dashboard
    description: Custom dashboard for specific use case
    category: Custom
    configPath: templates/ba-mynew-dashboard/config.yaml
    icon: "🎯"
    target: mynew
    tags:
      - custom
      - mynew
      - specialized
    priority: 100
    status: development
```

#### 4. Validate Configuration
```bash
# Validate YAML syntax
yamllint templates/ba-mynew-dashboard/config.yaml
yamllint registry.yaml

# Test configuration loading
curl -s "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/templates/ba-mynew-dashboard/config.yaml"

# Validate with schema (if available)
# ajv validate -s schemas/dashboard-config.schema.json -d templates/ba-mynew-dashboard/config.yaml
```

#### 5. Test Dashboard
```bash
# Commit and push changes
git add .
git commit -m "feat: add mynew dashboard

- Add ba-mynew-dashboard configuration
- Focus on [specific purpose]
- Configured for [target audience]
- Added custom quick actions and branding

Author: Your Name <your.email@ba.com>"
git push

# Wait for changes to propagate (5 minutes)
# Test in Backstage application
```

## 🎛️ Advanced Widget Development

### Creating Custom Widget Types

#### 1. Define Widget Interface
```typescript
// types/CustomWidget.ts
export interface CustomWidgetConfig {
  enabled: boolean;
  refreshInterval?: number;
  title?: string;
  customProperty?: string;
  apiEndpoint?: string;
  displayOptions?: {
    showSummary?: boolean;
    maxItems?: number;
  };
}
```

#### 2. Implement Widget Component
```typescript
// widgets/CustomWidget.tsx
import React, { useState, useEffect } from 'react';
import { InfoCard } from '@backstage/core-components';
import { Typography, Box, CircularProgress } from '@material-ui/core';
import { useDashboardConfig } from '../../hooks/useDashboardConfig';

export const CustomWidget = () => {
  const { config } = useDashboardConfig();
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(false);
  const [error, setError] = useState(null);

  const widgetConfig = config?.spec?.widgets?.customWidget;

  // Don't render if disabled
  if (!widgetConfig?.enabled) {
    return null;
  }

  useEffect(() => {
    const fetchData = async () => {
      if (!widgetConfig.apiEndpoint) return;

      setLoading(true);
      try {
        const response = await fetch(widgetConfig.apiEndpoint);
        const result = await response.json();
        setData(result);
        setError(null);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();

    // Set up refresh interval
    const interval = setInterval(fetchData, widgetConfig.refreshInterval || 300000);
    return () => clearInterval(interval);
  }, [widgetConfig]);

  if (loading) {
    return (
      <InfoCard title={widgetConfig.title || "Custom Widget"}>
        <Box display="flex" justifyContent="center" alignItems="center" height={200}>
          <CircularProgress />
          <Typography variant="body2" style={{ marginLeft: 16 }}>
            Loading custom data...
          </Typography>
        </Box>
      </InfoCard>
    );
  }

  if (error) {
    return (
      <InfoCard title={widgetConfig.title || "Custom Widget"}>
        <Box p={2} textAlign="center">
          <Typography color="error">
            Failed to load data: {error}
          </Typography>
        </Box>
      </InfoCard>
    );
  }

  return (
    <InfoCard title={widgetConfig.title || "Custom Widget"}>
      <Box p={2}>
        <Typography variant="h6">{widgetConfig.customProperty}</Typography>
        {/* Render your custom data here */}
        <pre>{JSON.stringify(data, null, 2)}</pre>
      </Box>
    </InfoCard>
  );
};
```

#### 3. Register Widget in HomePage
```typescript
// components/home/HomePage.tsx
import { CustomWidget } from './widgets/CustomWidget';

// Add to render method
{spec.widgets.customWidget?.enabled && (
  <Grid item xs={12} md={6}>
    <CustomWidget />
  </Grid>
)}
```

#### 4. Add to Configuration Schema
```yaml
# In dashboard config.yaml
spec:
  widgets:
    customWidget:
      enabled: true
      refreshInterval: 300000
      title: "My Custom Widget"
      customProperty: "Custom Value"
      apiEndpoint: "https://api.example.com/data"
      displayOptions:
        showSummary: true
        maxItems: 10
```

### Extending Existing Widgets

#### Adding Features to GitHub Widget
```typescript
// Extend TeamActivity widget with additional filters
const loadDynamicRepos = async () => {
  const githubConfig = config?.spec?.widgets?.github;
  
  // New filter: language
  const languageFilter = githubConfig?.filters?.languages || [];
  
  const filteredRepos = repos.filter(repo => {
    // Existing filters...
    const matchesKeywords = /* existing logic */;
    const matchesTopics = /* existing logic */;
    
    // New language filter
    const matchesLanguage = languageFilter.length === 0 || 
      languageFilter.includes(repo.language?.toLowerCase());
    
    return matchesKeywords && matchesTopics && matchesLanguage;
  });
  
  return filteredRepos;
};
```

#### Enhanced Configuration
```yaml
github:
  enabled: true
  filters:
    keywords: ["backstage"]
    topics: ["backstage"]
    languages: ["typescript", "javascript", "python"]  # New filter
    exclude: ["archived"]
    sortBy: "updated"
    maxRepos: 8
```

## 🔧 Configuration Management

### Configuration Validation

#### JSON Schema Definition
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Dashboard Configuration",
  "type": "object",
  "required": ["apiVersion", "kind", "metadata", "spec"],
  "properties": {
    "apiVersion": {
      "type": "string",
      "enum": ["backstage.io/v1beta1"]
    },
    "kind": {
      "type": "string",
      "enum": ["DashboardConfig"]
    },
    "metadata": {
      "type": "object",
      "required": ["name", "title", "version"],
      "properties": {
        "name": {"type": "string"},
        "title": {"type": "string"},
        "subtitle": {"type": "string"},
        "version": {"type": "string"}
      }
    },
    "spec": {
      "type": "object",
      "required": ["widgets", "theme"],
      "properties": {
        "widgets": {"$ref": "#/definitions/widgets"},
        "theme": {"$ref": "#/definitions/theme"}
      }
    }
  },
  "definitions": {
    "widgets": {
      "type": "object",
      "properties": {
        "github": {"$ref": "#/definitions/githubWidget"},
        "catalog": {"$ref": "#/definitions/catalogWidget"}
      }
    },
    "githubWidget": {
      "type": "object",
      "properties": {
        "enabled": {"type": "boolean"},
        "refreshInterval": {"type": "number", "minimum": 1000},
        "filters": {
          "type": "object",
          "properties": {
            "keywords": {"type": "array", "items": {"type": "string"}},
            "topics": {"type": "array", "items": {"type": "string"}}
          }
        }
      }
    }
  }
}
```

### Configuration Testing

#### Automated Validation Script
```bash
#!/bin/bash
# scripts/validate-all.sh

echo "🔍 Validating all dashboard configurations..."

# Validate registry
echo "📋 Validating registry.yaml..."
if yamllint registry.yaml; then
    echo "✅ Registry YAML is valid"
else
    echo "❌ Registry YAML has errors"
    exit 1
fi

# Validate all config files
echo "📄 Validating config files..."
for config in templates/*/config.yaml; do
    echo "Validating $config..."
    if yamllint "$config"; then
        echo "✅ $config is valid"
    else
        echo "❌ $config has errors"
        exit 1
    fi
done

# Test URL accessibility
echo "🌐 Testing URL accessibility..."
base_url="https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main"

# Test registry
if curl -s --fail "$base_url/registry.yaml" > /dev/null; then
    echo "✅ Registry is accessible"
else
    echo "❌ Registry is not accessible"
fi

# Test config files
for config in templates/*/config.yaml; do
    url="$base_url/$config"
    if curl -s --fail "$url" > /dev/null; then
        echo "✅ $config is accessible"
    else
        echo "❌ $config is not accessible"
    fi
done

echo "🎉 Validation complete!"
```

#### Configuration Testing Framework
```typescript
// scripts/test-configs.ts
import { DashboardConfig, DashboardTemplate } from '../types';

class ConfigurationTester {
  async testAllConfigurations() {
    console.log('🧪 Testing all dashboard configurations...');
    
    // Load registry
    const registry = await this.loadRegistry();
    console.log(`📋 Found ${registry.templates.length} templates`);
    
    // Test each template
    for (const template of registry.templates) {
      await this.testTemplate(template);
    }
    
    console.log('✅ All configurations tested');
  }
  
  private async loadRegistry() {
    const url = 'https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml';
    const response = await fetch(url);
    const content = await response.text();
    return this.parseRegistry(content);
  }
  
  private async testTemplate(template: DashboardTemplate) {
    console.log(`🔧 Testing template: ${template.name}`);
    
    try {
      const config = await this.loadConfig(template.configPath);
      
      // Validate structure
      this.validateConfigStructure(config);
      
      // Test widget configurations
      this.validateWidgets(config.spec.widgets);
      
      // Test theme configuration
      this.validateTheme(config.spec.theme);
      
      console.log(`✅ ${template.name} is valid`);
    } catch (error) {
      console.error(`❌ ${template.name} failed:`, error.message);
    }
  }
  
  private validateConfigStructure(config: DashboardConfig) {
    const required = ['apiVersion', 'kind', 'metadata', 'spec'];
    for (const field of required) {
      if (!(field in config)) {
        throw new Error(`Missing required field: ${field}`);
      }
    }
  }
  
  private validateWidgets(widgets: any) {
    // Validate enabled widgets have proper configuration
    Object.entries(widgets).forEach(([name, config]: [string, any]) => {
      if (config.enabled && name === 'github') {
        if (!config.owner) {
          throw new Error('GitHub widget requires owner property');
        }
      }
    });
  }
  
  private validateTheme(theme: any) {
    const requiredColors = ['primaryColor', 'secondaryColor'];
    for (const color of requiredColors) {
      if (!theme[color]) {
        throw new Error(`Missing required theme color: ${color}`);
      }
      
      // Validate color format
      if (!/^#[0-9A-Fa-f]{6}$/.test(theme[color])) {
        throw new Error(`Invalid color format: ${theme[color]}`);
      }
    }
  }
}

// Usage
const tester = new ConfigurationTester();
tester.testAllConfigurations();
```

## 🚀 Deployment & CI/CD

### GitHub Actions Workflow

#### Configuration Validation
```yaml
# .github/workflows/validate-configs.yml
name: Validate Dashboard Configurations

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  validate:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
      
    - name: Set up Python
      uses: actions/setup-python@v4
      with:
        python-version: '3.9'
        
    - name: Install yamllint
      run: pip install yamllint
      
    - name: Validate YAML files
      run: |
        yamllint registry.yaml
        find templates -name "*.yaml" -exec yamllint {} \;
        
    - name: Test URL accessibility
      run: |
        # Test if configs are accessible via GitHub raw URLs
        bash scripts/validate-all.sh
        
    - name: Validate JSON Schema (if available)
      run: |
        # npm install -g ajv-cli
        # ajv validate -s schemas/dashboard-config.schema.json -d "templates/*/config.yaml"
        echo "Schema validation placeholder"
```

#### Documentation Deployment
```yaml
# .github/workflows/deploy-docs.yml
name: Deploy Documentation

on:
  push:
    branches: [ main ]
    paths: [ 'docs/**', 'README.md' ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout
      uses: actions/checkout@v3
      
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '16'
        
    - name: Build documentation
      run: |
        # Generate documentation site
        npm install -g @backstage/create-app
        # Custom documentation build process
        
    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./docs-build
```

### Automated Configuration Updates

#### Version Bumping Script
```bash
#!/bin/bash
# scripts/bump-version.sh

dashboard_name=$1
new_version=$2

if [ -z "$dashboard_name" ] || [ -z "$new_version" ]; then
    echo "Usage: $0 <dashboard-name> <new-version>"
    exit 1
fi

config_file="templates/${dashboard_name}/config.yaml"

if [ ! -f "$config_file" ]; then
    echo "Configuration file not found: $config_file"
    exit 1
fi

# Update version in config file
sed -i "s/version: \".*\"/version: \"$new_version\"/" "$config_file"

# Update last-updated annotation
current_time=$(date -u +"%Y-%m-%dT%H:%M:%SZ")
sed -i "s/backstage.io\/last-updated: \".*\"/backstage.io\/last-updated: \"$current_time\"/" "$config_file"

echo "Updated $dashboard_name to version $new_version"
```

## 📋 Best Practices

### Code Quality
- **YAML Formatting**: Use consistent indentation (2 spaces)
- **Naming Conventions**: Use kebab-case for IDs, PascalCase for titles
- **Documentation**: Include comprehensive descriptions and comments
- **Version Control**: Use semantic versioning for dashboard configs

### Performance
- **Refresh Intervals**: Set appropriate intervals (not too frequent)
- **Data Limits**: Limit the number of items displayed
- **Error Handling**: Implement graceful fallbacks
- **Caching**: Use browser caching where appropriate

### Security
- **No Secrets**: Never commit API tokens or secrets
- **URL Validation**: Validate all external URLs
- **Input Sanitization**: Sanitize user-provided content
- **CORS Handling**: Proper cross-origin resource sharing

### Maintainability
- **Modular Design**: Keep widgets independent
- **Configuration-Driven**: Minimize hardcoded values
- **Testing**: Implement comprehensive testing
- **Documentation**: Keep documentation up-to-date

---

**🔧 Happy developing! Build dashboards that empower teams.**