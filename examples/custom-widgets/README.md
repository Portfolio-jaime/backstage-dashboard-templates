# 🎛️ Custom Widgets Examples

This directory contains examples of how to create and integrate custom widgets into the dashboard system.

## 📁 Structure

```
custom-widgets/
├── README.md                    # This file
├── CustomMetricsWidget.tsx      # Example custom widget
├── CustomAPIWidget.tsx          # API integration example
├── CustomChartWidget.tsx        # Chart/visualization example
├── config-with-custom.yaml      # Configuration using custom widgets
└── integration-guide.md         # Step-by-step integration
```

## 🚀 Quick Start

### 1. Basic Custom Widget

Create a simple custom widget that displays custom data:

```typescript
// CustomMetricsWidget.tsx
import React, { useState, useEffect } from 'react';
import { InfoCard } from '@backstage/core-components';
import { Typography, Box, Grid, Paper } from '@material-ui/core';
import { makeStyles } from '@material-ui/core/styles';
import { useDashboardConfig } from '../hooks/useDashboardConfig';

const useStyles = makeStyles((theme) => ({
  metricCard: {
    padding: theme.spacing(2),
    textAlign: 'center',
    backgroundColor: theme.palette.background.paper,
  },
  metricValue: {
    fontSize: '2rem',
    fontWeight: 'bold',
    color: theme.palette.primary.main,
  },
  metricLabel: {
    color: theme.palette.text.secondary,
  },
}));

export const CustomMetricsWidget = () => {
  const classes = useStyles();
  const { config } = useDashboardConfig();
  const [metrics, setMetrics] = useState({});
  const [loading, setLoading] = useState(true);

  const widgetConfig = config?.spec?.widgets?.customMetrics;

  // Don't render if disabled
  if (!widgetConfig?.enabled) {
    return null;
  }

  useEffect(() => {
    const fetchMetrics = async () => {
      setLoading(true);
      try {
        // Simulate API call
        await new Promise(resolve => setTimeout(resolve, 1000));
        
        setMetrics({
          totalServices: 42,
          activeDeployments: 8,
          healthyServices: 38,
          averageResponseTime: 245
        });
      } catch (error) {
        console.error('Failed to fetch metrics:', error);
      } finally {
        setLoading(false);
      }
    };

    fetchMetrics();
    
    const interval = setInterval(fetchMetrics, widgetConfig.refreshInterval || 30000);
    return () => clearInterval(interval);
  }, [widgetConfig]);

  if (loading) {
    return (
      <InfoCard title={widgetConfig.title || "Custom Metrics"}>
        <Box p={3} textAlign="center">
          <Typography>Loading metrics...</Typography>
        </Box>
      </InfoCard>
    );
  }

  return (
    <InfoCard title={widgetConfig.title || "Custom Metrics"}>
      <Box p={2}>
        <Grid container spacing={2}>
          <Grid item xs={6} sm={3}>
            <Paper className={classes.metricCard}>
              <Typography className={classes.metricValue}>
                {metrics.totalServices}
              </Typography>
              <Typography className={classes.metricLabel}>
                Total Services
              </Typography>
            </Paper>
          </Grid>
          <Grid item xs={6} sm={3}>
            <Paper className={classes.metricCard}>
              <Typography className={classes.metricValue}>
                {metrics.activeDeployments}
              </Typography>
              <Typography className={classes.metricLabel}>
                Active Deployments
              </Typography>
            </Paper>
          </Grid>
          <Grid item xs={6} sm={3}>
            <Paper className={classes.metricCard}>
              <Typography className={classes.metricValue}>
                {metrics.healthyServices}
              </Typography>
              <Typography className={classes.metricLabel}>
                Healthy Services
              </Typography>
            </Paper>
          </Grid>
          <Grid item xs={6} sm={3}>
            <Paper className={classes.metricCard}>
              <Typography className={classes.metricValue}>
                {metrics.averageResponseTime}ms
              </Typography>
              <Typography className={classes.metricLabel}>
                Avg Response Time
              </Typography>
            </Paper>
          </Grid>
        </Grid>
      </Box>
    </InfoCard>
  );
};
```

### 2. API Integration Widget

Example of integrating with external APIs:

```typescript
// CustomAPIWidget.tsx
import React, { useState, useEffect } from 'react';
import { InfoCard } from '@backstage/core-components';
import { 
  List, 
  ListItem, 
  ListItemText, 
  Typography, 
  Box,
  CircularProgress,
  Alert
} from '@material-ui/core';
import { useDashboardConfig } from '../hooks/useDashboardConfig';

interface APIData {
  id: string;
  title: string;
  status: 'active' | 'inactive' | 'warning';
  lastUpdated: string;
}

export const CustomAPIWidget = () => {
  const { config } = useDashboardConfig();
  const [data, setData] = useState<APIData[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  const widgetConfig = config?.spec?.widgets?.customAPI;

  if (!widgetConfig?.enabled) {
    return null;
  }

  useEffect(() => {
    const fetchData = async () => {
      if (!widgetConfig.apiEndpoint) {
        setError('No API endpoint configured');
        setLoading(false);
        return;
      }

      setLoading(true);
      setError(null);

      try {
        const response = await fetch(widgetConfig.apiEndpoint, {
          headers: {
            'Accept': 'application/json',
            ...(widgetConfig.apiKey && {
              'Authorization': `Bearer ${widgetConfig.apiKey}`
            })
          }
        });

        if (!response.ok) {
          throw new Error(`API request failed: ${response.statusText}`);
        }

        const result = await response.json();
        setData(result.items || []);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
    
    const interval = setInterval(fetchData, widgetConfig.refreshInterval || 300000);
    return () => clearInterval(interval);
  }, [widgetConfig]);

  if (loading) {
    return (
      <InfoCard title={widgetConfig.title || "Custom API Data"}>
        <Box display="flex" justifyContent="center" alignItems="center" height={200}>
          <CircularProgress />
          <Typography variant="body2" style={{ marginLeft: 16 }}>
            Loading data from API...
          </Typography>
        </Box>
      </InfoCard>
    );
  }

  if (error) {
    return (
      <InfoCard title={widgetConfig.title || "Custom API Data"}>
        <Alert severity="error" style={{ margin: 16 }}>
          Failed to load data: {error}
        </Alert>
      </InfoCard>
    );
  }

  const getStatusColor = (status: string) => {
    switch (status) {
      case 'active': return 'success';
      case 'warning': return 'warning';
      case 'inactive': return 'error';
      default: return 'info';
    }
  };

  return (
    <InfoCard title={widgetConfig.title || "Custom API Data"}>
      <List>
        {data.slice(0, widgetConfig.maxItems || 10).map((item) => (
          <ListItem key={item.id}>
            <ListItemText
              primary={item.title}
              secondary={
                <Box display="flex" justifyContent="space-between" alignItems="center">
                  <Typography variant="caption">
                    Status: {item.status}
                  </Typography>
                  <Typography variant="caption">
                    Updated: {new Date(item.lastUpdated).toLocaleDateString()}
                  </Typography>
                </Box>
              }
            />
          </ListItem>
        ))}
      </List>
      
      {data.length === 0 && (
        <Box p={3} textAlign="center">
          <Typography color="textSecondary">
            No data available
          </Typography>
        </Box>
      )}
    </InfoCard>
  );
};
```

### 3. Chart/Visualization Widget

Example with charts and data visualization:

```typescript
// CustomChartWidget.tsx
import React, { useState, useEffect } from 'react';
import { InfoCard } from '@backstage/core-components';
import { Typography, Box, Select, MenuItem, FormControl, InputLabel } from '@material-ui/core';
import { 
  LineChart, 
  Line, 
  XAxis, 
  YAxis, 
  CartesianGrid, 
  Tooltip, 
  ResponsiveContainer 
} from 'recharts';
import { useDashboardConfig } from '../hooks/useDashboardConfig';

interface ChartData {
  timestamp: string;
  value: number;
  category: string;
}

export const CustomChartWidget = () => {
  const { config } = useDashboardConfig();
  const [data, setData] = useState<ChartData[]>([]);
  const [selectedMetric, setSelectedMetric] = useState('requests');
  const [timeRange, setTimeRange] = useState('24h');

  const widgetConfig = config?.spec?.widgets?.customChart;

  if (!widgetConfig?.enabled) {
    return null;
  }

  useEffect(() => {
    const generateSampleData = () => {
      const now = new Date();
      const data = [];
      
      for (let i = 23; i >= 0; i--) {
        const timestamp = new Date(now.getTime() - (i * 60 * 60 * 1000));
        data.push({
          timestamp: timestamp.toISOString(),
          value: Math.floor(Math.random() * 100) + 50,
          category: selectedMetric
        });
      }
      
      setData(data);
    };

    generateSampleData();
    
    const interval = setInterval(generateSampleData, widgetConfig.refreshInterval || 60000);
    return () => clearInterval(interval);
  }, [selectedMetric, widgetConfig]);

  const formatData = data.map(item => ({
    time: new Date(item.timestamp).toLocaleTimeString('en-US', { 
      hour: '2-digit', 
      minute: '2-digit' 
    }),
    value: item.value
  }));

  return (
    <InfoCard title={widgetConfig.title || "Performance Metrics"}>
      <Box p={2}>
        {/* Controls */}
        <Box display="flex" justifyContent="space-between" alignItems="center" mb={2}>
          <FormControl variant="outlined" size="small" style={{ minWidth: 120 }}>
            <InputLabel>Metric</InputLabel>
            <Select
              value={selectedMetric}
              onChange={(e) => setSelectedMetric(e.target.value as string)}
              label="Metric"
            >
              <MenuItem value="requests">Requests/Hour</MenuItem>
              <MenuItem value="errors">Error Rate</MenuItem>
              <MenuItem value="latency">Avg Latency</MenuItem>
              <MenuItem value="throughput">Throughput</MenuItem>
            </Select>
          </FormControl>
          
          <FormControl variant="outlined" size="small" style={{ minWidth: 100 }}>
            <InputLabel>Range</InputLabel>
            <Select
              value={timeRange}
              onChange={(e) => setTimeRange(e.target.value as string)}
              label="Range"
            >
              <MenuItem value="1h">1 Hour</MenuItem>
              <MenuItem value="6h">6 Hours</MenuItem>
              <MenuItem value="24h">24 Hours</MenuItem>
              <MenuItem value="7d">7 Days</MenuItem>
            </Select>
          </FormControl>
        </Box>
        
        {/* Chart */}
        <Box height={300}>
          <ResponsiveContainer width="100%" height="100%">
            <LineChart data={formatData}>
              <CartesianGrid strokeDasharray="3 3" />
              <XAxis dataKey="time" />
              <YAxis />
              <Tooltip />
              <Line 
                type="monotone" 
                dataKey="value" 
                stroke="#1976d2" 
                strokeWidth={2}
                dot={{ fill: '#1976d2', strokeWidth: 2, r: 4 }}
                activeDot={{ r: 6 }}
              />
            </LineChart>
          </ResponsiveContainer>
        </Box>
        
        {/* Summary */}
        <Box mt={2} display="flex" justifyContent="space-around">
          <Box textAlign="center">
            <Typography variant="h6">
              {formatData.length > 0 ? formatData[formatData.length - 1].value : 0}
            </Typography>
            <Typography variant="caption" color="textSecondary">
              Current
            </Typography>
          </Box>
          <Box textAlign="center">
            <Typography variant="h6">
              {formatData.length > 0 
                ? Math.round(formatData.reduce((a, b) => a + b.value, 0) / formatData.length)
                : 0}
            </Typography>
            <Typography variant="caption" color="textSecondary">
              Average
            </Typography>
          </Box>
          <Box textAlign="center">
            <Typography variant="h6">
              {Math.max(...formatData.map(d => d.value))}
            </Typography>
            <Typography variant="caption" color="textSecondary">
              Peak
            </Typography>
          </Box>
        </Box>
      </Box>
    </InfoCard>
  );
};
```

### 4. Configuration Example

Configuration file showing how to use custom widgets:

```yaml
# config-with-custom.yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: custom-widgets-example
  title: "Custom Widgets Dashboard"
  subtitle: "Demonstrating custom widget integration"
  version: "1.0.0"

spec:
  widgets:
    # Standard widgets
    github:
      enabled: true
      refreshInterval: 300000
      owner: "Portfolio-jaime"
      filters:
        keywords: ["backstage"]
        maxRepos: 5

    # Custom metrics widget
    customMetrics:
      enabled: true
      refreshInterval: 30000
      title: "Operations Metrics"
      displayOptions:
        showTrends: true
        showAlerts: true

    # Custom API integration widget  
    customAPI:
      enabled: true
      refreshInterval: 300000
      title: "External Service Status"
      apiEndpoint: "https://api.example.com/services"
      apiKey: "${API_KEY}"  # Environment variable
      maxItems: 8
      
    # Custom chart widget
    customChart:
      enabled: true
      refreshInterval: 60000
      title: "Performance Analytics"
      chartType: "line"
      metrics:
        - "requests"
        - "errors" 
        - "latency"
      timeRanges: ["1h", "6h", "24h", "7d"]

  layout:
    grid:
      columns: 12
      spacing: 3
    positions:
      customMetrics:
        xs: 12
        md: 8
      customAPI:
        xs: 12
        md: 4
      customChart:
        xs: 12

  theme:
    primaryColor: "#1976d2"
    secondaryColor: "#ff9800"
    backgroundColor: "#f5f5f5"
```

## 📋 Integration Steps

1. **Create Widget Component**: Follow the examples above
2. **Add to HomePage**: Import and conditionally render the widget
3. **Configure in YAML**: Add widget configuration to dashboard config
4. **Test Integration**: Verify widget loads and functions correctly
5. **Document Usage**: Add documentation for the custom widget

## 🔧 Best Practices

- **Error Handling**: Always handle API failures gracefully
- **Loading States**: Show appropriate loading indicators
- **Responsive Design**: Ensure widgets work on all screen sizes
- **Theme Integration**: Use Material-UI theme variables
- **Performance**: Implement appropriate refresh intervals
- **Configuration**: Make widgets configurable via YAML

## 📚 Additional Resources

- [React Component Development](https://reactjs.org/docs/components-and-props.html)
- [Material-UI Components](https://mui.com/components/)
- [Recharts Documentation](https://recharts.org/en-US/)
- [Backstage Plugin Development](https://backstage.io/docs/plugins/)