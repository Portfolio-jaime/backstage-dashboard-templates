# 📖 Documentación Completa - Sistema de Dashboards Múltiples

## 🏗️ **Arquitectura del Sistema**

### **Repositorios Separados:**
- **`backstage-app-devc`** - Aplicación Backstage principal
- **`backstage-dashboard-templates`** - Configuraciones de dashboards (fuente de verdad)

### **Flujo de Datos:**
```
GitHub Repo (backstage-dashboard-templates) 
    ↓ (fetch cada 5 min)
Hook (useDashboardConfig.ts) 
    ↓ (parse YAML)
Dashboard Selector 
    ↓ (switch template)
HomePage + Widgets
```

---

## 📁 **Estructura de Archivos**

### **Repositorio: `backstage-dashboard-templates`**
```
backstage-dashboard-templates/
├── registry.yaml                           # Índice de dashboards
├── templates/
│   ├── ba-devops-dashboard/
│   │   └── config.yaml                     # Configuración DevOps
│   ├── ba-platform-dashboard/
│   │   └── config.yaml                     # Configuración Platform
│   ├── ba-security-dashboard/
│   │   └── config.yaml                     # Configuración Security
│   ├── ba-management-dashboard/
│   │   └── config.yaml                     # Configuración Management
│   └── ba-developer-dashboard/
│       └── config.yaml                     # Configuración Developer
└── README.md
```

### **Repositorio: `backstage-app-devc`**
```
backstage/packages/app/src/
├── hooks/
│   └── useDashboardConfig.ts               # Hook principal
├── components/home/
│   ├── HomePage.tsx                        # Página principal
│   ├── DashboardSelector.tsx               # Selector de dashboards
│   └── widgets/
│       ├── TeamActivity.tsx                # Widget GitHub dinámico
│       ├── WorldClock.tsx                  # Widget reloj mundial
│       └── LiveCatalogServices.tsx         # Widget catálogo
```

---

## 🆕 **1. CREAR UN NUEVO DASHBOARD**

### **Paso 1: Crear estructura en repositorio**
```bash
# En backstage-dashboard-templates
cd templates/
mkdir ba-nuevodashboard-dashboard/
cd ba-nuevodashboard-dashboard/
```

### **Paso 2: Crear config.yaml**
```yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: ba-nuevodashboard-dashboard
  title: "Mi Nuevo Dashboard"
  subtitle: "Descripción del dashboard"
  version: "1.0.0"
  author: "Tu Nombre <tu.email@ba.com>"
  
spec:
  widgets:
    # Solo widgets reales - NO simular
    github:
      enabled: true
      refreshInterval: 300000
      dynamicRepos: true
      owner: "Portfolio-jaime"
      filters:
        keywords: ["palabra1", "palabra2", "palabra3"]
        topics: ["topic1", "topic2"]
        exclude: ["archived", "private"]
        sortBy: "updated"
        maxRepos: 8
      
    catalog:
      enabled: true
      refreshInterval: 120000
      maxServices: 8
      
    worldClock:
      enabled: true
      refreshInterval: 1000
      timezones:
        - name: "Londres"
          timezone: "Europe/London"
          flag: "🇬🇧"
    
    # Deshabilitar widgets sin API
    flightOps:
      enabled: false
      reason: "No real API available"
    
    costDashboard:
      enabled: false
      reason: "No real API available"
      
    security:
      enabled: false
      reason: "No real API available"
      
    systemHealth:
      enabled: false
      reason: "No real API available"

  layout:
    grid:
      columns: 12
      spacing: 3
    
  theme:
    primaryColor: "#1976d2"
    secondaryColor: "#ff9800"
    backgroundColor: "#f5f5f5"
    cardElevation: 2
    borderRadius: 8
```

### **Paso 3: Actualizar registry.yaml**
```yaml
# Añadir al final de templates:
  - id: ba-nuevodashboard
    name: Mi Nuevo Dashboard
    description: Descripción breve del dashboard
    category: Nueva Categoría
    configPath: templates/ba-nuevodashboard-dashboard/config.yaml
    icon: "🎯"
    target: nuevocategoria
    tags:
      - tag1
      - tag2
```

### **Paso 4: Commit y Push**
```bash
git add .
git commit -m "feat: add nuevo dashboard

- Add ba-nuevodashboard configuration
- Focus on [describe purpose]
- Configured for [target audience]

Author: Tu Nombre <tu.email@ba.com>"
git push
```

### **Paso 5: Verificar en Backstage**
- Esperar 5 minutos (auto-refresh)
- O recargar página
- Nuevo dashboard aparece en selector

---

## ✏️ **2. MODIFICAR DASHBOARD EXISTENTE**

### **Paso 1: Localizar configuración**
```bash
cd backstage-dashboard-templates/templates/ba-devops-dashboard/
nano config.yaml
```

### **Paso 2: Realizar cambios**
**Ejemplos comunes:**

**Cambiar repositorios GitHub:**
```yaml
github:
  filters:
    keywords: ["nuevapalabra", "terraform", "k8s"]
    topics: ["devops", "nuevotopic"]
    maxRepos: 12  # Cambiar cantidad
```

**Añadir zona horaria:**
```yaml
worldClock:
  timezones:
    - name: "Tokio"
      timezone: "Asia/Tokyo" 
      flag: "🇯🇵"
```

**Cambiar tema:**
```yaml
theme:
  primaryColor: "#2e7d32"    # Verde
  secondaryColor: "#ff6f00"  # Naranja
```

### **Paso 3: Commit cambios**
```bash
git add config.yaml
git commit -m "feat: update devops dashboard

- Add Tokyo timezone
- Change theme to green
- Increase max repos to 12

Author: Tu Nombre <tu.email@ba.com>"
git push
```

---

## 🔧 **3. EDITAR WIDGETS Y FUNCIONALIDAD**

### **Widgets Disponibles y Estados:**

| Widget | Estado | Descripción |
|--------|--------|-------------|
| `github` | ✅ **Funcional** | GitHub Activity dinámico |
| `catalog` | ✅ **Funcional** | Servicios de Backstage |
| `worldClock` | ✅ **Funcional** | Relojes mundiales |
| `flightOps` | ❌ **Deshabilitado** | Sin API real |
| `costDashboard` | ❌ **Deshabilitado** | Sin API real |
| `security` | ❌ **Deshabilitado** | Sin API real |
| `systemHealth` | ❌ **Deshabilitado** | Sin API real |

### **Configuraciones GitHub Dinámicas:**

**Por Keywords:**
```yaml
github:
  filters:
    keywords: ["backstage", "k8s", "terraform", "api", "service"]
```

**Por Topics:**
```yaml
github:
  filters:
    topics: ["kubernetes", "devops", "microservices"]
```

**Exclusiones:**
```yaml
github:
  filters:
    exclude: ["archived", "private", "fork"]
```

### **Configuración de Zonas Horarias:**
```yaml
worldClock:
  timezones:
    - name: "London HQ"
      timezone: "Europe/London"
      flag: "🇬🇧"
      primary: true
    - name: "New York"
      timezone: "America/New_York"  
      flag: "🇺🇸"
      primary: false
```

### **Configuración del Catálogo:**
```yaml
catalog:
  enabled: true
  refreshInterval: 120000
  maxServices: 8
  filters:
    kinds: ["Component", "API", "Resource"]
  displayOptions:
    showHealthMetrics: true
    showUptime: true
```

---

## 🔄 **4. ACTUALIZAR SISTEMA BACKSTAGE**

### **Modificar Hook (useDashboardConfig.ts):**

**Cambiar owner GitHub:**
```typescript
const loadDynamicRepos = async () => {
  const owner = 'TU-GITHUB-USERNAME';  // Cambiar aquí
  const response = await fetch(`https://api.github.com/users/${owner}/repos?sort=updated&per_page=20`);
  // ...
};
```

**Cambiar filtros por defecto:**
```typescript
return topics.some((topic: string) => 
  ['backstage', 'devops', 'TU-TOPIC'].includes(topic)  // Añadir topics
) || 
['backstage', 'TU-KEYWORD'].some(keyword =>             // Añadir keywords
  name.includes(keyword)
);
```

### **Modificar Selector (DashboardSelector.tsx):**

**Cambiar estilos:**
```typescript
const useStyles = makeStyles((theme) => ({
  container: {
    backgroundColor: theme.palette.background.paper,
    // Personalizar estilos
  },
}));
```

### **Modificar HomePage:**

**Cambiar layout:**
```typescript
<Grid item xs={12} md={6}>  {/* Cambiar tamaños de grid */}
  <LiveCatalogServices />
</Grid>
```

---

## 📋 **5. WORKFLOWS COMPLETOS**

### **🔄 Workflow: Cambio Rápido**
```bash
# 1. Editar configuración
cd backstage-dashboard-templates/templates/ba-devops-dashboard/
nano config.yaml

# 2. Hacer cambios (ej: añadir timezone)

# 3. Commit
git add config.yaml
git commit -m "feat: add Tokyo timezone to devops dashboard"
git push

# 4. Verificar en Backstage (5 min o reload)
```

### **🆕 Workflow: Nuevo Dashboard Completo**
```bash
# 1. Crear estructura
mkdir templates/ba-marketing-dashboard/
cd templates/ba-marketing-dashboard/

# 2. Crear config.yaml (usar template base)

# 3. Actualizar registry.yaml
cd ../..
nano registry.yaml

# 4. Commit todo
git add .
git commit -m "feat: add marketing dashboard"
git push

# 5. Verificar disponibilidad
```

### **🔧 Workflow: Cambio de Código**
```bash
# 1. Cambiar código Backstage
cd backstage-app-devc/backstage/packages/app/src/hooks/
nano useDashboardConfig.ts

# 2. Testear localmente
yarn start

# 3. Commit si funciona
git add .
git commit -m "feat: improve dynamic repo loading"
git push
```

---

## 🧪 **6. TESTING Y DEBUGGING**

### **Logs del Navegador (F12 → Console):**
```javascript
// Buscar estos logs:
"📋 Fetching dashboard registry from GitHub..."
"✅ Parsed 5 templates from registry"
"🔍 Dynamic repos loaded: [repo1, repo2, ...]"
"📡 Fetching activity for repos: [...]"
```

### **URLs de Testing:**
```
# Registry
https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml

# Config específica
https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/templates/ba-devops-dashboard/config.yaml

# API GitHub
https://api.github.com/users/Portfolio-jaime/repos?sort=updated&per_page=20
```

### **Debugging Común:**

| Problema | Causa | Solución |
|----------|-------|----------|
| Dashboard no aparece | Error en registry.yaml | Validar YAML syntax |
| Repos no cargan | GitHub API rate limit | Esperar o añadir token |
| Widgets no aparecen | enabled: false | Verificar configuración |
| Tema oscuro roto | CSS overrides | Usar Material-UI theme |

---

## 📝 **7. TEMPLATES Y EJEMPLOS**

### **Template Base Dashboard:**
```yaml
apiVersion: backstage.io/v1beta1
kind: DashboardConfig
metadata:
  name: ba-template-dashboard
  title: "Template Dashboard"
  subtitle: "Base template para nuevos dashboards"
  version: "1.0.0"
  
spec:
  widgets:
    github:
      enabled: true
      refreshInterval: 300000
      dynamicRepos: true
      owner: "Portfolio-jaime"
      filters:
        keywords: ["CAMBIAR"]
        topics: ["CAMBIAR"]
        maxRepos: 8
        
    catalog:
      enabled: true
      refreshInterval: 120000
      maxServices: 8
      
    worldClock:
      enabled: true
      refreshInterval: 1000
      timezones:
        - name: "London"
          timezone: "Europe/London"
          flag: "🇬🇧"
    
    # Deshabilitar widgets simulados
    flightOps:
      enabled: false
      reason: "No real API available"
    costDashboard:
      enabled: false  
      reason: "No real API available"
    security:
      enabled: false
      reason: "No real API available"
    systemHealth:
      enabled: false
      reason: "No real API available"

  theme:
    primaryColor: "#1976d2"
    secondaryColor: "#ff9800"
    backgroundColor: "#f5f5f5"
    cardElevation: 2
    borderRadius: 8
```

### **Entry en Registry:**
```yaml
  - id: ba-template
    name: Template Dashboard
    description: Base template for new dashboards
    category: Template
    configPath: templates/ba-template-dashboard/config.yaml
    icon: "📋"
    target: template
    tags:
      - template
      - base
```

---

## ⚠️ **8. MEJORES PRÁCTICAS**

### **✅ DO:**
- Usar solo widgets con datos reales
- Mantener registry.yaml actualizado
- Commits descriptivos con Author
- Testear en navegador después de cambios
- Usar Material-UI theme variables
- Filtrar repos por keywords/topics relevantes

### **❌ DON'T:**
- Hardcodear listas de repositorios
- Habilitar widgets sin APIs reales
- Commitear sin probar
- Usar colores hardcoded (tema oscuro)
- Duplicar títulos en UI
- Sobrescribir estilos de Material-UI

### **🔒 Seguridad:**
- No exponer tokens en configuraciones
- Usar API pública de GitHub
- No commitear credentials
- Validar YAML antes de push

---

## 🚀 **9. COMANDOS ÚTILES**

### **Git:**
```bash
# Status rápido
git status --porcelain

# Commit con template
git commit -m "feat: [action] [component]

- [change 1]  
- [change 2]

Author: Jaime Henao <jaime.andres.henao.arbelaez@ba.com>"

# Ver cambios
git diff HEAD~1
```

### **YAML Validation:**
```bash
# Instalar yamllint
pip install yamllint

# Validar archivo
yamllint config.yaml
```

### **Testing APIs:**
```bash
# Test GitHub API
curl -s "https://api.github.com/users/Portfolio-jaime/repos?sort=updated&per_page=5" | jq '.[] | .name'

# Test registry
curl -s "https://raw.githubusercontent.com/Portfolio-jaime/backstage-dashboard-templates/main/registry.yaml"
```

---

## 📞 **10. TROUBLESHOOTING**

### **Dashboard no carga:**
1. Verificar registry.yaml sintaxis
2. Comprobar URLs en logs del navegador
3. Validar estructura de carpetas
4. Esperar 5 minutos (cache)

### **Repos no aparecen:**
1. Verificar owner en filtros
2. Comprobar keywords/topics
3. Revisar rate limits GitHub API
4. Ver logs en consola del navegador

### **Tema oscuro se ve mal:**
1. Usar variables de Material-UI theme
2. No hardcodear colores
3. Testear en ambos temas

### **Widget duplicado:**
1. Verificar enabled: false en configs
2. Comprobar títulos duplicados
3. Revisar imports en componentes

---

## 🎯 **11. DASHBOARDS ACTUALES**

### **🚀 BA DevOps Dashboard**
- **Propósito**: Operaciones y despliegues
- **Repositorios**: backstage, devops, k8s, terraform, gitops
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Usuario objetivo**: Equipos DevOps

### **⚙️ BA Platform Engineering**
- **Propósito**: Infraestructura y plataforma
- **Repositorios**: kubernetes, infrastructure, helm, cluster
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Usuario objetivo**: Ingenieros de plataforma

### **🔒 BA Security Dashboard**
- **Propósito**: Seguridad y compliance
- **Repositorios**: security, compliance, audit, vulnerability
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Usuario objetivo**: Equipo de seguridad

### **📊 BA Executive Dashboard**
- **Propósito**: Métricas ejecutivas
- **Repositorios**: strategic, executive, reports
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Usuario objetivo**: Liderazgo ejecutivo

### **💻 BA Developer Dashboard**
- **Propósito**: Herramientas de desarrollo
- **Repositorios**: api, microservice, tools, templates
- **Widgets**: GitHub Activity, Catalog, World Clock
- **Usuario objetivo**: Desarrolladores

---

## 📈 **12. ROADMAP Y FUTURAS MEJORAS**

### **🔮 Próximas Funcionalidades:**
- [ ] Integración con APIs reales de BA
- [ ] Métricas de rendimiento de aplicaciones
- [ ] Dashboard personalizable por usuario
- [ ] Notificaciones push
- [ ] Exportación de reportes
- [ ] Integración con Slack/Teams
- [ ] Métricas de costos reales
- [ ] Health checks automatizados

### **🛠️ Mejoras Técnicas:**
- [ ] Cache inteligente de datos
- [ ] Optimización de rendimiento
- [ ] Tests automatizados
- [ ] CI/CD para configuraciones
- [ ] Validación automática de YAML
- [ ] Monitoreo de uptime

---

## 📧 **13. CONTACTO Y SOPORTE**

### **Mantenedor Principal:**
- **Nombre**: Jaime Henao
- **Email**: jaime.andres.henao.arbelaez@ba.com
- **GitHub**: Portfolio-jaime

### **Repositorios:**
- **Configuraciones**: https://github.com/Portfolio-jaime/backstage-dashboard-templates
- **Aplicación**: https://github.com/Portfolio-jaime/backstage-app-devc

### **Issues y Requests:**
- Reportar bugs en GitHub Issues
- Solicitar nuevos dashboards vía Pull Request
- Mejoras y sugerencias bienvenidas

---

**📅 Última actualización**: 11 Septiembre 2025  
**📋 Versión**: 1.0.0  
**🎯 Estado**: Producción estable

¡Con esta documentación completa puedes crear, modificar y mantener el sistema de dashboards múltiples de forma eficiente! 🚀