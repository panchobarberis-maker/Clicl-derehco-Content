# Click Derecho — Instrucciones para Claude

## Estructura del repo

- `imagenes/` — Todas las imágenes de fondo: fotos de Francisco Barberis (ChatGPT Image...) y fondos de facultad/estudios (hash .jpg)
- `01-5-errores/`, `02-posicionamiento-organico/`, `03-google-my-business/`, etc. — Una carpeta numerada por carrusel

## Workflow de carruseles

1. Crear el HTML en la carpeta `0N-nombre/`
2. Renderizar como JPGs y **mostrarle al usuario para revisión** — NO subir sin OK
3. Recibir el OK y recién entonces hacer commit + push a main

## Imágenes

- **Fotos de Francisco** (cara): archivos `ChatGPT Image May 22, 2026...png` en `imagenes/`
- **Fondos** (facultad, estudios): archivos con nombre hash `.jpg` en `imagenes/`
- Siempre mezclar ambos tipos en cada carrusel

## ⚠️ ERROR CRÍTICO — Imágenes negras al renderizar

**Problema:** Chrome headless bloquea la carga de imágenes con rutas `file://` relativas (`../imagenes/`). Las imágenes aparecen completamente negras.

**Solución SIEMPRE antes de renderizar:**
1. Levantar servidor HTTP local (el script lo hace automáticamente, pero si falla hacerlo a mano):
   ```bash
   cd /home/user/Clicl-derehco-Content && python3 -m http.server 8765 &
   ```
2. En el script de screenshot, reemplazar las rutas:
   ```javascript
   html = html.replace(/url\('\.\.\/imagenes\//g, `url('http://localhost:8765/imagenes/`);
   html = html.replace(/url\("\.\.\/imagenes\//g, `url("http://localhost:8765/imagenes/`);
   ```
3. **El script `/tmp/screenshot_carousel.js` ya levanta el servidor automáticamente** si detecta que no está corriendo. Si las imágenes siguen negras, matar el proceso y reiniciar:
   ```bash
   pkill -f "http.server 8765"
   cd /home/user/Clicl-derehco-Content && python3 -m http.server 8765 &
   ```

## Opacidad de overlays

Valores correctos para que se vean los fondos con suficiente legibilidad del texto:
```css
.ov-dark {
  background: linear-gradient(to top,
    rgba(10,6,2,0.88) 0%, rgba(10,6,2,0.40) 18%,
    rgba(10,6,2,0.10) 38%, rgba(10,6,2,0.0) 100%);
}
.ov-heavy { background: rgba(10,6,2,0.0); }
```

## Formato de salida

- Siempre JPG, formato 4:5 (1080x1350px)
- Screenshot del elemento `#stage` directamente (no de la página entera)
- Usar Playwright con Chromium en `/opt/pw-browsers/chromium-1194/chrome-linux/chrome`
