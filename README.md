# Portafolio Profesional - Estructura, Arquitectura y Buenas Prácticas

Este repositorio contiene el portafolio profesional de **Jordy Calderón (Full Stack Developer)**. El proyecto está construido sobre **Angular 22** aplicando principios de diseño modernos, arquitectura modular limpia, optimización SEO avanzada y localización nativa.

El objetivo de esta guía es documentar detalladamente la arquitectura, las decisiones de diseño y los flujos de trabajo técnicos implementados, sirviendo como referencia y ejemplo de buenas prácticas de ingeniería de software.

---

## 🛠️ Stack Tecnológico

- **Framework**: Angular 22 (Standalone Components, Señales para reactividad local, `@defer` para carga diferida).
- **Animaciones**: Anime.js v4 (efecto staggered y animaciones elásticas premium).
- **Estilos**: Vanilla CSS/SCSS con un sistema de diseño basado en **Glassmorphism / Acrílico translúcido** y variables HSL personalizadas.
- **Localización**: `@angular/localize` nativo (Soporte completo bilingüe Español/Inglés).
- **CI/CD**: GitHub Actions para despliegue automatizado y versionado.

---

## 📂 1. Estructura de Directorios (Feature-Based Architecture)

El proyecto sigue una arquitectura **basada en características (Feature-Based)**, donde cada módulo autocontenido o sección de la página se agrupa en su propia carpeta bajo `features/`. Esto reduce el acoplamiento y facilita la escalabilidad.

```text
src/
├── app/
│   ├── features/                          # Secciones autocontenidas de la aplicación
│   │   ├── about/                         # Sección "Sobre mí" y Consola interactiva
│   │   │   ├── about.html
│   │   │   ├── about.scss
│   │   │   └── about.ts
│   │   ├── contact/                       # Formulario de contacto integrado con Web3Forms
│   │   ├── education/                     # Línea de tiempo académica y certificaciones
│   │   ├── experience/                    # Trayectoria laboral con colapsables interactivos
│   │   └── skills/                        # Grilla de habilidades técnicas clasificadas
│   │
│   ├── shared/                            # Módulos y utilidades transversales
│   │   ├── components/                    # Componentes globales reutilizables (ej. Loading)
│   │   ├── layout/                        # Estructura del layout principal (Navbar, Footer)
│   │   └── services/                      # Lógica de negocio y servicios globales (ej. CvData)
│   │
│   ├── app.config.ts                      # Proveedores de la aplicación (Providers)
│   ├── app.routes.ts                      # Enrutamiento y redirecciones del SPA
│   ├── app.html                           # Plantilla principal (Orquestador estructural)
│   ├── app.scss                           # Estilos específicos del orquestador raíz
│   └── app.ts                             # Controlador raíz y animaciones generales
│
├── locale/                                # Archivos de traducción XLF (.xlf)
│   ├── messages.xlf                       # Plantilla de traducción base (Español)
│   └── messages.en.xlf                    # Traducciones compiladas en Inglés
│
├── index.html                             # Plantilla HTML base del portafolio
├── index.redirect.html                    # Redireccionador raíz de idioma para producción
├── main.ts                                # Punto de entrada de la aplicación Angular
└── styles.scss                            # Sistema de diseño global (Variables HSL, reset)
```

---

## 🌍 2. Internacionalización Nativa (i18n)

Se ha implementado traducción bilingüe nativa utilizando `@angular/localize`. A diferencia de otras librerías runtime que ralentizan la carga inicial, el compilador de Angular genera **dos compilados completamente independientes** optimizados por idioma.

### Configuración en `angular.json`

El archivo de configuración principal define el idioma de origen como español (`es`) e inglés (`en`) como secundario, apuntando a su catálogo de traducciones:

```json
"i18n": {
  "sourceLocale": "es",
  "locales": {
    "en": "src/locale/messages.en.xlf"
  }
}
```

### Selector de Idioma (Navbar)

El componente `Navbar` inyecta `LOCALE_ID` para conocer el idioma activo y expone un conmutador visual `ES | EN`.
Al hacer clic en el idioma opuesto, el portafolio detecta de forma dinámica la ruta y realiza una redirección segura mediante `window.location.pathname`, preservando el fragmento/hash de la sección actual (e.g. `#experience`) para evitar que el usuario pierda su contexto de navegación.

### Redirección en la Raíz de Producción (`/index.html`)

Dado que Angular compila los archivos dentro de directorios separados (`/es/` y `/en/`), se utiliza un orquestador de redirección en la raíz (`dist/portfolio/browser/index.html`). Este archivo:

1. Inspecciona la configuración regional del navegador del visitante (`navigator.language`).
2. Redirige automáticamente al usuario a la subcarpeta adecuada (`/en/` si su idioma predeterminado es inglés, y `/es/` en cualquier otro caso).
3. Preserva cualquier ruta interna o hash que se haya solicitado en el enlace original.

---

## 🔍 3. Optimización SEO y Metadatos Dinámicos

Para asegurar que los motores de búsqueda y las redes sociales indexen correctamente el sitio en ambos idiomas, se implementó una estrategia híbrida de metadatos:

### Fallback Estático (`src/index.html`)

El archivo HTML base contiene metadatos estáticos listos para indexadores simples y rastreadores de redes sociales (crawlers de WhatsApp, Facebook, LinkedIn, Twitter):

- **Open Graph / Twitter Cards**: Configurados para mostrar la imagen de presentación `avatar_jordy.webp` (ubicada en `public/`) junto a una descripción atractiva al compartir el enlace.
- **Keywords**: Palabras clave bilingües optimizadas para búsqueda de empleo (`programmer`, `desarrollador web`, `full stack`, `freelance`, `n8n`, `angular`, `c#`, `empleo`, `portfolio`).

### Inyección Dinámica Localizada (`app.ts`)

Para la navegación interactiva de los usuarios, el componente raíz `App` utiliza los servicios `Meta` y `Title` de `@angular/platform-browser` para inyectar títulos, palabras clave y descripciones personalizadas a través de `$localize`:

```typescript
private updateMetaTags(): void {
  const siteTitle = $localize`:@@siteTitle:Jordy Calderón | Full Stack Developer`;
  const siteDescription = $localize`:@@siteDescription:Portafolio profesional de Jordy Calderón...`;

  this.titleService.setTitle(siteTitle);
  this.meta.updateTag({ name: 'description', content: siteDescription });
  // Inyección de Open Graph y Keywords traducidas
}
```

Esto garantiza que los usuarios del portafolio vean el título y los metadatos perfectamente traducidos en el navegador web según el idioma activo (`es` / `en`).

---

## 🎨 4. Sistema de Diseño (Glassmorphism & Acrílico)

El portafolio destaca visualmente por un estilo elegante y premium enfocado en la temática de programación:

- **Variables CSS Centralizadas**: Colores, bordes luminosos y sombras dinámicas se definen utilizando HSL para mantener la armonía cromática.
- **Efecto Acrílico**: Tarjetas y componentes utilizan combinaciones de `backdrop-filter: blur(16px)` con bordes semi-transparentes y resplandores degradados.
- **Carga Diferida (`@defer`)**: Las secciones pesadas del portafolio (como las habilidades avanzadas, experiencia e historial educativo) se cargan de forma diferida cuando entran en el viewport del navegador, reduciendo el tamaño del bundle inicial y permitiendo que la web sea interactiva casi al instante.

---

## 🚀 5. Pipelines y Despliegue CI/CD (`deploy.yml`)

El proceso de integración y despliegue continuo (CI/CD) se realiza de forma automática mediante **GitHub Actions** cada vez que se hace un `push` a la rama `main` del repositorio privado:

1.  **Construcción de Producción**: Compila la aplicación generando de forma nativa los directorios de idioma mediante `npm run build:prod`.
2.  **Copias Post-compilación**: Copia el script redireccionador `index.redirect.html` a la raíz de la carpeta de distribución como `index.html`.
3.  **Sincronización Automática**: Clona el repositorio público final (`joordydev.github.io.git`), limpia el contenido antiguo preservando el historial Git, y copia las nuevas carpetas `/es/` y `/en/`.
4.  **Publicación**: Realiza `commit` y sube los cambios actualizados automáticamente, permitiendo que tu web esté siempre al día en producción.

---
