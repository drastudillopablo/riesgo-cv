# Riesgo CV · app web y móvil (v1.6)

Calculadora de riesgo cardiovascular global y objetivos lipídicos (SCORE2 / SCORE2-OP / SCORE2-Diabetes, Lp(a), LDLc · no-HDLc · ApoB, DLCN, imagen, moduladores y propuesta terapéutica). Toda la lógica clínica está en `www/index.html` (tu v1.6, sin cambios en los cálculos: autotest 61/61).

## Estructura
```
riesgo-cv/
├── www/                     ← la app (lo que se publica o se empaqueta)
│   ├── index.html           ← calculadora v1.6 + ajustes de app (zonas seguras, instalación)
│   ├── manifest.webmanifest ← nombre, icono y colores para instalarla
│   ├── sw.js                ← uso sin conexión (caché local; no envía ni guarda datos)
│   └── icons/
├── assets/                  ← icono 1024 px para generar los iconos nativos
├── capacitor.config.json    ← configuración de la app iOS/Android
├── package.json
└── SCORE2_v1.6_original.html  ← copia de seguridad del archivo original
```

## Publicación actual
- Web: https://drastudillopablo.github.io/riesgo-cv/
- Código: https://github.com/drastudillopablo/riesgo-cv (rama `main`); la web se sirve desde la rama `gh-pages`, que contiene solo `www/`.

Para publicar un cambio:
1. Edita los archivos de `www/` y cambia `VERSION` en `www/sw.js`.
2. `git add -A && git commit -m "Descripción del cambio" && git push`
3. `npm run publicar`  (equivale a `git subtree push --prefix www origin gh-pages`). En 1–2 minutos está en línea.

## 1 · App web instalable (PWA) — la vía más rápida
Sube la carpeta `www/` a cualquier hosting estático con **https** (GitHub Pages, Netlify, Cloudflare Pages o el servidor del hospital).
- **iPhone/iPad (Safari):** Compartir → «Añadir a pantalla de inicio».
- **Android (Chrome):** menú ⋮ → «Instalar aplicación».
- Después de la primera visita funciona **sin conexión**. Al publicar una versión nueva, cambia `VERSION` en `www/sw.js`: la app mostrará «Nueva versión disponible · Actualizar».

Prueba local: `python3 -m http.server 8080 --directory www` y abre http://localhost:8080 (abrir el HTML con doble clic también funciona, pero sin modo sin conexión).

## 2 · App nativa iOS / Android (Capacitor)
Requisitos: Node.js 20+ (https://nodejs.org), Xcode (iOS, solo en Mac) y/o Android Studio.
```bash
npm install
npx cap add ios        # y/o: npx cap add android
npm run icons          # genera iconos y pantallas de inicio desde assets/
npm run cap:ios        # abre Xcode → elegir equipo de firma → ▶
npm run cap:android    # abre Android Studio → ▶
```
Tras cada cambio en `www/`: `npx cap sync`.

Antes de publicar en App Store / Google Play:
- Cambia `appId` en `capacitor.config.json` por un identificador tuyo (p. ej. `es.tuhospital.riesgocv`).
- Cuentas de desarrollador: Apple (99 $/año) y Google Play (25 $ una vez).
- Las tiendas exigen política de privacidad (la app no recoge datos: indícalo) y, en Apple, revisan especialmente las apps médicas (guía 1.4.1): conviene declarar que es una herramienta de apoyo para profesionales sanitarios, no un producto sanitario certificado, con las fuentes citadas (ya están en la sección Metodología).
- Si se usa de forma asistencial fuera de tu consulta, valora el marco de producto sanitario (MDR UE 2017/745, software de apoyo a la decisión clínica).

## Validación antes de difundir (v1.7)
En la app: **Metodología → Validación frente a HeartScore**. Hay 20 casos fijos (SCORE2 en las 4 regiones, SCORE2-OP y SCORE2-Diabetes) con el resultado de la herramienta calculado en directo.
1. Introduce cada caso en HeartScore (u otra calculadora oficial de la ESC) y escribe el % obtenido en la columna «Calculadora oficial».
2. La app calcula la diferencia y marca ✔ si es ≤0,5 puntos porcentuales.
3. «Copiar tabla de validación» copia todo en formato Excel (TSV) para documentarlo.

Los valores introducidos se guardan solo en ese navegador. La tabla «Parámetros y fuente de cada uno» (misma sección) documenta el origen de coeficientes, factores de Lp(a), porcentajes de reducción y criterios de financiación.
