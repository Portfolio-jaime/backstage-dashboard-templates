# Backstage Dashboard Templates

Este repositorio contiene las configuraciones de dashboard para el portal Backstage de British Airways.

## Estructura

- `templates/ba-devops-dashboard/` - Configuración del dashboard principal de DevOps
- `schemas/` - Esquemas JSON para validación de configuraciones
- `examples/` - Ejemplos de configuración

## Uso

Backstage consume automáticamente estas configuraciones mediante:

```yaml
# En app-config.yaml
dashboard:
  configSource:
    type: github
    repository: Portfolio-jaime/backstage-dashboard-templates
    path: templates/ba-devops-dashboard/config.yaml
```

## Widgets Disponibles

- **FlightOps**: Métricas de operaciones de vuelo
- **TeamActivity**: Actividad del equipo desde GitHub
- **LiveCatalog**: Servicios en tiempo real del catálogo
- **CostDashboard**: Gestión de costos multi-cloud
- **SecurityAlerts**: Alertas de seguridad
- **SystemHealth**: Salud del sistema
- **WorldClock**: Relojes mundiales

## Actualización

Las configuraciones se actualizan automáticamente cada 5 minutos desde este repositorio.

## Estructura de Archivos

```
backstage-dashboard-templates/
├── README.md
├── templates/
│   ├── ba-devops-dashboard/
│   │   ├── config.yaml              # Configuración principal
│   │   ├── widgets/                 # Configuraciones específicas de widgets
│   │   └── layouts/                 # Layouts personalizados
│   └── default-dashboard/           # Dashboard por defecto
├── schemas/
│   └── dashboard-schema.json        # Schema de validación
└── examples/
    └── sample-config.yaml          # Ejemplo de configuración
```

## Versionado

- **v1.0.0** - Configuración inicial con widgets básicos
- Las versiones siguen semantic versioning (semver)
- Cada cambio mayor requiere actualización de versión

## Contribución

1. Crear rama para cambios: `git checkout -b feature/widget-name`
2. Actualizar configuración
3. Validar con schema
4. Crear Pull Request
5. Una vez aprobado, Backstage tomará los cambios automáticamente

---

**Autor:** Jaime Henao - BA DevOps Team  
**Última actualización:** January 2025