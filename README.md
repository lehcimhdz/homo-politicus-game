# Homo Politicus — Game

Frontend, content, scenarios and game-design layer for **Homo Politicus**. This repository holds everything that lives *above* the simulation engine: graphical UI (planned), historical leaders, prefabricated scenarios, the event catalog with flavor text, localization, tutorials and balance work.

The simulation engine itself lives in [`homo-politicus`](https://github.com/lehcimhdz/homo-politicus) and is consumed by this repository as a dependency (planned: git submodule once the engine stabilizes its public API).

---

## Why split into two repos?

The engine and the game move at different speeds and have different audiences:

| Concern | `homo-politicus` (engine) | `homo-politicus-game` (this) |
|---|---|---|
| Audience | C++ developers / modders | Players / writers / designers |
| Change rate | Slow (stable API) | Fast (content tuning, balance) |
| License | MIT | TBD (probably proprietary if commercial) |
| Skills | Systems C++, simulation | Game design, writing, localization |

The engine is a headless library that simulates politics; this repo gives it a face, a story and a goal.

---

## Directory layout

```
content/
  scenarios/   YAML/MD files describing historical scenarios (initial Country state + objectives)
  leaders/     Historical leader profiles (Perón, Thatcher, Mandela, ...) with starting stats
  events/      Event catalog: triggers + flavor text + branches
  locales/     ES / EN translation tables
design/        Game-design documents: tutorial scripts, balance notes, mode definitions
docs/          Player-facing documentation (rulebook, FAQs)
specs/         Implementation specs for each phase
ui/            Graphical UI scaffolding (SFML or Godot — TBD)
```

---

## Current status

This repo is fresh. It tracks **Fases 5 a 8** of the master roadmap (UI, design, content, polish). The simulation engine in the sister repo is at v0.1 (24 tests passing, full game loop, ~110 commands, 9 end conditions).

See [`ROADMAP.md`](ROADMAP.md) for what is planned and [`specs/`](specs/) for execution details.

---

## License

Content (scenarios, leaders, events) released under CC BY 4.0. Code under MIT. See `LICENSE`.
