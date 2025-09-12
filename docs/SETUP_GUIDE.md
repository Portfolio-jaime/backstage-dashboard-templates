# 📖 Complete Setup Guide

This guide walks you through setting up the Backstage Dynamic Dashboard System from scratch.

## 🚀 Prerequisites

- **Backstage Application**: Running instance of Backstage
- **GitHub Repository**: Access to backstage-dashboard-templates repo
- **Node.js**: Version 16+ for local development
- **Git**: For version control

## 📁 Repository Structure

```
backstage-dashboard-templates/
├── registry.yaml                           # Dashboard index
├── templates/
│   ├── ba-main-dashboard/
│   │   └── config.yaml                     # Main dashboard config
│   ├── ba-devops-dashboard/
│   │   └── config.yaml                     # DevOps dashboard config
│   ├── ba-platform-dashboard/
│   │   └── config.yaml                     # Platform dashboard config
│   ├── ba-security-dashboard/
│   │   └── config.yaml                     # Security dashboard config
│   └── ba-developer-dashboard/
│       └── config.yaml                     # Developer dashboard config
├── docs/                                   # Documentation
└── examples/                               # Template examples
```

## 🛠️ Installation Steps

### Step 1: Clone Repository
```bash
git clone https://github.com/Portfolio-jaime/backstage-dashboard-templates.git
cd backstage-dashboard-templates
```

### Step 2: Verify Structure
```bash
# Check registry file
cat registry.yaml

# List all dashboard templates
ls -la templates/
```

### Step 3: Configure Backstage Application

#### Update useDashboardConfig Hook
```bash
# Navigate to your Backstage app
cd ../backstage-app-devc/backstage/packages/app/src/hooks/

# Verify useDashboardConfig.ts exists and is configured
cat useDashboardConfig.ts | grep "GITHUB_BASE_URL"
```

#### Configure HomePage Component
```bash
# Check HomePage.tsx is using dynamic configurations
cd ../components/home/
cat HomePage.tsx | grep "useDashboardConfig"
```

### Step 4: Test Dashboard Loading

1. **Start Backstage Application**:
```bash
cd backstage-app-devc/backstage
yarn start
```

2. **Open Browser**: Navigate to `http://localhost:3000`

3. **Verify Dashboard Cards**: Look for dashboard navigation cards

4. **Check Console**: Press F12 → Console and look for:
```
📋 Fetching dashboard registry from GitHub...
✅ Parsed 5 templates from registry
🎯 Loading dashboard: BA Operations Overview (ba-main)
```

## 🔧 Configuration

### GitHub API Configuration

The system uses GitHub's public API. No token required for public repositories.

**Rate Limits**: 60 requests/hour per IP for unauthenticated requests.

### Cache Configuration

- **Registry Cache**: 5 minutes
- **Dashboard Config Cache**: 5 minutes
- **GitHub API Cache**: None (real-time data)

## ✅ Verification Checklist

### Basic Functionality
- [ ] Registry loads from GitHub
- [ ] Dashboard cards appear on homepage
- [ ] Clicking cards switches dashboards
- [ ] Different dashboards show different content
- [ ] GitHub repositories load dynamically
- [ ] Service catalog displays
- [ ] World clock shows correct times

### Error Handling
- [ ] Graceful fallback if GitHub is unavailable
- [ ] Error messages are user-friendly
- [ ] Console logs are informative
- [ ] Dashboard works in both light and dark themes

### Performance
- [ ] Initial load time < 3 seconds
- [ ] Dashboard switching is responsive
- [ ] No memory leaks during navigation
- [ ] Mobile layout works properly

## 🚨 Common Issues

### Registry Not Loading
**Symptoms**: No dashboard cards appear
**Solutions**:
1. Check internet connectivity
2. Verify registry.yaml syntax with `yamllint registry.yaml`
3. Check browser console for network errors
4. Wait 5 minutes for cache refresh

### Dashboard Shows Default Content
**Symptoms**: All dashboards look the same
**Solutions**:
1. Verify individual config.yaml files exist
2. Check GitHub repository permissions
3. Validate YAML syntax in config files
4. Clear browser cache and refresh

### GitHub Repositories Not Loading
**Symptoms**: Empty or error in GitHub widget
**Solutions**:
1. Check GitHub API rate limits
2. Verify repository owner in config
3. Check network connectivity
4. Review filter keywords/topics

## 📞 Getting Help

If you encounter issues:

1. **Check Logs**: Browser console (F12)
2. **Validate YAML**: Use `yamllint` or online validators
3. **Test URLs**: Manually test GitHub raw file URLs
4. **Report Issues**: Create GitHub issue with:
   - Error messages
   - Browser console logs
   - Steps to reproduce

## 🔄 Next Steps

After successful setup:

1. **Customize Dashboards**: Edit config.yaml files
2. **Add New Dashboards**: Follow [Development Guide](DEVELOPMENT.md)
3. **Set Up Monitoring**: Implement health checks
4. **Configure Notifications**: Set up alerts

## 📚 Additional Resources

- [Configuration Reference](CONFIGURATION.md)
- [Development Guide](DEVELOPMENT.md)
- [Troubleshooting](TROUBLESHOOTING.md)
- [Architecture Guide](ARCHITECTURE.md)

---

**✅ Setup complete!** Your dynamic dashboard system is ready to use.