# Feedback legible — Formato unificado

## Problema actual
Hoy `tax+` imprime solo: `>> Taxes RAISED! Revenue up, Popularity down.`

El jugador no ve:
- Cuánto subió la recaudación exactamente.
- Cuánto cayó la popularidad.
- Qué efectos secundarios se activaron.
- Qué riesgos nuevos aparecieron.

## Formato propuesto

Toda acción del jugador debe imprimir:

```
>> {ACTION_NAME} {SUBJECT}
   {Variable 1}: {sign}{delta} ({from} → {to})
   {Variable 2}: {sign}{delta} ({from} → {to})
   ...
   Riesgos nuevos: {evento1} ({prob}), {evento2} ({prob})
   Próximo turno: {prediction}
```

## Ejemplo 1: `tax+`

**Antes:**
```
>> Taxes RAISED! Revenue up, Popularity down.
```

**Después:**
```
>> SUBES IMPUESTOS al 33%
   Recaudación:    +10.0%   ($100M → $110M)
   Popularidad:    -5.0%    (60% → 55%)
   Inflación:      +1.0%    (3% → 4%)
   Ideología econ: -2pts    (centro → centro-izquierda)
   Riesgos nuevos: Lobby industrial protesta (+5%)
                   Huelga general (+3%)
   Próximo turno: si recaudación >= meta, podés invertir en salud o infra.
```

## Ejemplo 2: `cover_up`

**Antes:**
```
>> COVER-UP exitoso: severidades reducidas, control mediático sube.
```

**Después:**
```
>> COVER-UP del escándalo de corrupción [ÉXITO]
   Costo:           $500.000 (lobbying intenso)
   Severidad escándalo: -60% (0.80 → 0.32)
   Control mediático:    +5% (30% → 35%)
   Presión judicial:     +0% (no detectaron)
   Riesgos nuevos: Filtración futura (+12%)
                   Investigación periodística (+8%)
```

**Con fallo:**
```
>> COVER-UP del escándalo de corrupción [FRACASO]
   Costo:               $500.000 (perdido)
   Severidad escándalo:  +0%
   Presión judicial:    +15% (40% → 55%)
   Exposición mediática: +20%
   Riesgos nuevos: Allanamiento al palacio (+18%)
                   Renuncia de ministro (+10%)
```

## Componentes del feedback

### 1. Acción + objeto
- "SUBES IMPUESTOS al 33%"
- "DECLARAS ESTADO DE EMERGENCIA"
- "EXPROPIAS la corporación XYZ"

### 2. Deltas inmediatos
- 3-5 variables que cambiaron, con valor antes → después y delta porcentual.
- Resaltar las que el jugador puede no esperar.

### 3. Efectos colaterales
- Cambios en otros sistemas indirectos.
- Ej: subir impuestos cambia ideología economic + riesgo de huelga.

### 4. Riesgos nuevos
- Eventos cuya probabilidad cambió como consecuencia.
- Solo mostrar los que subieron >3%.

### 5. Predicción próximo turno
- Una línea de orientación: "si X, podés Y; si Z, cuidado con W".

## Color coding (en UI gráfica)

| Color | Uso |
|---|---|
| **Verde** | Cambios positivos para popularidad / estabilidad. |
| **Rojo** | Cambios negativos / costos. |
| **Amarillo** | Trade-offs ambivalentes. |
| **Gris** | Información neutra. |
| **Rojo pálido** | Riesgos nuevos / advertencias. |

## Reglas de redacción

- **Concreto sobre abstracto**: "Recaudación +$10M" mejor que "Recaudación up".
- **Activo sobre pasivo**: "Subiste impuestos" mejor que "Los impuestos fueron subidos".
- **Numerado sobre cualitativo**: "+5%" mejor que "Mucho".
- **Hispano sobre extranjerismo**: "ascendieron al 33%" mejor que "raisearon".

## Implementación

Cada handler de comando debe:
1. Capturar el estado pre-acción.
2. Aplicar la acción.
3. Capturar estado post-acción.
4. Construir el feedback usando un helper `FeedbackBuilder`.

```cpp
class FeedbackBuilder {
public:
    void add_delta(const std::string& label, double from, double to, const std::string& unit = "%");
    void add_risk(const std::string& event_name, double prob_increase);
    void add_prediction(const std::string& text);
    std::string build();
};
```

## Validación
- Cada comando emite ≥ 3 deltas explícitos.
- Riesgos nuevos solo aparecen si Δprob > 3%.
- Predicción próximo turno opcional pero recomendada.
- Pruebas: ningún comando emite menos de 2 líneas (excepto exit/quit).
