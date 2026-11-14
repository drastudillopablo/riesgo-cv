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
