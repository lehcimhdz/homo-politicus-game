# Specs — homo-politicus-game

This directory holds the detailed implementation specs for Fases 5-12 of the master roadmap. Each spec is self-contained and can be executed independently once its prerequisites are met.

| Phase | File | Topic | Depth | Status |
|---|---|---|---|---|
| 5 | [SPEC_PHASE5_UI.md](SPEC_PHASE5_UI.md) | Graphical UI (SFML + ImGui) | Layout, 6 panels, modal, animations | Blocked: needs `brew install sfml` |
| 6 | [SPEC_PHASE6_GAME_DESIGN.md](SPEC_PHASE6_GAME_DESIGN.md) | Modes, tutorial, scoring, 40 achievements, balance | Production-ready design doc | Ready to execute |
| 7 | [SPEC_PHASE7_CONTENT.md](SPEC_PHASE7_CONTENT.md) | 5 scenarios, 10 leaders, 30 events, ES/EN locales | Full YAML schemas | Ready to execute |
| 8 | [SPEC_PHASE8_PRODUCT.md](SPEC_PHASE8_PRODUCT.md) | Save versioning, packaging, marketing | Pre-launch checklist | After Phase 5 |
| 9 | [SPEC_PHASE9_WORLD_DEPTH.md](SPEC_PHASE9_WORLD_DEPTH.md) | Regions, cities, industries, corps, unions (SimCity-class) | New data structures | Engine extension |
| 10 | [SPEC_PHASE10_DIPLOMACY.md](SPEC_PHASE10_DIPLOMACY.md) | 30+ countries, blocs, treaties, intl orgs, global crises | Geopolitical depth | Engine extension |
| 11 | [SPEC_PHASE11_AGENTS.md](SPEC_PHASE11_AGENTS.md) | Named agents: ministers, generals, legislators, judges | CK-style depth | Engine extension |
| 12 | [SPEC_PHASE12_MODDING.md](SPEC_PHASE12_MODDING.md) | Mod loading, hooks, Steam Workshop | Community extension | After v1.0 |
| 13 | [SPEC_PHASE13_MULTIPLAYER.md](SPEC_PHASE13_MULTIPLAYER.md) | Hot-seat, PBEM, online sync | 3 multiplayer modes | Post v1.0 |
| 14 | [SPEC_PHASE14_AI_ADVISOR.md](SPEC_PHASE14_AI_ADVISOR.md) | LLM-powered advisors (Claude/GPT) | 5 advisor personalities + cost model | Differentiator for 2026+ |
| 15 | [SPEC_PHASE15_STEAM.md](SPEC_PHASE15_STEAM.md) | Steamworks integration, marketing, launch | Asset checklist + timeline | At launch |

---

## Recommended order of execution

1. **Phase 7** (content) — pure data, no engine changes needed. Unlocks scenarios for testing.
2. **Phase 6** (game design) — written deliverables (tutorial scripts, balance tables, achievements). No code yet.
3. **Phase 9-11** (engine extensions) — require engine API changes; do these in `homo-politicus` repo, then update game repo.
4. **Phase 5** (UI) — needs SFML installed and engine API frozen.
5. **Phase 12** (modding) — best done after content format stabilizes.
6. **Phase 8** (product polish + launch) — last.

---

## Cross-cutting concerns

- **Localization**: every new content piece must have `name_es`, `name_en`, `description_es`, `description_en`.
- **Schema versioning**: every YAML file declares `version: N`. Bumping requires migrator.
- **Engine compatibility**: content references engine variables. When engine refactors, run a content-validation pass.
- **License**: CC BY 4.0 for content, MIT for code.
