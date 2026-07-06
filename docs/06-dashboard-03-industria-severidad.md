# Dashboard 03 - Industria y Severidad

## Proposito del dashboard

Este dashboard demuestra el valor del enriquecimiento entre tablas. No se queda solo en contar incidentes; cruza informacion de industria, severidad y datos comprometidos.

Responde:

- Que industrias son mas atacadas?
- Que tipos de ataque tienen mayor severidad tecnica?
- Que ataques comprometen mas datos?
- Que aporta T2 y T3 al analisis?

## A quien le beneficia

| Usuario | Beneficio |
| --- | --- |
| Analistas SOC | Ven severidad tecnica por tipo de ataque |
| Responsables de sectores criticos | Identifican industrias mas expuestas |
| Equipo de respuesta a incidentes | Prioriza ataques con mayor severidad o datos comprometidos |
| Investigadores | Analizan relacion entre industria, severidad e impacto |
| Docente/evaluador | Comprueba que el join triple tiene sentido analitico |

## Como se usaria

En exposicion, se usa para defender el join triple:

> Aqui se ve por que no bastaba con T1. T1 nos dice industria e impacto, T2 agrega severidad tecnica y T3 agrega datos comprometidos.

En una organizacion real, se usaria para:

- Priorizar sectores mas atacados.
- Identificar ataques con severidad alta.
- Detectar tipos de ataque que comprometen mas informacion.
- Definir controles segun industria.

## Como se llego a mostrar esto desde la data

### Datos que entran

```text
T1 -> Target_Industry, Attack_Type
T2 -> Attack_Severity, Event_ID, Source_IP, Data_Exfiltrated
T3 -> data_compromised_GB, attack_duration_min, response_time_min
```

### Transformacion previa

T2 convierte severidad textual a escala numerica:

```text
Low      -> 1
Medium   -> 2
High     -> 3
Critical -> 4
```

T2 se agrega antes del join:

```text
T2 -> groupBy(Attack_Type, Year)
```

T3 se agrega antes del join:

```text
T3 -> groupBy(Attack_Type)
```

Luego se unen con T1:

```text
T1 LEFT JOIN T2_agg ON Attack_Type + Year
Resultado LEFT JOIN T3_agg ON Attack_Type
```

### Salida usada por el dashboard

```text
cybersecurity_joined Parquet
```

## Grafico: Incidentes por industria objetivo

Origen:

```python
dff.groupby("Target_Industry").size()
```

Fuente principal:

```text
T1
```

Que muestra:

> Sectores mas afectados por incidentes.

Como explicarlo:

> Este grafico permite identificar si los ataques se concentran en salud, finanzas, gobierno, educacion, tecnologia u otros sectores.

## Grafico: Severidad promedio por tipo

Origen:

```python
dff.groupby("Attack_Type")["t2_avg_severity"].mean()
```

Fuente principal:

```text
T2 enriquecida
```

Que muestra:

> Que ataques tienen mayor severidad tecnica promedio.

Como explicarlo:

> Esta metrica existe gracias a T2. T1 por si sola no trae el mismo detalle tecnico de severidad de eventos.

## Grafico: Datos comprometidos promedio por tipo

Origen:

```python
dff.groupby("Attack_Type")["t3_avg_data_GB"].mean()
```

Fuente principal:

```text
T3 enriquecida
```

Que muestra:

> Que tipos de ataque comprometen mas informacion en GB.

Como explicarlo:

> Esta metrica viene de T3 y ayuda a medir impacto operativo, no solo cantidad de incidentes.

## Relacion con la visualizacion

Este dashboard es la prueba visual de que el join triple funciono:

```text
T1 aporta industria
T2 aporta severidad tecnica
T3 aporta datos comprometidos
```

Si estas tres fuentes no se hubieran integrado, estos graficos no podrian convivir en la misma vista.

## Relacion con los objetivos

Se relaciona con el Objetivo 1:

```text
Integracion de 3 fuentes + enriquecimiento analitico
```

Este dashboard es tecnico porque muestra el valor de T2 y T3 despues del join.

## Preguntas probables

### Que aporta T2 si ya existe T1?

Respuesta:

> T2 aporta severidad, eventos tecnicos, IPs y exfiltraciones. Es informacion mas operativa que T1 no tiene.

### Que aporta T3?

Respuesta:

> T3 aporta datos comprometidos, duracion, tiempo de respuesta, severidad operativa y variables utiles para ML.

### Por que se agrego T2 antes del join?

Respuesta:

> Porque T2 tiene muchos eventos. Se resume por `Attack_Type` y `Year` para evitar duplicar filas de T1.

### Por que T3 se une solo por Attack_Type?

Respuesta:

> Porque en el pipeline actual T3 se resume a nivel de tipo de ataque. Su rol es enriquecer con metricas promedio por ataque.

### Que grafico defenderias mas?

Respuesta:

> Severidad promedio y datos comprometidos, porque demuestran el valor agregado de T2 y T3. Sin el join, esos datos no estarian en la misma vista.

## Frase para exposicion

> Este dashboard demuestra el enriquecimiento del pipeline: T1 nos dice a que industria afecto el incidente, T2 agrega severidad tecnica y T3 agrega datos comprometidos. Asi pasamos de una tabla simple a analisis multidimensional.
