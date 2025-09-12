# 🐛 Debug Guide

Advanced debugging techniques and tools for the Backstage Dynamic Dashboard System.

## 🔍 Debug Environment Setup

### Browser Console Setup

#### Enable Verbose Logging
```javascript
// Enable debug mode
localStorage.setItem('debug-dashboard', 'true');

// Set specific debug categories
localStorage.setItem('debug', 'dashboard:*,github:*,catalog:*');

// Reload to apply
location.reload();
```

#### Console Helpers
```javascript
// Add global debug helpers
window.debugDashboard = {
  // Get current configuration
  getConfig: () => window.dashboardConfig,
  
  // Force refresh configuration
  refreshConfig: async () => {
    const event = new CustomEvent('dashboard-refresh');
    window.dispatchEvent(event);
  },
  
  // Test GitHub API
  testGitHub: async (owner = 'Portfolio-jaime') => {
    const response = await fetch(`https://api.github.com/users/${owner}/repos?per_page=5`);
    return await response.json();
  },
  
  // Test configuration URL
  testConfigUrl: async (configPath) => {
    const url = `https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/${configPath}`;
    const response = await fetch(url);
    return {
      status: response.status,
      statusText: response.statusText,
      content: response.ok ? await response.text() : null
    };
  }
};

console.log('🐛 Dashboard debug helpers loaded. Use window.debugDashboard');
```

### Network Request Monitoring

#### Advanced Network Filtering
```javascript
// Monitor all dashboard-related requests
const originalFetch = window.fetch;
window.fetch = async function(...args) {
  const response = await originalFetch.apply(this, args);
  
  // Log dashboard-related requests
  if (args[0].includes('github.com') || args[0].includes('backstage')) {
    console.group('🌐 Dashboard Network Request');
    console.log('URL:', args[0]);
    console.log('Status:', response.status);
    console.log('Headers:', Object.fromEntries(response.headers.entries()));
    console.groupEnd();
  }
  
  return response;
};
```

## 🔧 Component-Level Debugging

### Hook Debugging (`useDashboardConfig`)

#### State Inspection
```typescript
// Add to useDashboardConfig.ts for debugging
useEffect(() => {
  if (localStorage.getItem('debug-dashboard') === 'true') {
    console.group('🔧 Dashboard Config State');
    console.log('Config:', config);
    console.log('Loading:', loading);
    console.log('Error:', error);
    console.log('Available Templates:', availableTemplates);
    console.log('Current Template:', currentTemplate);
    console.groupEnd();
  }
}, [config, loading, error, availableTemplates, currentTemplate]);
```

#### Registry Parsing Debug
```typescript
// Enhanced parseRegistryYaml with debug logging
const parseRegistryYaml = (yamlContent: string): DashboardTemplate[] => {
  if (localStorage.getItem('debug-dashboard') === 'true') {
    console.log('🔍 Registry YAML Content:', yamlContent.substring(0, 500));
  }
  
  try {
    const templates: DashboardTemplate[] = [];
    
    // Debug: Show regex matches
    const templatesMatch = yamlContent.match(/templates:([\\s\\S]*?)(?=\\n\\w+:|$)/);
    if (localStorage.getItem('debug-dashboard') === 'true') {
      console.log('🎯 Templates section match:', templatesMatch ? 'Found' : 'Not found');
    }
    
    // ... rest of parsing logic with debug logs
    
    return templates;
  } catch (error) {
    console.error('❌ Registry parsing error:', error);
    return [EMERGENCY_FALLBACK_TEMPLATE];
  }
};
```

### Widget Debugging

#### GitHub Widget Debug
```typescript
// Add debug logging to TeamActivity widget
const loadDynamicRepos = async () => {
  const debug = localStorage.getItem('debug-dashboard') === 'true';
  
  if (debug) {
    console.group('🐙 GitHub Widget Debug');
    console.log('Dashboard Config:', config);
    console.log('GitHub Config:', config?.spec?.widgets?.github);
    console.log('Filters:', {
      keywords: githubConfig?.filters?.keywords,
      topics: githubConfig?.filters?.topics,
      exclude: githubConfig?.filters?.exclude
    });
  }
  
  try {
    const repos = await fetchUserRepos();
    const filteredRepos = repos.filter(repo => matchesFilters(repo));
    
    if (debug) {
      console.log('Total Repos:', repos.length);
      console.log('Filtered Repos:', filteredRepos.length);
      console.log('Repo Names:', filteredRepos.map(r => r.name));
      console.groupEnd();
    }
    
    return filteredRepos;
  } catch (error) {
    if (debug) {
      console.error('GitHub API Error:', error);
      console.groupEnd();
    }
    throw error;
  }
};
```

#### Catalog Widget Debug
```typescript
// Add debug logging to LiveCatalogServices
const fetchCatalogData = async () => {
  const debug = localStorage.getItem('debug-dashboard') === 'true';
  
  if (debug) {
    console.group('📋 Catalog Widget Debug');
  }
  
  try {
    const response = await catalogApi.getEntities({
      filter: {
        kind: ['Component', 'API', 'Resource'],
      },
    });
    
    if (debug) {
      console.log('Catalog Response:', response);
      console.log('Entity Count:', response.items.length);
      console.log('Entity Kinds:', response.items.map(e => e.kind));
      console.groupEnd();
    }
    
    return response.items;
  } catch (error) {
    if (debug) {
      console.error('Catalog API Error:', error);
      console.groupEnd();
    }
    throw error;
  }
};
```

## 📊 Performance Debugging

### Render Performance
```typescript
// Add performance monitoring to components
const PerformanceWrapper = ({ children, componentName }) => {
  useEffect(() => {
    const startTime = performance.now();
    
    return () => {
      const endTime = performance.now();
      if (localStorage.getItem('debug-dashboard') === 'true') {
        console.log(`⏱️ ${componentName} render time: ${endTime - startTime}ms`);
      }
    };
  });
  
  return children;
};

// Usage in components
<PerformanceWrapper componentName="HomePage">
  <HomePage />
</PerformanceWrapper>
```

### Memory Usage Monitoring
```javascript
// Monitor memory usage
const logMemoryUsage = () => {
  if (performance.memory) {
    console.table({
      'Used JS Heap Size': `${Math.round(performance.memory.usedJSHeapSize / 1024 / 1024)}MB`,
      'Total JS Heap Size': `${Math.round(performance.memory.totalJSHeapSize / 1024 / 1024)}MB`,
      'JS Heap Size Limit': `${Math.round(performance.memory.jsHeapSizeLimit / 1024 / 1024)}MB`
    });
  }
};

// Set interval to monitor memory
if (localStorage.getItem('debug-dashboard') === 'true') {
  setInterval(logMemoryUsage, 10000); // Every 10 seconds
}
```

## 🌐 Network Debugging

### Request Interceptor
```typescript
// Comprehensive request monitoring
class DashboardRequestMonitor {
  private requests: Map<string, any> = new Map();
  
  constructor() {
    this.interceptFetch();
  }
  
  private interceptFetch() {
    const originalFetch = window.fetch;
    
    window.fetch = async (input, init) => {
      const url = typeof input === 'string' ? input : input.url;
      const requestId = `${Date.now()}-${Math.random()}`;
      
      this.requests.set(requestId, {
        url,
        startTime: performance.now(),
        method: init?.method || 'GET'
      });
      
      try {
        const response = await originalFetch(input, init);
        const endTime = performance.now();
        
        this.logRequest(requestId, {
          status: response.status,
          statusText: response.statusText,
          duration: endTime - this.requests.get(requestId)?.startTime,
          headers: Object.fromEntries(response.headers.entries())
        });
        
        return response;
      } catch (error) {
        this.logError(requestId, error);
        throw error;
      }
    };
  }
  
  private logRequest(requestId: string, response: any) {
    const request = this.requests.get(requestId);
    if (request && this.isDashboardRequest(request.url)) {
      console.group(`🌐 ${request.method} ${request.url}`);
      console.log('Duration:', `${response.duration.toFixed(2)}ms`);
      console.log('Status:', `${response.status} ${response.statusText}`);
      console.log('Response Headers:', response.headers);
      console.groupEnd();
    }
    this.requests.delete(requestId);
  }
  
  private logError(requestId: string, error: any) {
    const request = this.requests.get(requestId);
    if (request && this.isDashboardRequest(request.url)) {
      console.error(`❌ Request failed: ${request.url}`, error);
    }
    this.requests.delete(requestId);
  }
  
  private isDashboardRequest(url: string): boolean {
    return url.includes('github.com') || 
           url.includes('backstage') || 
           url.includes('dashboard-templates');
  }
  
  getRequestStats() {
    // Return performance statistics
    return {
      activeRequests: this.requests.size,
      requests: Array.from(this.requests.values())
    };
  }
}

// Initialize monitor
if (localStorage.getItem('debug-dashboard') === 'true') {
  window.dashboardRequestMonitor = new DashboardRequestMonitor();
}
```

### Rate Limit Monitoring
```typescript
const monitorGitHubRateLimit = () => {
  const checkRateLimit = async () => {
    try {
      const response = await fetch('https://api.github.com/rate_limit');
      const data = await response.json();
      
      console.table({
        'Core Limit': data.resources.core.limit,
        'Core Remaining': data.resources.core.remaining,
        'Core Reset': new Date(data.resources.core.reset * 1000).toLocaleTimeString(),
        'Search Limit': data.resources.search.limit,
        'Search Remaining': data.resources.search.remaining,
        'Search Reset': new Date(data.resources.search.reset * 1000).toLocaleTimeString()
      });
      
      if (data.resources.core.remaining < 10) {
        console.warn('⚠️ GitHub API rate limit running low!');
      }
    } catch (error) {
      console.error('Failed to check GitHub rate limit:', error);
    }
  };
  
  // Check immediately and then every 5 minutes
  checkRateLimit();
  setInterval(checkRateLimit, 5 * 60 * 1000);
};
```

## 🧪 Testing Utilities

### Configuration Testing
```typescript
class DashboardConfigTester {
  async testRegistry(registryUrl?: string) {
    const url = registryUrl || 'https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml';
    
    console.group('🧪 Registry Test');
    
    try {
      const response = await fetch(url);
      const content = await response.text();
      
      console.log('✅ Registry accessible');
      console.log('Status:', response.status);
      console.log('Content length:', content.length);
      
      // Test YAML parsing
      const templates = this.parseRegistryYaml(content);
      console.log('✅ Registry parsed');
      console.log('Templates found:', templates.length);
      
      // Test each config file
      for (const template of templates) {
        await this.testTemplate(template);
      }
      
    } catch (error) {
      console.error('❌ Registry test failed:', error);
    }
    
    console.groupEnd();
  }
  
  async testTemplate(template: any) {
    const configUrl = `https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/${template.configPath}`;
    
    console.group(`🔧 Testing template: ${template.name}`);
    
    try {
      const response = await fetch(configUrl);
      const content = await response.text();
      
      if (response.ok) {
        console.log('✅ Config accessible');
        console.log('Content length:', content.length);
        
        // Basic YAML validation
        if (content.includes('apiVersion') && content.includes('spec')) {
          console.log('✅ Basic YAML structure valid');
        } else {
          console.warn('⚠️ Missing required YAML fields');
        }
      } else {
        console.error('❌ Config not accessible:', response.status);
      }
    } catch (error) {
      console.error('❌ Template test failed:', error);
    }
    
    console.groupEnd();
  }
  
  private parseRegistryYaml(content: string): any[] {
    // Simplified parsing for testing
    const templates = [];
    const lines = content.split('\n');
    let inTemplates = false;
    let currentTemplate: any = {};
    
    for (const line of lines) {
      if (line.includes('templates:')) {
        inTemplates = true;
        continue;
      }
      
      if (inTemplates && line.startsWith('  - id:')) {
        if (Object.keys(currentTemplate).length > 0) {
          templates.push(currentTemplate);
        }
        currentTemplate = { id: line.split(':')[1].trim() };
      } else if (inTemplates && line.startsWith('    ')) {
        const [key, ...valueParts] = line.trim().split(':');
        currentTemplate[key] = valueParts.join(':').trim();
      }
    }
    
    if (Object.keys(currentTemplate).length > 0) {
      templates.push(currentTemplate);
    }
    
    return templates;
  }
}

// Make available globally
window.dashboardTester = new DashboardConfigTester();
```

### Widget Testing
```typescript
const testWidgets = async () => {
  console.group('🎛️ Widget Tests');
  
  // Test GitHub widget
  try {
    console.log('Testing GitHub API...');
    const response = await fetch('https://api.github.com/users/Portfolio-jaime/repos?per_page=1');
    const data = await response.json();
    console.log('✅ GitHub API accessible');
    console.log('Rate limit remaining:', response.headers.get('x-ratelimit-remaining'));
  } catch (error) {
    console.error('❌ GitHub API test failed:', error);
  }
  
  // Test Catalog API
  try {
    console.log('Testing Catalog API...');
    const response = await fetch('/api/catalog/entities?filter=kind=component');
    const data = await response.json();
    console.log('✅ Catalog API accessible');
    console.log('Components found:', data.items?.length || 0);
  } catch (error) {
    console.error('❌ Catalog API test failed:', error);
  }
  
  // Test timezone functionality
  console.log('Testing timezone support...');
  const testTimezones = ['Europe/London', 'America/New_York', 'Asia/Tokyo'];
  testTimezones.forEach(tz => {
    try {
      const time = new Date().toLocaleString('en-US', { timeZone: tz });
      console.log(`✅ ${tz}: ${time}`);
    } catch (error) {
      console.error(`❌ ${tz}: Invalid timezone`);
    }
  });
  
  console.groupEnd();
};
```

## 📈 Performance Profiling

### React Performance Profiler
```typescript
import { Profiler } from 'react';

const onRenderCallback = (id, phase, actualDuration, baseDuration, startTime, commitTime) => {
  if (localStorage.getItem('debug-dashboard') === 'true') {
    console.log('⚡ Component Performance:', {
      id,
      phase,
      actualDuration: `${actualDuration.toFixed(2)}ms`,
      baseDuration: `${baseDuration.toFixed(2)}ms`,
      startTime: `${startTime.toFixed(2)}ms`,
      commitTime: `${commitTime.toFixed(2)}ms`
    });
  }
};

// Wrap components for profiling
<Profiler id="HomePage" onRender={onRenderCallback}>
  <HomePage />
</Profiler>
```

### Bundle Analysis
```javascript
// Analyze loaded modules
const analyzeModules = () => {
  const modules = Object.keys(window.__webpack_require__.cache || {});
  const modulesBySize = modules.map(id => ({
    id,
    module: window.__webpack_require__.cache[id],
    size: JSON.stringify(window.__webpack_require__.cache[id]).length
  })).sort((a, b) => b.size - a.size);
  
  console.table(modulesBySize.slice(0, 20));
  
  const totalSize = modulesBySize.reduce((sum, mod) => sum + mod.size, 0);
  console.log(`Total bundle size: ${(totalSize / 1024 / 1024).toFixed(2)}MB`);
};
```

## 🎯 Debugging Specific Scenarios

### Debug Dashboard Switching Issues
```typescript
const debugDashboardSwitch = (templateId: string) => {
  console.group(`🔄 Debug Dashboard Switch: ${templateId}`);
  
  // Log before switch
  console.log('Before switch:');
  console.log('- Current template:', window.dashboardConfig?.currentTemplate?.id);
  console.log('- Available templates:', window.dashboardConfig?.availableTemplates?.map(t => t.id));
  console.log('- Stored selection:', localStorage.getItem('backstage-selected-dashboard'));
  
  // Perform switch
  window.dashboardConfig.switchTemplate(templateId).then(() => {
    // Log after switch
    setTimeout(() => {
      console.log('After switch:');
      console.log('- New template:', window.dashboardConfig?.currentTemplate?.id);
      console.log('- New config loaded:', !!window.dashboardConfig?.config);
      console.log('- Storage updated:', localStorage.getItem('backstage-selected-dashboard'));
      console.groupEnd();
    }, 1000);
  }).catch(error => {
    console.error('Switch failed:', error);
    console.groupEnd();
  });
};
```

### Debug Widget Configuration Issues
```typescript
const debugWidgetConfig = (widgetName: string) => {
  console.group(`🎛️ Debug Widget: ${widgetName}`);
  
  const config = window.dashboardConfig?.config;
  const widgetConfig = config?.spec?.widgets?.[widgetName];
  
  console.log('Dashboard Config:', config);
  console.log('Widget Config:', widgetConfig);
  console.log('Widget Enabled:', widgetConfig?.enabled);
  
  if (widgetName === 'github') {
    console.log('GitHub Filters:', widgetConfig?.filters);
    console.log('GitHub Owner:', widgetConfig?.owner);
  }
  
  if (widgetName === 'catalog') {
    console.log('Catalog Filters:', widgetConfig?.filters);
    console.log('Max Services:', widgetConfig?.maxServices);
  }
  
  console.groupEnd();
};
```

## 📋 Debug Checklist

### Pre-Debug Setup
- [ ] Enable debug mode: `localStorage.setItem('debug-dashboard', 'true')`
- [ ] Open browser console (F12)
- [ ] Clear browser cache if needed
- [ ] Check network connectivity

### Configuration Issues
- [ ] Test registry URL accessibility
- [ ] Validate YAML syntax with yamllint
- [ ] Check file paths in registry
- [ ] Verify GitHub repository access

### Widget Issues
- [ ] Test external APIs (GitHub, Catalog)
- [ ] Check widget configuration in YAML
- [ ] Verify filters are not too restrictive
- [ ] Monitor console for widget-specific errors

### Performance Issues
- [ ] Profile component render times
- [ ] Monitor network request duration
- [ ] Check memory usage over time
- [ ] Analyze bundle size and loading

### Theme Issues
- [ ] Test in both light and dark themes
- [ ] Verify Material-UI theme usage
- [ ] Check for hardcoded colors
- [ ] Test responsive layout

---

**🐛 Debug systematically - isolate, reproduce, and fix one issue at a time**