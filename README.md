# Estructura de Proyecto y Arquitectura (Feature-Based)

Este documento describe la estructura actual del portafolio, analiza el diseño arquitectónico utilizando los principios de **Feature-Based Architecture** (Arquitectura Basada en Características) en Angular 22, y propone recomendaciones para mejorar la escalabilidad, legibilidad y mantenimiento del código.

---

## 1. Estructura Actual del Proyecto

A continuación se presenta el árbol de directorios tal y como está organizado actualmente en `src/app/`:

```text
src/
└── app/
    ├── features/                          # Carpeta que contiene las secciones/características
    │   ├── about/
    │   │   ├── about.html
    │   │   ├── about.scss
    │   │   └── about.ts
    │   ├── contact/
    │   │   ├── contact.html
    │   │   ├── contact.scss
    │   │   └── contact.ts
    │   ├── education/
    │   │   ├── education.html
    │   │   ├── education.scss
    │   │   └── education.ts
    │   ├── experience/
    │   │   ├── experience.html
    │   │   ├── experience.scss
    │   │   └── experience.ts
    │   └── skills/
    │       ├── skills.html
    │       ├── skills.scss
    │       └── skills.ts
    ├── shared/                            # Elementos compartidos
    │   ├── components/                    # Componentes compartidos
    │   │   └── loading/                   # Loading de los @defer
    │   ├── layout/                        # Componentes estructurales de la web
    │   │   ├── footer/
    │   │   └── navbar/
    │   └── services/                      # Servicios globales compartidos
    │      └── cv-data.service.ts
    ├── app.config.ts                      # Configuración de proveedores (providers)
    ├── app.html                           # Plantilla principal (orquestadora)
    ├── app.routes.ts                      # Configuración de rutas (actualmente vacía)
    ├── app.scss                           # Estilos globales/específicos del componente raíz
    ├── app.spec.ts                        # Pruebas del componente raíz
    └── app.ts                             # Componente raíz del portafolio (orquestador)
```

---

Esta arquitectura mantiene el portafolio sumamente limpio y preparado para escalar si en el futuro se agregan nuevas funcionalidades complejas (como un blog, o traducciones multiidioma), haciendo uso óptimo de **Lazy Loading** (carga perezosa) en [app.routes.ts].
