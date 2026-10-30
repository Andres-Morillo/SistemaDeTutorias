# Sistema de Tutorías

Aplicación web para la gestión de tutorías, desarrollada con **Vue 3** y **Vite**.

## Características

- Interfaz de página única (SPA) construida con Vue 3.
- Barra lateral (sidebar) de navegación.
- Iconografía para notificaciones, seguridad, contacto y ubicación (campana, candado, correo, mapa y teléfono).
- Entorno de desarrollo con recarga en caliente (Hot Module Replacement).

## Tecnologías

- [Vue 3](https://vuejs.org/)
- [Vite](https://vite.dev/)
- JavaScript (ES Modules)
- npm
- Chat gpt framework
- Claude.ia

## Estructura del proyecto

```
SistemaDeTutorias/
├── README.md
└── sistema-tutorias/
    ├── .vscode/          # Configuración recomendada para VS Code
    ├── public/           # Archivos estáticos
    ├── src/              # Código fuente de la aplicación Vue
    ├── index.html        # Punto de entrada HTML
    ├── package.json      # Dependencias y scripts
    ├── vite.config.js    # Configuración de Vite
    └── jsconfig.json     # Configuración de alias y JavaScript
```

## Requisitos previos

- [Node.js](https://nodejs.org/) (versión LTS recomendada)
- npm (incluido con Node.js)

## Instalación

1. Clona el repositorio:

   ```bash
   git clone https://github.com/Andres-Morillo/SistemaDeTutorias.git
   cd SistemaDeTutorias/sistema-tutorias
   ```

2. Instala las dependencias:

   ```bash
   npm install
   ```

## Uso

### Modo desarrollo

Inicia el servidor de desarrollo con recarga en caliente:

```bash
npm run dev
```

Luego abre en el navegador la URL que muestra la terminal (normalmente `http://localhost:5173`).

### Compilación para producción

Genera la versión optimizada y minificada en la carpeta `dist/`:

```bash
npm run build
```

## Recomendaciones de desarrollo

- **IDE:** VS Code con la extensión [Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar). Desactiva Vetur si lo tienes instalado.
- **Navegador:** instala [Vue.js devtools](https://devtools.vuejs.org/) para depurar la aplicación.

## Personalización

La configuración de Vite se encuentra en `vite.config.js`. Consulta la [referencia de configuración de Vite](https://vite.dev/config/) para más opciones.

## Autor

**Andrés Morillo** - [@Andres-Morillo](https://github.com/Andres-Morillo)

## Contribuidores

**Blazquez Igancio**
**Naiara Palacio**
**Morillo Andres**
