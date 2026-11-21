# 🏗️ PROPUESTA DE ARQUITECTURA - PRIMER PASO

**Fecha**: 7 de febrero de 2026  
**Para**: Luinux81  
**Documento**: Decisión arquitectónica y setup inicial

---

## 🎯 RESUMEN EJECUTIVO

Basado en la auditoría completa, te propongo una **arquitectura híbrida con Monorepo + Git Submodules** que te permitirá:

✅ Trabajar de forma eficiente con todos los paquetes  
✅ Mantener repos individuales para Packagist  
✅ Facilitar el desarrollo y testing integrado  
✅ Preservar la flexibilidad de instalación independiente  

---

## 🏛️ ARQUITECTURA PROPUESTA DETALLADA

### Visión General

```shell
┌─────────────────────────────────────────────────────────────┐
│                 GITHUB REPOSITORIES                          │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  1. lbcdev-filament-maps-suite (MONOREPO PRINCIPAL)         │
│     ├── packages/                                            │
│     │   ├── core/          [submodule → repo 2]             │
│     │   ├── geometries/    [submodule → repo 3]             │
│     │   ├── fields/        [submodule → repo 4]             │
│     │   └── widgets/       [submodule → repo 5]             │
│     ├── docs/                                                │
│     ├── examples/                                            │
│     └── tests/             [tests integrados]               │
│                                                              │
│  2. livewire-maps-core (INDEPENDIENTE)                      │
│     ├── src/                                                 │
│     ├── resources/                                           │
│     └── tests/                                               │
│                                                              │
│  3. map-geometries (INDEPENDIENTE)                          │
│     ├── src/                                                 │
│     └── tests/                                               │
│                                                              │
│  4. filament-maps-fields (INDEPENDIENTE)                    │
│     ├── src/                                                 │
│     ├── resources/                                           │
│     └── tests/                                               │
│                                                              │
│  5. filament-maps-widgets (INDEPENDIENTE)                   │
│     ├── src/                                                 │
│     ├── resources/                                           │
│     └── tests/                                               │
│                                                              │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    PACKAGIST PACKAGES                        │
├─────────────────────────────────────────────────────────────┤
│                                                              │
│  - lbcdev/filament-maps-suite         [meta-package]        │
│  - lbcdev/livewire-maps-core                                │
│  - lbcdev/map-geometries                                    │
│  - lbcdev/filament-maps-fields                              │
│  - lbcdev/filament-maps-widgets                             │
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

### Ventajas de esta Arquitectura

#### ✅ Para Ti (Desarrollador)

- **Un solo workspace**: Trabajas con todos los paquetes abiertos en VS Code
- **Tests integrados**: Puedes testear interacciones entre paquetes
- **Commits atómicos**: Cambios en múltiples paquetes en un solo commit del monorepo
- **Documentación centralizada**: Un solo sitio de docs para todo
- **DevContainer único**: Setup de desarrollo instantáneo

#### ✅ Para Usuarios Finales

- **Instalación flexible**:

  ```bash
  # Instalar todo
  composer require lbcdev/filament-maps-suite
  
  # O solo lo que necesitan
  composer require lbcdev/filament-maps-fields
  ```

- **Versionado independiente**: Cada paquete tiene su propio changelog
- **Actualizaciones granulares**: Pueden actualizar solo un paquete si quieren

#### ✅ Para Packagist

- **5 paquetes independientes**: Cada uno con su propio repo y tags
- **Búsqueda orgánica**: Usuarios pueden encontrar los paquetes por separado
- **Meta-package**: Instalación conveniente del suite completo

---

## 📁 ESTRUCTURA DE DIRECTORIOS DETALLADA

### Repo Principal: lbcdev-filament-maps-suite

```shell
lbcdev-filament-maps-suite/
│
├── .devcontainer/
│   ├── devcontainer.json          # Configuración VS Code Dev Container
│   ├── docker-compose.yml         # Servicios: PHP, PostgreSQL, Redis
│   └── Dockerfile                 # Imagen custom si es necesario
│
├── .github/
│   ├── workflows/
│   │   ├── test-core.yml          # CI para el paquete core
│   │   ├── test-geometries.yml    # CI para geometries
│   │   ├── test-fields.yml        # CI para fields
│   │   ├── test-widgets.yml       # CI para widgets
│   │   ├── test-integration.yml   # Tests entre todos los paquetes
│   │   └── release.yml            # Automatizar releases
│   └── ISSUE_TEMPLATE/
│       ├── bug_report.md
│       └── feature_request.md
│
├── .vscode/
│   ├── settings.json              # Settings compartidos
│   ├── extensions.json            # Extensiones recomendadas
│   └── launch.json                # Debug config
│
├── packages/                      # ← GIT SUBMODULES
│   ├── core/                      # → github.com/LBCDev-Libs/livewire-maps-core
│   ├── geometries/                # → github.com/LBCDev-Libs/map-geometries
│   ├── fields/                    # → github.com/LBCDev-Libs/filament-maps-fields
│   └── widgets/                   # → github.com/LBCDev-Libs/filament-maps-widgets
│
├── docs/                          # Documentación unificada (VitePress)
│   ├── .vitepress/
│   │   └── config.js
│   ├── index.md                   # Home
│   ├── getting-started/
│   │   ├── installation.md
│   │   ├── quickstart.md
│   │   └── configuration.md
│   ├── core-concepts/
│   │   ├── architecture.md
│   │   ├── livewire-component.md
│   │   └── geometries.md
│   ├── fields/
│   │   ├── map-field.md
│   │   ├── bounds-field.md
│   │   └── multi-marker-field.md
│   ├── widgets/
│   │   ├── map-widget.md
│   │   └── clustering.md
│   ├── advanced/
│   │   ├── actions.md
│   │   ├── custom-tiles.md
│   │   └── geocoding.md
│   └── api/                       # API reference generada
│
├── examples/                      # Proyectos Laravel de ejemplo
│   ├── basic-usage/
│   │   ├── app/
│   │   ├── composer.json
│   │   └── README.md
│   ├── advanced-features/
│   └── real-world-app/
│
├── tests/                         # Tests de integración entre paquetes
│   ├── Integration/
│   │   ├── CoreAndFieldsTest.php
│   │   ├── AllPackagesTest.php
│   │   └── FilamentIntegrationTest.php
│   └── TestCase.php
│
├── .editorconfig
├── .gitignore
├── .gitmodules                    # Configuración de submodules
├── ARCHITECTURE.md                # Este documento
├── CHANGELOG.md                   # Changelog del suite completo
├── CODE_OF_CONDUCT.md
├── CONTRIBUTING.md                # Guía de contribución
├── LICENSE                        # MIT
├── README.md                      # README principal del suite
├── composer.json                  # Meta-package
└── lbcdev-maps-suite.code-workspace  # VS Code Workspace
```

## 🛠️ Estructura del Proyecto (Monorepo)

A continuación se detallan los archivos de configuración principales y sus respectivos accesos en el repositorio:

### 📄 Archivos de Configuración Core

| Componente | Descripción | Enlace al Archivo |
| --- | --- | --- |
| **composer.json** | Definición del Meta-package y dependencias. | [Ver en Github](https://www.google.com/search?q=%5Bhttps://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/blob/main/composer.json%5D(https://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/blob/main/composer.json)) |
| **Git Submodules** | Configuración de los submódulos del repositorio. | [Ver en Github](https://www.google.com/search?q=%5Bhttps://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/blob/main/.gitmodules%5D(https://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/blob/main/.gitmodules)) |

---

### 💻 Entorno de Desarrollo

#### **VS Code Workspace**

Configuración personalizada para el espacio de trabajo en VS Code, optimizando la gestión del monorepo. 💡 [Ver en Github](https://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/blob/main/lbcdev-maps-suite.code-workspace)

#### **DevContainer Configuration**

Entorno de desarrollo contenedorizado para garantizar que todos los colaboradores tengan las mismas dependencias (PHP, Extensiones, etc.). 💡 [Ver en Github](https://github.com/LBCDev-Libs/lbcdev-filament-maps-suite/tree/main/.devcontainer)

---

## 🚀 PASOS DE IMPLEMENTACIÓN

### Semana 1: Setup Inicial (5-7 días)

#### ~~Día 1: Preparación~~

#### ~~Día 2: Crear Estructura de Repos~~

#### ~~Día 3: Setup del Monorepo~~

#### ~~Día 4: Configurar DevContainer~~

#### ~~Día 5: Migrar Código del Core~~

#### ~~Día 6: Crear Paquete de Geometrías~~

- Extraer clases de geometría existentes de webbingbrasil
- (copiar y adaptar Marker, Polyline, etc.)
- Dejar para mas adelante, por ahora solo Marker y MarkerCollection

## 📋 CHECKLIST DE MIGRACIÓN

### Setup Infraestructura ✅

- [X] Crear repo principal `lbcdev-filament-maps-suite`
- [X] Renombrar `livewire-lbcdev-component-map` → `livewire-maps-core`
- [X] Crear repo `map-geometries`
- [X] Renombrar (opcional) `filament-lbcdev-map-field` → `filament-maps-fields`
- [X] Crear repo `filament-maps-widgets`
- [X] Configurar Git submodules
- [X] Crear DevContainer
- [X] Crear VS Code Workspace
- [X] Configurar CI/CD básico

### Migración de Código ✅

#### Core (packages/core)

- [X] Actualizar namespaces
- [X] Renombrar clases principales
- [X] Refactorizar para multi-marker
- [X] Actualizar tests
- [X] Actualizar README

#### Geometrías (packages/geometries)

- [X] Crear interfaces base
- [X] Implementar clase `Marker`
- [ ] Implementar clase `Polyline`
- [ ] Implementar clase `Polygon`
- [ ] Implementar clase `Circle`
- [ ] Implementar clase `Rectangle`
- [ ] Tests unitarios para cada geometría
- [ ] Documentar API
- [ ] Tag `v1.0.0-beta.1`

#### Fields (packages/fields)

- [ ] Actualizar dependencia a core v2.0
- [ ] Actualizar dependencia a geometries v1.0
- [ ] Refactorizar MapField con nuevas geometrías
- [ ] Implementar MapMultiMarkerField
- [ ] Actualizar tests
- [ ] Tag `v2.0.0-beta.1`

#### Widgets (packages/widgets)

- [ ] Crear widget base
- [ ] Implementar MapWidget
- [ ] Integrar clustering
- [ ] Sistema de acciones
- [ ] Tests
- [ ] Tag `v1.0.0-beta.1`

### Documentación ✅

- [ ] Setup VitePress en `/docs`
- [ ] Escribir Getting Started
- [ ] Documentar cada componente
- [ ] Crear ejemplos prácticos
- [ ] Generar API reference
- [ ] Deploy docs (GitHub Pages o Netlify)

### Testing ✅

- [ ] Tests unitarios (80%+ coverage)
- [ ] Tests de integración
- [ ] Tests E2E con Dusk (opcional)
- [ ] CI passing en todos los repos

### Release ✅

- [ ] Crear CHANGELOGs
- [ ] Tag estable en cada paquete
- [ ] Publicar en Packagist
- [ ] Anuncio en Filament Discord
- [ ] Anuncio en Twitter/LinkedIn

---

## 🎯 RESULTADO ESPERADO

Después de seguir este plan, tendrás:

✅ **5 repositorios en GitHub**:

- lbcdev-filament-maps-suite (monorepo principal)
- livewire-maps-core (componente base)
- map-geometries (clases de geometría)
- filament-maps-fields (campos de formulario)
- filament-maps-widgets (widgets de dashboard)

✅ **5 paquetes en Packagist**:

- Instalables independientemente
- Con versionado semántico
- Documentación completa

✅ **Entorno de Desarrollo Profesional**:

- DevContainer listo para usar
- VS Code Workspace configurado
- CI/CD automatizado
- Tests funcionando

✅ **Documentación de Primera Clase**:

- Sitio web con VitePress
- API reference completa
- Ejemplos prácticos
- Guías paso a paso

✅ **Base Sólida para Crecimiento**:

- Arquitectura escalable
- Fácil de mantener
- Preparado para colaboradores

---

### Mantén el Enfoque

- 🎯 Primero: infraestructura y setup
- 🎯 Segundo: funcionalidad core
- 🎯 Tercero: features avanzadas
- 🎯 Cuarto: documentación y pulido
