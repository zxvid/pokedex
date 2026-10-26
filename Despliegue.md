
# Despliegue de Pokédex en Azure - Static Web Apps 
<img src="https://www.microsoft.com/favicon.ico"
     alt="Icono de Azure"
     width="80"
     height="80">

## Información del proyecto

- **Aplicación:** Pokédex Angular
- **Repositorio GitHub:** `pokedex` — [añadir aquí la URL del repositorio]
- **Rama de despliegue:** `main`
- **Servicio de Azure:** Azure Static Web Apps
- **Plan:** Free
- **URL pública:** <https://kind-forest-040ab3310.6.azurestaticapps.net/>
- **Carpeta de salida de compilación:** `dist/pokedex-angular`


## Requisitos previos

- Cuenta activa de Azure for Students y acceso al portal de Azure.
- Cuenta de GitHub con el repositorio `pokedex`.
- Node.js y npm instalados para ejecutar y compilar la aplicación localmente.
- Proyecto Angular guardado en el repositorio, junto con `package.json` y `angular.json`.

## Prueba y compilación local

Desde la carpeta del proyecto, donde se encuentra `package.json`, se pueden instalar las dependencias, ejecutar la aplicación y generar los archivos de producción:

```bash
npm install
npm start
npm run build
```

`npm start` inicia el servidor de desarrollo local. La dirección mostrada por Angular CLI suele ser `http://localhost:4200/`. `npm run build` genera los archivos estáticos que se publican en `dist/pokedex-angular`.

Antes del despliegue, comprobé que existiese `dist/pokedex-angular/index.html`. aunque no añadí `node_modules` ni `dist`; Azure instala las dependencias y genera la carpeta de salida durante la compilación.

## Creación del recurso en Azure

1. Iniciar sesión en <https://portal.azure.com/>.
2. Seleccioné **Create a resource** y buscar **Static Web App**.
3. Seleccioné **Create**.
4. En **Basics**, elegí  la suscripción Azure for Students, seleccionar o crear un grupo de recursos y asignar un nombre único al recurso.
5. En **Plan type**, seleccioné la opcion **Free**.
6. En **Deployment details**, seleccioné **GitHub** e iniciar sesión o autorizar Azure si se solicita, en mi caso solicito autorización por ser primera vez.
7. Seleccioné mi Org en github, el repositorio `pokedex` y la rama `main`.
8. En **Build Details**:
   - **Build Preset:** Angular.
   - **App location:** `/`
   - **API location:** Vacio
   - **Output location:** `dist/pokedex-angular`.
9. Comprobé la seleccion de las opciones y le di a **Create**.


## Publicación desde GitHub

Azure conecta el repositorio con un flujo de GitHub Actions. Cada vez que se suben cambios a la rama configurada, el flujo obtiene el código, instala dependencias, compila la aplicación y publica la carpeta de salida.

Para publicar cambios:

1. Guardé los archivos modificados en VS Code.
2. Revisé los cambios en Source Control y crear un commit.
3. Enviar los commits a GitHub con Push o Sync Changes en VSCode.
4. En GitHub, abrir Actions y revisar el flujo Azure Static Web Apps CI/CD.
5. Esperar a que el flujo de despliegue termine correctamente.
6. Abrir la URL pública de Azure y comprobar que la Pokédex en todos sus apartados funcionara.

## Configuración de recursos y rutas para Azure

El proyecto original utiliza `/pokedex-angular/assets/images` para GitHub Pages. Azure sirve el sitio desde la raíz del dominio; por eso, en `src/environments/environment.prod.ts`, la ruta de imágenes debe ser de esta manera:

```ts
imagesPath: '/assets/images',
```

La compilación debe generar los recursos locales bajo `dist/pokedex-angular/assets/images`.

Para configurar encabezados HTTP y rutas de una aplicación Angular, se utiliza `staticwebapp.config.json`. El archivo se mantiene en el código fuente y debe copiarse a la raíz de la carpeta publicada como `dist/pokedex-angular/staticwebapp.config.json`. En `angular.json`, la lista `assets` del objetivo `build` debe incluir `src/staticwebapp.config.json` para copiarlo durante la compilación.

Configuración para Azure Static Web Apps:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/assets/*"]
  },
  "globalHeaders": {
    "Content-Security-Policy": "default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline' https://fonts.googleapis.com; img-src 'self' data: https://raw.githubusercontent.com https://assets.pokemon.com; connect-src 'self' https://beta.pokeapi.co; font-src 'self' data: https://fonts.gstatic.com; object-src 'none'; base-uri 'self'; frame-ancestors 'none'; form-action 'self'; upgrade-insecure-requests",
    "Strict-Transport-Security": "max-age=31536000; includeSubDomains",
    "X-Content-Type-Options": "nosniff",
    "X-Frame-Options": "DENY",
    "Referrer-Policy": "strict-origin-when-cross-origin",
    "Permissions-Policy": "camera=(), microphone=(), geolocation=()"
  }
}
```

Política CSP usado por la aplicación (`beta.pokeapi.co`), los estilos de Google Fonts, las fuentes servidas por Google y las imágenes alojadas en `assets.pokemon.com` y `raw.githubusercontent.com`.


## Comprobaciones posteriores al despliegue

- Abrir la URL pública en una ventana nueva o recargar con `Ctrl+F5`.
- Confirmar que aparece la lista de Pokémon y funcionan los filtros y el buscador.
- Si algo no cargaba, abría las herramientas del navegador con **F12** y revisar **Console** y **Network**. Anotar los errores y corregirlos antes de considerar terminado el despliegue.


## Registro de cambios

| Fecha | Cambio | Verificación |
|---|---|---|
| [21/10/26] | Creación de Azure Static Web App y las conexiones a GitHub | [Completado] |
| [21/10/26] | Configuración o actualización de encabezados y rutas | [Completado] |
| [21/10/26] | Escaneo en SecurityHeaders.com (verificaciones de seguridad) | [Completado] |


> Written with [StackEdit](https://stackedit.io/).
