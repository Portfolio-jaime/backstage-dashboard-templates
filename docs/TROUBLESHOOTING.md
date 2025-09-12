# 🚨 Troubleshooting Guide

This guide helps you diagnose and resolve common issues with the Backstage Dynamic Dashboard System.

## 🔍 Quick Diagnosis

### Dashboard Not Loading
**Symptoms**: Blank screen, loading indicator stuck, no dashboard cards

**Quick Checks**:
```bash
# 1. Check registry accessibility
curl -s "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml"

# 2. Verify internet connectivity
ping github.com

# 3. Check browser console (F12 → Console)
# Look for: "📋 Fetching dashboard registry from GitHub..."
```

### Dashboard Shows Default/Fallback Content
**Symptoms**: All dashboards look identical, generic content displayed

**Quick Checks**:
```bash
# 1. Test specific config file
curl -s "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/templates/ba-devops-dashboard/config.yaml"

# 2. Validate YAML syntax
yamllint templates/ba-devops-dashboard/config.yaml

# 3. Check file exists in repository
ls -la templates/ba-devops-dashboard/
```

### GitHub Repositories Not Loading
**Symptoms**: Empty GitHub widget, "No repositories found"

**Quick Checks**:
```bash
# 1. Test GitHub API directly
curl -s "https://api.github.com/users/Portfolio-jaime/repos?per_page=5" | jq '.[].name'

# 2. Check rate limits
curl -s -I "https://api.github.com/users/Portfolio-jaime/repos" | grep "x-ratelimit"

# 3. Verify repository filters in config
grep -A 5 "keywords:" templates/ba-devops-dashboard/config.yaml
```

## 🐛 Common Issues & Solutions

### Issue 1: Registry Loading Failed

#### Symptoms
- No dashboard cards appear
- Console error: "Failed to fetch registry"
- Stuck on loading screen

#### Root Causes
1. **Network connectivity issues**
2. **GitHub repository access problems**
3. **YAML syntax errors in registry.yaml**
4. **Browser cache issues**

#### Solutions

**Step 1: Verify Registry Access**
```bash
# Test direct access to registry
curl -v "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml"

# Expected: HTTP 200 with YAML content
# If 404: Check repository name and file path
# If network error: Check internet connectivity
```

**Step 2: Validate YAML Syntax**
```bash
# Install yamllint if not available
pip install yamllint

# Validate registry.yaml
yamllint registry.yaml

# Common syntax errors:
# - Missing spaces after colons
# - Incorrect indentation
# - Special characters not quoted
```

**Step 3: Clear Browser Cache**
```javascript
// In browser console (F12)
// Clear localStorage
localStorage.clear();

// Hard refresh
location.reload(true);
```

**Step 4: Check Browser Console**
```javascript
// Look for these logs:
"📋 Fetching dashboard registry from GitHub..."
"✅ Parsed X templates from registry"

// Error indicators:
"❌ Error parsing registry YAML"
"⚠️ Registry not accessible"
```

### Issue 2: Dashboard Configuration Not Loading

#### Symptoms
- Dashboard cards appear but clicking shows fallback content
- Console error: "Failed to fetch config"
- All dashboards show identical content

#### Root Causes
1. **Missing config.yaml files**
2. **Incorrect paths in registry.yaml**
3. **YAML syntax errors in config files**
4. **GitHub raw file access issues**

#### Solutions

**Step 1: Verify File Existence**
```bash
# Check all config files exist
find templates/ -name "config.yaml" -ls

# Test specific file access
curl -s "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/templates/ba-devops-dashboard/config.yaml" | head -10
```

**Step 2: Validate Configuration Paths**
```bash
# Extract paths from registry
grep "configPath:" registry.yaml

# Verify each path exists
ls templates/ba-devops-dashboard/config.yaml
ls templates/ba-platform-dashboard/config.yaml
# ... check all paths
```

**Step 3: Debug Configuration Loading**
```javascript
// In browser console, monitor network requests
// Look for failed requests to GitHub raw files
// Check response status codes and error messages
```

### Issue 3: GitHub Widget Shows No Repositories

#### Symptoms
- GitHub widget is empty
- Message: "No repositories found" or loading indicator
- Widget appears but no repository data

#### Root Causes
1. **GitHub API rate limiting**
2. **Repository filters too restrictive**
3. **Incorrect GitHub username/organization**
4. **Network issues with GitHub API**

#### Solutions

**Step 1: Test GitHub API Direct Access**
```bash
# Test basic API access
curl -s "https://api.github.com/users/Portfolio-jaime/repos?per_page=5" | jq '.[].name'

# Check rate limits
curl -s -I "https://api.github.com/users/Portfolio-jaime/repos" | grep "x-ratelimit"

# Expected headers:
# x-ratelimit-limit: 60
# x-ratelimit-remaining: 59
# x-ratelimit-reset: 1642598400
```

**Step 2: Review Repository Filters**
```yaml
# Check if filters are too restrictive
github:
  filters:
    keywords: ["very-specific-keyword"]  # Too specific?
    topics: ["rare-topic"]               # Non-existent topic?
    exclude: ["*"]                       # Excluding everything?
```

**Step 3: Broaden Filters Temporarily**
```yaml
# Temporarily use broader filters for testing
github:
  filters:
    keywords: ["backstage"]              # Common keyword
    topics: ["backstage"]                # Common topic
    exclude: ["archived"]                # Only exclude archived
    maxRepos: 20                         # Increase limit
```

**Step 4: Check Console Logs**
```javascript
// Look for these logs:
"🔍 Loading dynamic repositories for dashboard: devops"
"📡 Fetching activity for repos: [repo1, repo2, ...]"
"✅ GitHub activity loaded: X events"

// Error indicators:
"❌ Error loading dynamic repositories"
"⚠️ GitHub API rate limit exceeded"
```

### Issue 4: Dark Theme Display Issues

#### Symptoms
- White text on white background
- Invisible elements in dark theme
- Poor contrast or readability

#### Root Causes
1. **Hardcoded colors instead of theme variables**
2. **Missing Material-UI theme integration**
3. **CSS overrides breaking theme system**

#### Solutions

**Step 1: Verify Material-UI Theme Usage**
```typescript
// Correct: Using theme variables
const useStyles = makeStyles((theme) => ({
  container: {
    backgroundColor: theme.palette.background.paper,
    color: theme.palette.text.primary,
  },
}));

// Incorrect: Hardcoded colors
const useStyles = makeStyles(() => ({
  container: {
    backgroundColor: '#ffffff',  // Won't work in dark theme
    color: '#000000',           // Won't work in dark theme
  },
}));
```

**Step 2: Test Both Themes**
```javascript
// In Backstage, toggle theme and verify:
// Settings → Theme → Light/Dark

// Check these elements work in both themes:
// - Dashboard cards
// - Widget backgrounds
// - Text readability
// - Button states
```

### Issue 5: Service Catalog Widget Empty

#### Symptoms
- Catalog widget shows "No services found"
- Loading state but no data appears
- Widget displays but with empty list

#### Root Causes
1. **No entities registered in Backstage catalog**
2. **Catalog API connectivity issues**
3. **Filter configuration excluding all entities**
4. **Permissions or authentication problems**

#### Solutions

**Step 1: Verify Catalog API**
```javascript
// In browser console (while on Backstage)
// Test catalog API directly
fetch('/api/catalog/entities')
  .then(r => r.json())
  .then(d => console.log('Catalog entities:', d.items.length));
```

**Step 2: Check Entity Registration**
```bash
# In your Backstage instance, navigate to:
# /catalog

# Expected: See registered components, APIs, and resources
# If empty: Register some test entities
```

**Step 3: Review Catalog Filters**
```yaml
catalog:
  filters:
    kinds: ["Component", "API", "Resource"]  # Include common kinds
    # Remove restrictive filters temporarily
```

### Issue 6: World Clock Widget Issues

#### Symptoms
- Incorrect times displayed
- Timezone names not showing correctly
- Clock not updating

#### Root Causes
1. **Invalid timezone identifiers**
2. **Browser timezone detection issues**
3. **Refresh interval too long/short**

#### Solutions

**Step 1: Validate Timezone Identifiers**
```javascript
// Test timezone validity in browser console
const testTimezone = (tz) => {
  try {
    return new Date().toLocaleString('en-US', { timeZone: tz });
  } catch (e) {
    return `Invalid timezone: ${tz}`;
  }
};

// Test your configured timezones
testTimezone('Europe/London');     // Should work
testTimezone('America/New_York');  // Should work
testTimezone('Invalid/Timezone');  // Should show error
```

**Step 2: Check Refresh Configuration**
```yaml
worldClock:
  refreshInterval: 1000  # 1 second - reasonable for clocks
  # Not: 60000 (too slow) or 100 (too fast)
```

## 🔧 Debug Tools & Techniques

### Browser Developer Tools

#### Console Debugging
```javascript
// Enable verbose logging
localStorage.setItem('debug', 'true');

// Monitor dashboard config loading
window.addEventListener('dashboardConfigLoaded', (event) => {
  console.log('Dashboard config loaded:', event.detail);
});

// Check current configuration
console.log('Current dashboard config:', window.dashboardConfig);
```

#### Network Monitoring
1. Open F12 → Network tab
2. Filter by "Fetch/XHR"
3. Look for requests to:
   - `raw.githubusercontent.com` (config loading)
   - `api.github.com` (repository data)
   - `/api/catalog/` (Backstage catalog)

#### Storage Inspection
```javascript
// Check localStorage for saved preferences
console.log('Stored dashboard:', localStorage.getItem('backstage-selected-dashboard'));

// Clear stored preferences
localStorage.removeItem('backstage-selected-dashboard');
```

### YAML Validation Tools

#### Command Line
```bash
# Install yamllint
pip install yamllint

# Validate specific files
yamllint registry.yaml
yamllint templates/ba-devops-dashboard/config.yaml

# Custom rules for stricter validation
yamllint -d "{extends: default, rules: {line-length: {max: 120}}}" registry.yaml
```

#### Online Validators
- [YAML Lint](http://www.yamllint.com/)
- [Online YAML Parser](https://yaml-online-parser.appspot.com/)

### API Testing

#### GitHub API
```bash
# Test repository listing
curl -s "https://api.github.com/users/Portfolio-jaime/repos?per_page=5&sort=updated" | jq '.[] | {name, topics, updated_at}'

# Check rate limits
curl -s -I "https://api.github.com/rate_limit" | grep "x-ratelimit"

# Test with filters
curl -s "https://api.github.com/search/repositories?q=user:Portfolio-jaime+topic:backstage" | jq '.items[] | .name'
```

#### Backstage Catalog API
```bash
# From within your network/container where Backstage runs
curl -s "http://localhost:7007/api/catalog/entities" | jq '.[] | {name: .metadata.name, kind}'
```

## 📊 Health Checks

### System Health Checklist

```bash
#!/bin/bash
# dashboard-health-check.sh

echo "🔍 Dashboard System Health Check"
echo "================================"

# 1. Registry accessibility
echo "📋 Testing registry access..."
if curl -s --fail "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml" > /dev/null; then
    echo "✅ Registry accessible"
else
    echo "❌ Registry not accessible"
fi

# 2. Config file accessibility
echo "📄 Testing config files..."
for config in templates/*/config.yaml; do
    template_name=$(basename $(dirname $config))
    url="https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/$config"
    if curl -s --fail "$url" > /dev/null; then
        echo "✅ $template_name config accessible"
    else
        echo "❌ $template_name config not accessible"
    fi
done

# 3. GitHub API accessibility
echo "🐙 Testing GitHub API..."
if curl -s --fail "https://api.github.com/users/Portfolio-jaime/repos?per_page=1" > /dev/null; then
    echo "✅ GitHub API accessible"
else
    echo "❌ GitHub API not accessible"
fi

# 4. YAML syntax validation
echo "📝 Validating YAML syntax..."
if command -v yamllint &> /dev/null; then
    if yamllint registry.yaml; then
        echo "✅ Registry YAML valid"
    else
        echo "❌ Registry YAML invalid"
    fi
else
    echo "⚠️  yamllint not installed - skipping validation"
fi

echo "================================"
echo "Health check complete!"
```

## 📞 Getting Help

### Before Seeking Help

**Gather Information**:
1. **Browser console logs** (F12 → Console)
2. **Network requests** (F12 → Network)
3. **Configuration files** being used
4. **Steps to reproduce** the issue
5. **Expected vs actual behavior**

### Information to Include in Issues

```markdown
## Issue Description
Brief description of the problem

## Environment
- Browser: Chrome/Firefox/Safari + version
- Backstage version: X.X.X
- Dashboard system version: X.X.X
- OS: macOS/Windows/Linux

## Steps to Reproduce
1. Step one
2. Step two  
3. Step three

## Expected Behavior
What should happen

## Actual Behavior  
What actually happens

## Console Logs
```
Paste relevant console logs here
```

## Configuration
```yaml
Paste relevant configuration here
```

## Screenshots
If applicable, add screenshots
```

### Community Resources

- **GitHub Issues**: [Report bugs and feature requests](https://github.com/Portfolio-jaime/backstage-dashboard-templates/issues)
- **Backstage Discord**: Join the #plugins channel
- **Documentation**: Check all guides in `/docs/` folder

---

**🚨 Most issues are configuration-related - start with YAML validation!**