# 🎯 Backstage Dynamic Dashboard System

**British Airways Digital Operations - Multiple Dashboard Configuration System**

[![Status](https://img.shields.io/badge/Status-Active-green)](https://github.com/Portfolio-jaime/backstage-dashboard-templates)
[![Version](https://img.shields.io/badge/Version-2.0.0-blue)](https://github.com/Portfolio-jaime/backstage-dashboard-templates)
[![Dashboards](https://img.shields.io/badge/Dashboards-5-orange)](https://github.com/Portfolio-jaime/backstage-dashboard-templates)

## 🏗️ Architecture Overview

This repository provides dynamic dashboard configurations for Backstage, enabling specialized dashboards for different teams and use cases. The system loads configurations from GitHub and dynamically renders content based on YAML definitions.

```
┌─────────────────────────────────────────────────────────────┐
│                    BACKSTAGE APPLICATION                    │
├─────────────────────────────────────────────────────────────┤
│  HomePage.tsx → useDashboardConfig → GitHub Raw Files      │
│                                                             │
│  ┌─────────────┐    ┌──────────────┐    ┌─────────────────┐ │
│  │ Dashboard   │    │ Widget       │    │ Navigation      │ │
│  │ Selector    │    │ Components   │    │ Cards           │ │
│  └─────────────┘    └──────────────┘    └─────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                               │
                               ▼
┌─────────────────────────────────────────────────────────────┐
│           GITHUB CONFIGURATION REPOSITORY                  │
├─────────────────────────────────────────────────────────────┤
│  registry.yaml              │  templates/                  │
│  └── Dashboard Registry     │  ├── ba-main-dashboard/      │
│                              │  ├── ba-devops-dashboard/   │
│                              │  ├── ba-platform-dashboard/ │
│                              │  ├── ba-security-dashboard/ │
│                              │  └── ba-developer-dashboard/│
└─────────────────────────────────────────────────────────────┘
```

## 🚀 Quick Start

### 1. Available Dashboards

| Dashboard | Purpose | Target Audience | Features |
|-----------|---------|----------------|----------|
| 🏠 **BA Main** | Overview & Navigation | All Users | Global operations, navigation hub |
| 🚀 **DevOps** | CI/CD & Deployments | DevOps Engineers | GitHub repos, deployment tracking |
| ⚙️ **Platform** | Infrastructure | Platform Engineers | Infrastructure monitoring, services |
| 🔒 **Security** | Security & Compliance | Security Team | Security alerts, compliance status |
| 💻 **Developer** | Developer Tools | Developers | Development tools, productivity |

### 2. Configuration Structure

```
backstage-dashboard-templates/
├── registry.yaml                    # Dashboard registry
├── templates/
│   ├── ba-main-dashboard/
│   │   └── config.yaml             # Main dashboard config
│   ├── ba-devops-dashboard/
│   │   └── config.yaml             # DevOps dashboard config
│   └── ...
├── docs/                           # Documentation
└── examples/                       # Template examples
```

### 3. How It Works

1. **Registry Discovery**: Backstage loads `registry.yaml` to discover available dashboards
2. **Configuration Loading**: Based on user selection, loads specific dashboard YAML config
3. **Dynamic Rendering**: Renders widgets and content based on configuration
4. **Auto-Refresh**: Configurations are refreshed every 5 minutes from GitHub

## 📋 Dashboard Features

### 🎛️ Widget System
- **GitHub Activity**: Dynamic repository filtering based on dashboard type
- **Service Catalog**: Live Backstage catalog integration
- **World Clock**: Global timezone support for operations
- **System Health**: Real-time monitoring (when APIs available)
- **Quick Actions**: Dashboard-specific action shortcuts

### 🎨 Theming
- **Material-UI Integration**: Full theme support including dark mode
- **Customizable Colors**: Per-dashboard color schemes
- **Responsive Layout**: Mobile and desktop optimized
- **British Airways Branding**: Consistent BA visual identity

### 🔄 Real-Time Updates
- **GitHub Integration**: Live repository data via GitHub API
- **Catalog Sync**: Real-time service catalog updates
- **Configuration Refresh**: Auto-refresh every 5 minutes
- **Error Handling**: Graceful fallbacks for network issues

## 📚 Documentation

| Guide | Description | Audience |
|-------|-------------|----------|
| [📖 Complete Setup Guide](docs/SETUP_GUIDE.md) | Step-by-step implementation | Developers |
| [🏗️ Architecture Guide](docs/ARCHITECTURE.md) | System design & components | Architects |
| [⚙️ Configuration Reference](docs/CONFIGURATION.md) | YAML configuration options | Administrators |
| [🔧 Development Guide](docs/DEVELOPMENT.md) | Creating custom dashboards | Developers |
| [🚨 Troubleshooting](docs/TROUBLESHOOTING.md) | Common issues & solutions | Support |
| [🐛 Debug Guide](docs/DEBUG.md) | Debug procedures & tools | Developers |

## 🛠️ Quick Configuration

### Adding a New Dashboard

1. **Create Configuration**:
```yaml
# templates/my-dashboard/config.yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: my-dashboard
  title: "My Custom Dashboard"
  subtitle: "Description of purpose"
  version: "1.0.0"

spec:
  widgets:
    github:
      enabled: true
      filters:
        keywords: ["my-keyword"]
    catalog:
      enabled: true
      maxServices: 8
```

2. **Register Dashboard**:
```yaml
# registry.yaml
templates:
  - id: my-dashboard
    name: My Custom Dashboard
    description: Dashboard description
    category: Custom
    configPath: templates/my-dashboard/config.yaml
    icon: "🎯"
    target: custom
```

3. **Deploy**: Push to GitHub - changes are live in 5 minutes!

## 📊 System Status

- ✅ **Registry System**: Operational
- ✅ **Main Dashboard**: Active
- ✅ **DevOps Dashboard**: Active  
- 🚧 **Platform Dashboard**: In Development
- 🚧 **Security Dashboard**: In Development
- 🚧 **Developer Dashboard**: In Development

## 🔄 Data Flow

### Repository Separation
- **`backstage-app-devc`** - Main Backstage application
- **`backstage-dashboard-templates`** - Dashboard configurations (source of truth)

### Data Flow Process
```
GitHub Repo (backstage-dashboard-templates) 
    ↓ (fetch every 5 min)
Hook (useDashboardConfig.ts) 
    ↓ (parse YAML)
Dashboard Selector 
    ↓ (switch template)
HomePage + Widgets
```

## 🎯 Current Dashboards

### 🚀 BA DevOps Dashboard
- **Purpose**: Operations and deployments
- **Repositories**: backstage, devops, k8s, terraform, gitops
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Target Users**: DevOps Teams

### ⚙️ BA Platform Engineering
- **Purpose**: Infrastructure and platform
- **Repositories**: kubernetes, infrastructure, helm, cluster
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Target Users**: Platform Engineers

### 🔒 BA Security Dashboard
- **Purpose**: Security and compliance
- **Repositories**: security, compliance, audit, vulnerability
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Target Users**: Security Team

### 📊 BA Executive Dashboard
- **Purpose**: Executive metrics
- **Repositories**: strategic, executive, reports
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Target Users**: Executive Leadership

### 💻 BA Developer Dashboard
- **Purpose**: Development tools
- **Repositories**: api, microservice, tools, templates
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Target Users**: Developers

## 📈 Roadmap

### 🔮 Upcoming Features:
- [ ] Integration with real BA APIs
- [ ] Application performance metrics
- [ ] User-customizable dashboards
- [ ] Push notifications
- [ ] Report exports
- [ ] Slack/Teams integration
- [ ] Real cost metrics
- [ ] Automated health checks

### 🛠️ Technical Improvements:
- [ ] Intelligent data caching
- [ ] Performance optimization
- [ ] Automated tests
- [ ] CI/CD for configurations
- [ ] Automatic YAML validation
- [ ] Uptime monitoring

## 🔗 Related Resources

- [Backstage Documentation](https://backstage.io/docs/)
- [Material-UI Theming](https://mui.com/material-ui/customization/theming/)
- [GitHub API Reference](https://docs.github.com/en/rest)

## 📞 Support

- **Team**: British Airways Digital Operations
- **Maintainer**: Jaime Henao <jaime.andres.henao.arbelaez@ba.com>
- **Repository**: [backstage-dashboard-templates](https://github.com/Portfolio-jaime/backstage-dashboard-templates)
- **Issues**: [GitHub Issues](https://github.com/Portfolio-jaime/backstage-dashboard-templates/issues)

---

**📅 Last Updated**: September 11, 2025  
**📋 Version**: 2.0.0  
**🎯 Status**: Production Stable

**🔄 Auto-refreshes from GitHub every 5 minutes** | **✈️ Ready for takeoff!**