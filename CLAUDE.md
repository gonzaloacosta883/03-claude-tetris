# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Resumen

Tetris vanilla: HTML5 Canvas + CSS3 + JavaScript ES6. Sin dependencias, sin build, sin frameworks. Apto para jugar directamente abriendo `index.html` o en un servidor local.

## Ejecutar

```bash
# Opción 1: servidor local (recomendado)
python3 -m http.server 8000
# → http://localhost:8000

# Opción 2: abrir directo
xdg-open index.html  # Linux
open index.html      # macOS
start index.html     # Windows
```

## Arquitectura

Tres archivos, sin dependencias:

- **`index.html`**: DOM básico + dos `<canvas>` (tablero 300×600px, preview 120×120px).
- **`style.css`**: Dark/retro arcade. Flexbox, variables CSS, backdrop-filter.
- **`game.js`**: Toda la lógica (~300 líneas). Motor de juego con loop basado en `requestAnimationFrame`.

## game.js: estructura

| Sección | Responsabilidad |
|---------|-----------------|
| Constantes | `COLS`, `ROWS`, `BLOCK`, `COLORS`, `PIECES`, `LINE_SCORES` |
| Estado global | `board` (matriz), `current`/`next` (piezas), `score`, `lines`, `level`, `paused`, `gameOver` |
| Tablero | `createBoard()`, `lockPiece()`, `clearLines()`, `collide()` |
| Piezas | `randomPiece()`, `spawn()`, `rotateCW()` (transposición+reverso) |
| Colisiones | `collide()` (fuera tablero + solape), `tryRotate()` (wall kicks ±1, ±2) |
| Motor | `loop()` (RAF), `draw()` (canvas), `ghostY()` (proyección) |
| I/O | `keydown` listeners (mover, rotar, drop, pausa) |
| UI | `drawBoard()`, `drawNextPiece()`, `updateScoreUI()`, overlay (pausa/game over) |

## Puntos clave

- **Rotación CW**: transposición matriz + reverso de filas.
- **Ghost piece**: proyecta pieza actual bajando sin colisiones; dibuja con `globalAlpha=0.2`.
- **Wall kicks**: si rotar choca, intenta ±1 y ±2 cols antes de rechazar.
- **Velocidad**: `dropInterval = max(100, 1000 − (level−1)×90)` ms. Sube cada 10 líneas.
- **Scoring**: `LINE_SCORES[n] × level` para n líneas; hard drop +2pts/celda, soft drop +1pt/fila.
- **Game over**: si pieza nueva colisiona al spawner.

## Personalización

Constantes en `game.js`:
- `COLS` (10), `ROWS` (20), `BLOCK` (30): ajusta canvas en `index.html` también.
- `COLORS`: paleta 7 colores.
- `LINE_SCORES`: puntos 1/2/3/4 líneas.
- `dropInterval`: velocidad inicial.

## Controles

| Tecla | Acción |
|-------|--------|
| `←`/`→` | Mover |
| `↑` o `X` | Rotar CW |
| `↓` | Soft drop |
| `Space` | Hard drop |
| `P` | Pausa |
