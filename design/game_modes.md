# Modos de juego — Especificación funcional

## 1. Sandbox

### Propósito
Jugar libremente sin objetivos forzados. Modo más popular en simuladores tipo Tropico / Democracy.

### Parámetros configurables
- País inicial: preset Estable / Frágil / En crisis / Estado fallido.
- Año de inicio: 1960, 1980, 2000, 2024.
- Líder: random o elegir uno de los 10 históricos.
- Eventos extremos: ON (nuclear, golpe) / OFF.
- Velocidad de simulación: 1× / 2× / 4× / pausa.
- Random seed: visible al jugador (para compartir partidas).

### Reglas
- Sin condición de victoria automática.
- Game over por cualquier `EndCondition` regular.
- Cualquier momento se puede `save` / `load`.
- Sin scoring final salvo si lo activa el jugador.

---

## 2. Misiones

### Propósito
Aprender el motor a través de objetivos progresivos. Reemplaza el viejo concepto de "campaña".

### Estructura
- 30 misiones agrupadas en 6 capítulos temáticos.
- Cada misión: setup forzado + objetivo + bono opcional.

### Capítulos
1. **Fundamentos** (5 misiones): manejo básico, fiscal, vecinos, escándalos, decisiones.
2. **Economía profunda** (5): inflación, recesión, SWF, commodities, comercio.
3. **Política dura** (5): coup, impeachment, revolución, court packing.
4. **Diplomacia** (5): alianzas, guerra, sanciones, sumits, refugiados.
5. **Crisis múltiples** (5): pandemia + guerra, hambruna + escándalo, etc.
6. **Histórico** (5): subset de escenarios con objetivos específicos.

### Reward
- 1-5 estrellas según turnos usados / bonos cumplidos.
- Desbloquea misiones siguientes y logros.

---

## 3. Histórico

### Propósito
Recrear o alterar momentos reales. Educativo + entretenimiento.

### Escenarios disponibles
- Argentina 1976 (★★★★★)
- Cuba 1959 (★★★★★)
- Venezuela 2013 (★★★★★)
- Chile 1973 (★★★★★)
- Singapur 1965 (★★★★☆)
- (Futuro: USA 1929, Alemania 1933, Sudáfrica 1994, etc.)

### Reglas
- Estado inicial cargado desde el YAML del escenario.
- Eventos forzados (`forced_events`) ocurren sí o sí.
- Algunos comandos pueden estar vetados por contexto temporal (no AI en 1976).
- Objetivos por dificultad (bronce/plata/oro/diamante).
- Score final con multiplicador histórico × 1.5.

---

## 4. Sucesor

### Propósito
Mostrar las consecuencias de tus decisiones más allá de tu mandato.

### Mecánica
1. Terminás partida regular (cualquier modo).
2. Si finalizaste por `TERM_COMPLETED` o `ELECTION_LOSS`: opción "Continuar como sucesor".
3. Heredás el país en el estado en que lo dejaste.
4. Jugás como tu sucesor (auto-generado con stats opuestas si perdiste elección).
5. 30 turnos para gestionar el legado.

### Valor de gameplay
- Si dejaste deuda alta → tu sucesor sufre.
- Si dejaste backsliding democrático → sucesor puede deshacerlo o consolidarlo.
- Cierra el ciclo "consecuencias temporales".

---

## 5. Iron Man

### Propósito
Modo hardcore para veteranos. Single-life political career.

### Reglas
- Sin save/load durante la partida.
- Autosave silencioso al final de cada turno (para crash recovery, no para reload).
- Si game over → partida terminada, no se puede reintentar.
- Score recibe +30% por completar.
- Logros exclusivos solo en Iron Man.

---

## 6. Co-op (futuro, post v1.0)

### Concepto
Dos jugadores controlan: uno el ejecutivo, otro el congreso/oposición.

### Por qué no v1.0
- Multiplayer triplica complejidad técnica.
- Mejor lanzar single player sólido primero.

---

## UX común a todos los modos

### Pantalla de selección
```
+----------------------------------+
|  HOMO POLITICUS                  |
|                                  |
|  [ Sandbox       ★☆☆☆☆ - ★★★★★ ] |
|  [ Misiones      Progress 12/30 ]|
|  [ Histórico     5 escenarios   ]|
|  [ Sucesor       (locked)       ]|
|  [ Iron Man      (locked)       ]|
|                                  |
|  Settings | Achievements | Quit  |
+----------------------------------+
```

### Persistencia entre modos
- Logros son cross-mode.
- Progreso de misiones se mantiene aunque cambies de modo.
- Histórico desbloqueado siempre.
- Sucesor desbloqueado tras completar primer mandato en cualquier modo.
- Iron Man desbloqueado tras 10+ horas de juego.

---

## Validación
- 5 modos implementados.
- Selección desde menú principal funciona.
- Cada modo tiene su propio score / objetivos.
- Persistencia funciona entre modos.
