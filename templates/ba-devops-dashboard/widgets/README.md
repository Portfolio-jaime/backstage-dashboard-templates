# Widget Configurations

Este directorio contiene configuraciones específicas para cada widget del dashboard.

## Estructura

Cada widget puede tener su propia configuración detallada aquí:

- `github-config.yaml` - Configuración específica para GitHub widget
- `catalog-config.yaml` - Configuración para Backstage Catalog widget
- `flight-ops-config.yaml` - Configuración para Flight Operations widget

## Uso

Estas configuraciones pueden referenciar desde el config.yaml principal:

```yaml
spec:
  widgets:
    github:
      $ref: "./widgets/github-config.yaml"
```

## Validación

Cada configuración debe seguir el schema definido en `/schemas/dashboard-schema.json`