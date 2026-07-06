# Dashboard 03 - Industria y Severidad

## Objetivo

Demostrar el valor del enriquecimiento entre tablas.

Este dashboard cruza informacion de:

```text
T1 -> industria e incidente principal
T2 -> severidad tecnica
T3 -> datos comprometidos
```

## Grafico: Incidentes por industria objetivo

Origen:

```python
dff.groupby("Target_Industry").size()
```

Sirve para saber que sectores son mas atacados.

Ejemplos de industrias:

- Healthcare.
- Finance.
- Government.
- Education.
- Retail.
- Technology.
- Energy.
- Manufacturing.

## Grafico: Severidad promedio por tipo

Origen: T2 enriquecida.

La severidad textual se convierte a escala numerica:

```text
Low      -> 1
Medium   -> 2
High     -> 3
Critical -> 4
```

Luego se calcula:

```python
dff.groupby("Attack_Type")["t2_avg_severity"].mean()
```

## Grafico: Datos comprometidos promedio por tipo

Origen: T3 enriquecida.

```python
dff.groupby("Attack_Type")["t3_avg_data_GB"].mean()
```

Sirve para analizar que ataques comprometen mas informacion.

## A quien va dirigido

- Analistas SOC.
- Investigadores.
- Equipos tecnicos.
- Responsables de sectores criticos.

## Importancia

Este dashboard probablemente genera preguntas del profesor porque demuestra el join triple.

Frase para exposicion:

> T1 nos dice que ocurrio y a quien afecto. T2 agrega severidad tecnica. T3 agrega datos comprometidos. La union permite analizar riesgo desde mas dimensiones.

## Preguntas probables

Pregunta:

> Que aporta T2 si ya existe T1?

Respuesta:

> T2 aporta severidad, eventos tecnicos e informacion de seguridad que T1 no tiene.

Pregunta:

> Que aporta T3?

Respuesta:

> T3 aporta datos comprometidos, duracion, severidad operativa y mitigacion.

Pregunta:

> Por que se agrego T2 antes del join?

Respuesta:

> Porque T2 tiene muchos eventos. Se resume por Attack_Type y Year para evitar duplicar filas de T1 durante el join.
