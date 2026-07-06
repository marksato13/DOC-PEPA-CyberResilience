# Dashboard 01 - Tendencia y Distribucion

## Proposito del dashboard

Este dashboard es la vista ejecutiva inicial del proyecto. Su funcion es responder rapidamente:

- Cuantos incidentes existen en el dataset consolidado?
- Cual es la perdida financiera acumulada?
- Cuantos usuarios fueron afectados?
- Que tipos de ataques son mas frecuentes?
- Como evolucionan los ataques por anio?

Es el primer dashboard que conviene mostrar porque da contexto general antes de entrar a analisis geografico, tecnico o ML.

## A quien le beneficia

| Usuario | Beneficio |
| --- | --- |
| Directivos | Entienden el impacto general sin revisar tablas tecnicas |
| Docente/evaluador | Ve rapidamente que el pipeline produjo resultados analizables |
| Responsable de seguridad | Identifica volumen de incidentes y tendencia temporal |
| Equipo financiero | Relaciona ciberataques con perdida economica |
| Equipo de comunicacion/riesgo | Puede explicar impacto en usuarios afectados |

## Como se usaria

En una exposicion, se usa para abrir la demo:

> Primero mostramos la vista general. Aqui se ve el resumen del riesgo: incidentes, perdida financiera, usuarios afectados, paises involucrados y eventos vivos.

En una organizacion real, se usaria para seguimiento ejecutivo semanal o mensual:

- Ver si los incidentes aumentan o bajan.
- Identificar tipos de ataque dominantes.
- Revisar anos o periodos con mayor perdida.
- Comparar impacto historico antes de tomar decisiones.

## Como se llego a mostrar esto desde la data

### Entrada

Los datos vienen principalmente de T1, enriquecidos por el join con T2 y T3:

```text
T1_global_threats.csv
T2_ai_ml_events.csv
T3_synthesized.csv
```

### Pipeline previo

```text
T1/T2/T3 -> pipeline_linux.py -> Spark -> limpieza -> agregacion -> join -> Parquet
```

El dashboard no lee los CSV brutos. Lee el Parquet consolidado:

```text
~/bigdata/output/cybersecurity_joined
```

### Script que lo muestra

```text
dashboard.py
```

Funcion de carga:

```python
pd.read_parquet(PARQUET)
```

Luego se aplican filtros globales:

```text
Year
Attack_Type
Target_Industry
```

El DataFrame filtrado se llama conceptualmente:

```text
dff
```

## Widgets principales

### KPI - Incidentes

Origen:

```python
len(dff)
```

Interpretacion:

> Cuenta los incidentes consolidados despues del join y filtros activos.

Como explicarlo:

> Este numero no cuenta filas brutas de T2 o T3. Cuenta los incidentes consolidados con T1 como base.

### KPI - Perdida USD

Origen:

```python
dff["Financial_Loss_M"].sum()
```

Interpretacion:

> Suma la perdida financiera registrada en T1 para los datos filtrados.

Como explicarlo:

> Este KPI ayuda a traducir ciberseguridad a impacto economico.

### KPI - Usuarios afectados

Origen:

```python
dff["Affected_Users"].sum()
```

Interpretacion:

> Muestra el alcance social o operativo de los incidentes.

### KPI - Paises

Origen:

```python
dff["Country"].nunique()
```

Interpretacion:

> Indica cuantos paises aparecen en el subconjunto filtrado.

### KPI - Eventos vivo

Origen:

```text
live_events.csv
```

Interpretacion:

> Conecta la vista historica con la capa live demo.

## Grafico: Distribucion por tipo de ataque

Tipo:

```text
Pie chart / Donut
```

Origen:

```python
dff["Attack_Type"].value_counts()
```

Que muestra:

> Que tipos de ataque dominan dentro del dataset consolidado.

Como explicarlo:

> Este grafico permite identificar si el riesgo esta concentrado en DDoS, Phishing, Malware, Ransomware u otro tipo de ataque.

## Grafico: Perdida financiera acumulada por anio

Tipo:

```text
Barras apiladas
```

Origen:

```python
dff.groupby(["Year", "Attack_Type"])["Financial_Loss_M"].sum()
```

Que muestra:

> Como cambia la perdida financiera por anio y por tipo de ataque.

Como explicarlo:

> Aqui no solo vemos cuantos ataques hay, sino cuanto cuestan a traves del tiempo.

## Grafico: Evolucion de incidentes anio a anio

Tipo:

```text
Linea con marcadores
```

Origen:

```python
dff.groupby(["Year", "Attack_Type"]).size()
```

Que muestra:

> La tendencia temporal de incidentes por tipo de ataque.

Como explicarlo:

> Este grafico permite ver patrones: si un ataque aumenta, disminuye o se mantiene estable en el tiempo.

## Relacion con los objetivos

Este dashboard se relaciona principalmente con el Objetivo 1:

```text
Pipeline distribuido + dataset consolidado + visualizacion historica
```

Demuestra que el pipeline no solo proceso datos, sino que los convirtio en informacion util para decision.

## Preguntas probables

### Que KPI es mas importante?

Respuesta:

> Depende del enfoque. Para gestion ejecutiva, perdida financiera. Para operacion, incidentes. Para impacto social, usuarios afectados. Por eso mostramos varios KPIs.

### Por que separar por tipo de ataque?

Respuesta:

> Porque cada ataque tiene comportamiento e impacto diferente. No es lo mismo analizar DDoS que Ransomware o Phishing.

### Este dashboard usa T2 y T3?

Respuesta:

> La base visible principal viene de T1, pero el dataset ya esta consolidado con T2 y T3. T2 y T3 se aprovechan mas claramente en dashboards posteriores.

### Por que aparece eventos vivo en el mismo dashboard?

Respuesta:

> Porque el dashboard combina vista historica con una senal live demo. Los KPIs historicos vienen del Parquet y eventos vivo viene de `live_events.csv`.

## Frase para exposicion

> Este dashboard resume el estado general del riesgo. Desde el Parquet consolidado mostramos volumen de incidentes, perdida economica, usuarios afectados y tendencia por tipo de ataque. Es la vista ejecutiva del producto.
