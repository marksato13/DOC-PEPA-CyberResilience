# Arquitectura, tablas y pipeline

## Arquitectura general

```text
Datos CSV
  T1_global_threats.csv
  T2_ai_ml_events.csv
  T3_synthesized.csv
        -> Pandas
        -> Spark DataFrames
        -> Limpieza y transformacion
        -> Agregaciones
        -> Join triple
        -> Parquet particionado
        -> Dashboard Streamlit
```

## Tabla T1 - Global Threats

Es la tabla principal del analisis.

Aporta:

- Pais.
- Anio.
- Tipo de ataque.
- Industria objetivo.
- Perdida financiera.
- Usuarios afectados.
- Fuente del ataque.
- Vulnerabilidad.
- Mecanismo de defensa.

Uso en dashboard:

- KPIs de incidentes.
- Perdida USD.
- Usuarios afectados.
- Paises.
- Industria objetivo.
- Perdida por pais.
- Perdida por tipo de ataque.

## Tabla T2 - AI/ML Events

Aporta informacion tecnica de eventos.

Aporta:

- Severidad.
- Event ID.
- Source IP.
- Destination IP.
- Data Exfiltrated.
- Threat Intelligence.
- Response Action.

Transformacion importante:

```text
Low      -> 1
Medium   -> 2
High     -> 3
Critical -> 4
```

Uso en dashboard:

- Severidad promedio por tipo de ataque.
- Total de eventos tecnicos por tipo y anio.
- IPs unicas.
- Exfiltraciones.

## Tabla T3 - Synthesized

Aporta datos operativos o enriquecidos.

Aporta:

- IP atacante.
- Datos comprometidos en GB.
- Duracion del ataque.
- Severidad.
- Tiempo de respuesta.
- Metodo de mitigacion.

Uso en dashboard:

- Datos comprometidos promedio.
- Duracion.
- Variables para ML.
- Importancia de caracteristicas.

## Join triple

El pipeline hace:

```text
T1 LEFT JOIN T2_agg por Attack_Type + Year
Resultado LEFT JOIN T3_agg por Attack_Type
```

Por que T1 queda como base:

> T1 contiene el evento/incidente principal. T2 y T3 enriquecen ese incidente con severidad, eventos tecnicos y datos comprometidos.

## Por que Spark

Spark permite:

- Procesar datos grandes.
- Crear transformaciones reproducibles.
- Agregar por grupos.
- Hacer joins eficientes.
- Escalar en el futuro a cluster.

## Por que Parquet

Parquet es importante porque:

- Es formato columnar.
- Es mas eficiente que CSV para analisis.
- Permite compresion.
- Permite particionar por columnas.
- Funciona bien con Spark y Pandas.

En PEPA se particiona por:

```text
Attack_Type / Year
```
