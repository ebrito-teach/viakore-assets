# viakore-assets

CDN privado para avatares, marcos, templates de insignias y certificados del ecosistema VIAKORE.

Se sirve via GitHub Pages desde este repo. Todas las URLs tienen el formato:
`https://ebrito-teach.github.io/viakore-assets/{path}`

## Estructura

```
viakore-assets/
├── manifest.json              Catálogo central. Los paneles lo leen para armar los pickers.
├── avatars/
│   ├── free/                  Disponibles para todos (user free, pro, institucionales)
│   ├── pro/                   Solo plan pro e institutional
│   ├── institutional/         Exclusivos para teacher/student de instituciones
│   ├── achievement/           Se desbloquean al ganar badges específicos
│   └── ranking/               Se desbloquean por posición en leaderboard
├── frames/
│   ├── default/               El marco por defecto (user free queda aquí fijo)
│   ├── pro/                   Marcos pro
│   ├── achievement/           Marcos desbloqueables por logros
│   └── ranking/               Marcos exclusivos de top N del ranking
├── badges/
│   └── templates/             Plantillas App Icon para OpenGraph
└── certificates/
    └── templates/             Fondos A4 paisaje para el PDF generado
```

## Convenciones

**Nombres de archivos:**
- Avatares: `a1.png`, `a2.png`, `a3.png`... (numeración global, NO se reinicia por categoría)
- Marcos: `f1.png`, `f2.png`, `f3.png`... (numeración global)

**Formato:**
- PNG con fondo transparente
- Avatares: **256×256 px**
- Marcos: **320×320 px** con centro transparente (donde se verá el avatar)

**Actualización del manifest:**
Cada vez que se agrega/modifica un asset, actualizar `manifest.json` con:
- `id` (debe coincidir con el nombre del archivo sin extensión)
- `name` (label visible al usuario)
- `category` (free | pro | institutional | achievement | ranking)
- `path` (ruta relativa desde el root del repo)
- `unlock` (type + requirement)

## Tipos de unlock

- **default** — disponible para todos
- **plan** — requiere plan específico (`free` | `pro` | `institutional`)
- **achievement** — requiere badge específico (futuro)
- **ranking** — requiere posición en leaderboard (futuro)
- **seasonal** — disponible solo en fechas concretas (futuro)

## URLs finales

Ejemplo con avatar a1:
```
https://ebrito-teach.github.io/viakore-assets/avatars/free/a1.png
```

Ejemplo con marco f2:
```
https://ebrito-teach.github.io/viakore-assets/frames/pro/f2.png
```

## Manifest central

Los paneles de VIAKORE (yoda, sheherezadhe, keiko) descargan `manifest.json`
al inicio de cada sesión para armar los pickers de avatar/marco con lo disponible.

```javascript
const res = await fetch('https://ebrito-teach.github.io/viakore-assets/manifest.json');
const manifest = await res.json();
// manifest.avatars, manifest.frames, manifest.unlock_types
```

El cruce entre lo que existe en el manifest y lo que el user tiene desbloqueado
(tabla `user_unlocks` en Supabase) determina qué avatares/marcos ve en el picker.

## Setup GitHub Pages

Este repo está en modo **privado**. GitHub Pages en repos privados requiere plan
GitHub Pro o Enterprise.

**Opción A — Repo privado con GitHub Pro** (recomendado si ya lo tienes):
1. Settings → Pages → Source: Deploy from branch
2. Branch: `main` / (root)
3. Save
4. URL: `https://ebrito-teach.github.io/viakore-assets/`

**Opción B — Repo público** (gratis):
- Más simple, sirve igual. Los assets son por naturaleza públicos (avatares, marcos). Solo los `certificates/templates/` podrían ser sensibles pero son plantillas vacías sin datos de usuario.
- Recomendado si no tienes GitHub Pro.

**Opción C — Cloudflare Pages con repo privado:**
- Conectar este repo a Cloudflare Pages como proyecto estático
- Dominio custom: `assets.viakore.com` (opcional)
- Sin build command, solo sirve archivos estáticos

Decisión pendiente del usuario (Gash).
