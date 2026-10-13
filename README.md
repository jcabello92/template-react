# Plantilla Docker - React (Frontend Web)

Plantilla Docker para Frontend Web optimizada para **React + Vite + TypeScript + Tailwind CSS**.

## Arquitectura

* **Desarrollo**:
  * **app**: Node.js 26 (Alpine) ejecutando Vite Dev Server (`--host 0.0.0.0:5173`) con Hot Module Replacement (HMR).
  * **caddy**: Proxy inverso local escuchando en el puerto 80 y reenviando el tráfico hacia `app:5173`.
* **Producción**:
  * Multi-stage build que compila los estáticos (`npm run build`) y los sirve a través de un contenedor ligero de Caddy con compresión (`gzip`, `zstd`) y fallback SPA (`try_files {path} /index.html`).

## Requisitos previos

Crear la red externa de Docker definida en `.env` (si aún no existe):

```bash
docker network create template-network
```

## Uso en desarrollo

1. Iniciar los contenedores:

   ```bash
   docker compose up -d
   ```

2. Inicializar el proyecto de React con Vite y TypeScript en `./volume/app/src/`:

   ```bash
   docker compose exec app npm create vite@latest . -- --template react-ts
   ```

3. Instalar Tailwind CSS:

   ```bash
   docker compose exec app npm install tailwindcss @tailwindcss/vite
   ```

   * Añadir `@tailwindcss/vite` a los plugins en `vite.config.ts`.
   * Añadir `@import "tailwindcss";` en tu archivo `src/index.css`.

4. Instalar dependencias e iniciar:

   ```bash
   docker compose exec app npm install
   ```

   El contenedor detectará `package.json` y levantará Vite automáticamente.

## Uso en producción

Construir y levantar la imagen de producción (Caddy sirviendo `/app/dist`):

```bash
docker compose -f docker-compose.prod.yml up -d --build
```
