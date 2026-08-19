# AGENTS.md

## Proyecto

Clone de Asteroids en HTML5 Canvas puro. Sin bundler, sin dependencias externas.

**Archivos:**
- `index.html` — shell HTML, solo carga el canvas y `game.js`
- `game.js` — toda la lógica del juego
- `favicon.svg` — ícono

## Ejecutar

```bash
npx serve .
```
Visitar `localhost:3000`. También funciona abriendo `index.html` directamente en el navegador.

## Arquitectura de `game.js`

El archivo está organizado en secciones marcadas con comentarios `// ── Sección ──`. Todas las clases y el estado global viven en el mismo archivo, sin módulos.

**Secciones (en orden):**

1. **Input** — `keys` y `justPressed` para teclado. `pressed(code)` retorna true solo en el frame en que se presionó.
2. **Utils** — `wrap()`, `dist()`, `rand()`, `randInt()`. El wrap es toroidal, se usa en todas las entidades.
3. **Bullet** — `update(dt)` y `draw()`. Tiene `ttl` y `dead`.
4. **Asteroid** — Tamaños 1-3 con arreglos `RADII`, `SPEEDS`, `POINTS`. `split()` retorna 2 asteroides más pequeños. Vértices irregulares generados al azar.
5. **Ship** — `reset()` para reaparecer. Invencibilidad temporal con parpadeo. `tryShoot()` con cooldown. `applySpeed()` activa boost x2 por 5s. `applyShield()` activa escudo protector por 6s.
6. **Particle** — Explosiones, se auto-destruyen con `ttl`.
7. **PowerUp** — Dos tipos: `'speed'` (rayo amarillo, velocidad x2 por 5s) y `'shield'` (hexágono azul, escudo por 6s). Spawning periódico: speed cada 10-18s, shield cada 15-25s. TTL de 8s. Si se recoge otro del mismo tipo estando activo, se reinicia el timer.
8. **Estado del juego** — Variables globales: `ship`, `bullets`, `asteroids`, `particles`, `powerUps`, `score`, `lives`, `level`, `state`, `powerUpTimer`, `shieldPowerUpTimer`.
9. **Update** — Loop de actualización con máquina de estados (`playing`, `dead`, `gameover`). Colisiones bala-asteroide, nave-asteroide y nave-power-up.
10. **Draw** — Renderizado de HUD, overlays y entidades.
11. **Loop principal** — `requestAnimationFrame` con `dt` limitado a 50ms.

**Máquina de estados:**
- `playing` → juego activo
- `dead` → esperando `deadTimer` (2s) antes de reaparecer
- `gameover` → esperando `Space` para reiniciar con `initGame()`

## Convenciones

- Todo el código en `game.js`. No hay otros archivos JS ni módulos.
- Canvas rendering directo (`ctx.fillRect`, `ctx.beginPath`, etc.). No hay abstracción de rendering.
- Entidades tienen `update(dt)` y `draw()`. El `dt` viene de `requestAnimationFrame`.
- Entidades eliminadas se marcan `dead = true` y se filtran al final del frame.
- El espacio es toroidal: `wrap(valor, max)` asegura que nada sale del canvas.
- `state` controla el flujo del juego — revisar antes de agregar lógica nueva.
- Valores mágicos (velocidades, tamaños, puntos) están como constantes al inicio de cada clase, no hardcodeados en funciones.
- El HUD y overlays están en funciones separadas (`drawHUD`, `drawOverlay`).
