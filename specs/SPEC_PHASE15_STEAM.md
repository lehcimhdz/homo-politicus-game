# SPEC Fase 15 — Steam integration

## Objetivo
Lanzar en Steam Early Access con todas las features que la plataforma habilita.

---

## 15.1 — Cuenta y aplicación

- Crear Steamworks Partner account ($100 fee one-time).
- Submit app: nombre, descripción, tags, capturas, trailer.
- Get App ID asignado.
- Configurar página store: precio regional, idiomas, sistema mínimo.

---

## 15.2 — Integración técnica

### SDK
- Steamworks SDK 1.59+
- Wrapper C++ minimal.
- Inicializar con `SteamAPI_Init()` en main.cpp.

### Features prioritarias

**Achievements (40 logros del spec 6):**
```cpp
SteamUserStats()->SetAchievement("first_year");
SteamUserStats()->StoreStats();
```

**Cloud saves:**
- Auto-sync de `savegame.txt`, slots y profile.
- Quota: 100MB por usuario (más que suficiente).

**Workshop (mods):**
- Subscribe → carpeta `~/Steam/.../workshop/content/<appid>/`.
- Game loader detecta y carga.
- Rating, comentarios.

**Stats globales:**
- Tracking: turnos jugados, escándalos sobrevividos, países más jugados.
- Global leaderboard de score final.

**Rich Presence:**
- "Jugando como Mandela en Sudáfrica 1994, turno 12".

**Trading cards (post-launch):**
- 8 cards con líderes históricos.
- Crafting → badges, emoticons.

---

## 15.3 — Marketing y página Steam

### Asset checklist
- [ ] **Header capsule**: 460×215 (banner principal).
- [ ] **Small capsule**: 231×87 (lista de juegos).
- [ ] **Library hero**: 3840×1240 (banner alta resolución).
- [ ] **Library logo**: PNG transparente.
- [ ] **Library icon**: 256×256.
- [ ] **10 screenshots** (1920×1080).
- [ ] **Trailer** (60-90s, 1920×1080, MP4).
- [ ] **2 GIFs animados** para X/Twitter.

### Descripción Steam (500 palabras)
```
Liderá una nación durante una década de tormentas.

Homo Politicus es un simulador político profundo donde controlás un presidente
en medio de crisis económicas, conspiraciones militares, escándalos personales,
guerras civiles y disputas geopolíticas. Cada decisión te persigue.

CARACTERÍSTICAS:
- 200+ variables interconectadas modelando 5 sistemas de país.
- 9 condiciones de fin de partida (golpe, impeachment, revolución, exilio...).
- 8 escenarios históricos reales (Argentina 1976, Cuba 1959, Chile 1973...).
- 15 líderes históricos con stats y acciones únicas.
- 30 eventos con flavor text y decisiones ramificadas.
- Soporte de mods con Steam Workshop.
- Asesores LLM opcionales (Claude/GPT) que comentan tus decisiones.

NO ES un juego para todos:
- Sin combate twitchy. Sin gráficos AAA. Sin multiplayer al inicio.
- Sí es: simulación profunda, narrativa emergente, decisiones difíciles, errores costosos.

Si te gustan: Democracy 4, Suzerain, Crusader Kings, Tropico — esto es para vos.
```

### Tags
- Strategy
- Simulation
- Political
- Turn-Based
- Historical
- Singleplayer
- Choices Matter
- Replay Value

---

## 15.4 — Precio

- **Early Access**: $14.99 USD.
- **v1.0 launch**: $19.99 USD.
- **DLC escenarios** (post-launch): $4.99 cada.

Comparables:
- Democracy 4: $24.99
- Suzerain: $19.99
- Tropico 6: $49.99

Mi posicionamiento: precio premium-indie, no AAA.

---

## 15.5 — Cronograma de lanzamiento

```
T-90 días: Página Steam pública con wishlist
T-60 días: Trailer público + 10 screenshots
T-30 días: Beta cerrada (50 testers vía Discord)
T-14 días: Press kit a outlets (RPS, PCGamer, Indie Game Sponsorship)
T-7 días:  Streamer outreach (Quill18, Many a True Nerd, Wolf en español)
T-0:       Early Access launch
T+1 día:   AMA en r/IndieDev y r/strategy
T+7 días:  Patch 0.1.1 con feedback rápido
T+30 días: Patch 0.2 con balance + bugfixes
T+90 días: DLC anunciado
T+180 días: v1.0 lanzamiento completo
T+365 días: Second DLC + mobile port consideration
```

---

## 15.6 — Validación
- Steamworks integrado.
- Logros desbloquean.
- Cloud save funciona.
- 10 screenshots, trailer en H.264 listos.
- Wishlist >5000 antes de launch.
- Página Steam revisada por 5 personas externas.
