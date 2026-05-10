# B24 Pipeline Monitor

Dashboard standalone para monitorear el pipeline diario de Brand24
(13 steps × 4 clientes P/M/L/A, dos slots: `main` 02:00 y `adorni` 04:00 AR).

Single-file HTML/CSS/JS, sin build, sin framework. Pensado para hostear en
GitHub Pages y consumir el endpoint `GET /api/v1/pipeline-status` de
`b24_api.py`.

## Archivos

- `index.html` — el dashboard completo (CSS + JS embebidos).
- `config.js`  — define `window.B24_API_BASE` (URL del backend) y
  `window.B24_POLL_MS` (intervalo de polling).
- `README.md`  — este archivo.

## Setup

1. Editar `config.js`:
   ```js
   window.B24_API_BASE = "https://<cloudflared-tunnel>.trycloudflare.com";
   window.B24_POLL_MS  = 60000; // 60s
   ```
2. Push a `main`. GitHub Pages publica en
   `https://ctiraferri.github.io/warroomingesta/` ~30s después.

## Endpoint backend

`GET /api/v1/pipeline-status?days=30&name=all`

- `days`: 1–90, default 30.
- `name`: `main` | `adorni` | `all`, default `all`.
- Sin auth. CORS ya cubre `*.github.io` en `b24_api.py`.

Devuelve `runs[]` ordenado newest-first, cada run con `clients[].steps[]` en el
orden canónico de `b24_pipeline.STEPS`.

## Vista

- **Header**: filtros [ALL · MAIN · ADORNI], last-refreshed dot, botón refresh manual.
- **KPI band**: last run / success rate 7d / avg duration 7d / next scheduled.
- **Timeline feed**: cada run es una `<details>` card. Header siempre visible
  con 4 mini-barras (P/M/L/A) de 13 celdas. Click expande con tabla por step
  + `error_tail` en `<pre>` si hubo fallo.

Polling cada 60s, pausado cuando el tab está oculto, retomado on visibilitychange.

## V2 (out of scope ahora)

- Per-step counts (mentions:N, sentiment verde/rojo:N, engagement ok/error:N) parseados desde logs.
- Columna `name` en `pipeline_runs` (eliminar inferencia P/M/L vs A).
- Heatmap calendario alternativo.
- Drill-through a archivos de log (requeriría endpoint autenticado).
