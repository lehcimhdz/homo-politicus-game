# SPEC Fase 8 — Producto comercial

## Objetivo
Llevar el juego del estado "demo técnica" a "producto vendible en Steam Early Access".

---

## 8.1 — Save versionado robusto

Actual: `savegame.txt` con subset crítico (~30 keys).
Objetivo:
- Save completo: TODAS las variables del Country.
- `schema_version` en la cabecera.
- Migrador automático para saves de versiones anteriores.
- Slot múltiple: hasta 10 slots + autosave + quicksave.
- Save corrupto detectado vía checksum.

**Estructura propuesta:**
```yaml
schema_version: 2
saved_at: "2026-06-10T12:34:56Z"
checksum: "sha256:..."
metadata:
  player_name: "Michel"
  scenario_id: "argentina_1976"
  turn: 12
  difficulty: "frágil"
state:
  country: { ... estructura completa ... }
  game: { turn_count, popularity_sum, end_condition, pending_decisions }
```

Loader detecta `schema_version` y aplica migraciones en cascada.

---

## 8.2 — Auditoría de seguridad y UB

- Compilar con `-fsanitize=address,undefined,leak` en debug.
- Ejecutar suite de tests bajo sanitizers, cero issues.
- Auditar lecturas de `std::cin` con `iostream` fallidos (manejar EOF).
- Auditar punteros (ninguno en código actual, pero las std::function captures con `this` son sensibles).
- `-Wall -Wextra -Wpedantic` activado por defecto.
- Static analysis con `clang-tidy`.

---

## 8.3 — Performance

- Profiling con Instruments (macOS) o perf (Linux).
- Target: 60fps sostenidos en 2017-grade hardware.
- Eliminar allocations en hot path (preferir arenas / pools).
- Reducir `std::cout` en `update()` — usar log buffer batcheable.

---

## 8.4 — Packaging

- **macOS**: bundle `.app`, `.dmg` notarizado.
- **Windows**: instalador NSIS o MSI, firmado.
- **Linux**: AppImage + Flatpak.
- Versionado SemVer: `0.1.0-early-access`.
- CI/CD con GitHub Actions: build matrix Win/Mac/Linux en cada push.

---

## 8.5 — Marketing y lanzamiento

### Assets
- **Trailer** (60-90s): mostrar 3 escenarios, modal de decisión, end-screen.
- **Capturas (10)**: dashboard, mapa, decisión modal, end-game, tutorial, escenarios, configuración, logros.
- **Logo** y arte de portada.
- **Página web** (single page, `homopoliticus.com` o similar).
- **GIF animado** del modal de decisión para Twitter.

### Steam page
- Título: "Homo Politicus".
- Tagline: "Lidera. Sobrevive. Decide."
- Descripción: 500 palabras + bullets.
- Tags: Strategy, Simulation, Political, Turn-Based, Singleplayer.
- Precio sugerido: **$14.99 USD** Early Access, **$19.99** v1.0.
- Trailer + 10 screenshots + 1 GIF.

### Plan de lanzamiento
- T-90 días: Wishlist abierta + página Steam.
- T-60 días: Trailer público.
- T-30 días: Beta cerrada con 50 testers.
- T-7 días: Press kit a sitios indie (RPS, PCGamer, niche strategy outlets).
- T-0: Early Access live.
- T+30: Patch 0.2 con feedback inicial.
- T+90: Patch 0.3 con contenido nuevo.
- T+180: Lanzamiento v1.0 + DLC anunciado.

---

## 8.6 — Comunidad y soporte

- Discord oficial.
- Subreddit r/HomoPoliticus.
- GitHub Issues para bugs.
- Roadmap público en Trello o GitHub Projects.
- Stream del autor jugando 1 vez por semana (autenticidad).

---

## 8.7 — Validación
- Build CI verde en 3 OS.
- Sanitizers limpios.
- Save loadable en 100% de saves de versión anterior.
- 60 fps en 95% de configuraciones target.
- Wishlist >5000 antes de launch (indicador de tracción).
