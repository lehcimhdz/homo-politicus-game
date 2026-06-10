# SPEC Fase 13 — Multiplayer

## Objetivo
Convertir un single-player profundo en multi-player asincrónico o sincrónico, sin romper el motor existente.

---

## 13.1 — Modos de multiplayer

### 13.1.1 Hot-seat (2-4 jugadores, mismo PC)
- Misma partida, cada turno controla un jugador distinto.
- Cada uno tiene su propio Country.
- Fines de turno revelados simultáneamente.
- **Complejidad técnica:** baja.

### 13.1.2 Asincrónico Play-by-email (PBEM, 2-8 jugadores)
- Cada uno juega su turno cuando puede.
- Save file se transmite por email/Discord/Steam.
- Carga la partida, ejecuta tu turno, vuelve a guardar.
- **Complejidad técnica:** media (requires deterministic RNG seeding).

### 13.1.3 Online sincrónico (2-8 jugadores, red)
- Servidor dedicado (o peer-to-peer).
- Pausa cuando alguien decide.
- Reconexión, kick, vote-to-skip.
- **Complejidad técnica:** alta.

---

## 13.2 — Roles posibles

Cada jugador puede jugar como:
- **Líder de país completo** (Argentina, Brasil, Chile, etc.).
- **Líder de un sector**:
  - Sector militar (controla decisiones de defensa).
  - Sector económico (Banco Central + Hacienda).
  - Sector de oposición (controla congreso opositor + movimientos sociales).
- **Líder de organización internacional**: ONU, FMI, OEA.

---

## 13.3 — Sincronización de turno

Cada turno tiene fases:
1. **Plan** (todos en paralelo): cada jugador planifica acciones.
2. **Resolve** (server): se aplican todas las acciones.
3. **Reveal**: resultados visibles a todos con info parcial (fog of diplomacy).

Fog of diplomacy: vos no ves todas las acciones del otro, solo las visibles (declaraciones, sanciones, guerra). Las encubiertas (cover_ups, espionaje) requieren detección.

---

## 13.4 — Determinismo y anti-cheat

- RNG seedeado con (seed_global + turn_number + player_id) → mismo resultado en todos los clientes.
- Server autoritativo (modelo Frozen Synapse / Diplomacy).
- Validación cruzada de estados: si jugador A reporta un estado distinto al consenso → kick.

---

## 13.5 — Persistencia

- Save por partida (no por jugador).
- Replay de la partida completa para análisis posterior.
- Ranking ELO opcional.

---

## 13.6 — Por qué no en v1.0
- Single-player todavía no está pulido.
- Multiplayer triplica complejidad técnica (red, sincronización, anti-cheat).
- Multiplayer no es la propuesta de valor central (es un simulador político profundo, no Among Us).
- Riesgo: lanzar multiplayer roto destruye reputación.

## Cuándo sí
- Tras 50.000+ ventas y comunidad establecida.
- Tras 1 año de patches single-player.
- Como expansión paga ($9.99) o DLC.

---

## Validación
- 2 jugadores hot-seat completan partida sin desync.
- PBEM: save/load mantiene estado idéntico entre clientes.
- Online: 4 jugadores en LAN durante 50 turnos sin desync.
