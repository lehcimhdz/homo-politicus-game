# Retrospectiva Hito 3 — Early Access v1.0-EA

**Fecha:** junio 2026
**Sprints:** 21-28

## Resumen

Hito 3 completado. Versión `v1.0-EA` lista para Steam Early Access.

## Sprints

| Sprint | Objetivo | Tests | Estado |
|---|---|---|---|
| 21 | Steamworks scaffold | +4 | ✅ scaffold (requiere App ID humano) |
| 22 | LLM Advisor (Mock + Anthropic) | +6 | ✅ con env var ANTHROPIC_API_KEY |
| 23 | 3 modos de juego (Sandbox/Misiones/Histórico) | +5 | ✅ |
| 24 | Balance pass + Telemetría opt-in | +4 | ✅ |
| 25 | Marketing: trailer script + screenshots plan | 0 | ✅ docs |
| 26 | Steam page completa | 0 | ✅ docs |
| 27 | Press outreach: 30 outlets + templates | 0 | ✅ docs |
| 28 | LAUNCH EA + release notes | 0 | ✅ docs |

**Total tests Hito 3:** +19 (de 139 a 158).
**Commits:** 8 (4 código + 4 docs).

## Métricas al cierre

```
Engine: 16.000+ lineas C++17, 27 modulos
Tests:  158/158 (100%)
Warnings: 0
Binarios: HomoPoliticus (CLI) + HomoPoliticusUI (SFML)
Modos:  3 jugables (Sandbox, Misiones, Historico)
Steam:  scaffold listo, esperando App ID
LLM:    Mock + Anthropic providers
Telemetría: opt-in
Marketing: trailer script, 10 screenshots planeadas, 30 outlets identificados
```

## Lo que NO se hizo (requiere acción humana)

1. **Cuenta Steamworks Partner**: $100 USD, formulario, ID estatal. NO automatizable.
2. **Anthropic API key**: requiere registro + facturación. NO automatizable.
3. **Trailer real**: script listo, falta editor + compositor + grabar gameplay.
4. **Screenshots reales**: plan listo, falta capturar con app real corriendo.
5. **Cuenta de email para press**: bmichelcano@gmail.com sirve, pero un dominio propio se ve más pro.
6. **Discord server**: crear el server, configurar roles, invitar.
7. **Subreddit r/HomoPoliticus**: solicitar a mods de Reddit.

Todos documentados como precondiciones del Sprint 28 (LAUNCH).

## Mood self-report

- Energía: 7/10.
- Motivación: 9/10 (estamos a 4 sprints técnicos del lanzamiento).
- Confianza: 8/10 (los bloqueantes humanos son de procedimiento, no de capacidad).

## Próximo Hito

Hito 4 — v1.0 Release (sprints 29-52). Bloque A: 8 sprints de patches semanales.

Empezamos.
