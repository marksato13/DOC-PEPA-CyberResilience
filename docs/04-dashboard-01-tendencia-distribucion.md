# Dashboard 01 - Tendencia y Distribucion

## Objetivo

Mostrar una vista ejecutiva del comportamiento general de los incidentes.

Este dashboard responde:

- Cuantos incidentes hay?
- Cuanto fue la perdida total?
- Cuantos usuarios fueron afectados?
- Que tipos de ataques dominan?
- Como evolucionan los incidentes por anio?

## Widgets principales

### KPIs superiores

Muestran:

- Incidentes.
- Perdida USD.
- Usuarios afectados.
- Paises.
- Eventos vivo.

Origen de datos:

```text
Incidentes -> conteo de filas del Parquet filtrado
Perdida USD -> suma de Financial_Loss_M
Usuarios afectados -> suma de Affected_Users
Paises -> cantidad unica de Country
Eventos vivo -> filas en live_events.csv
```

## Grafico: Distribucion por tipo de ataque

Tipo: pie chart.

Sirve para saber que ataques aparecen con mayor frecuencia.

Codigo base:

```python
dff["Attack_Type"].value_counts()
```

## Grafico: Perdida financiera acumulada por anio

Tipo: barras apiladas.

Sirve para analizar impacto economico por anio y tipo de ataque.

Codigo base:

```python
dff.groupby(["Year", "Attack_Type"])["Financial_Loss_M"].sum()
```

## Grafico: Evolucion de incidentes anio a anio

Tipo: linea con marcadores.

Sirve para ver tendencia temporal.

Codigo base:

```python
dff.groupby(["Year", "Attack_Type"]).size()
```

## A quien va dirigido

- Directivos.
- Docentes evaluadores.
- Responsables de seguridad.
- Personas que necesitan una vista rapida.

## Importancia

Es el dashboard inicial porque resume el estado general del problema.

Frase para exposicion:

> Este dashboard permite pasar de datos tabulares a una lectura ejecutiva del riesgo: volumen, impacto economico, usuarios afectados y tendencia temporal.

## Preguntas probables

Pregunta:

> Que KPI es mas importante?

Respuesta:

> Depende del enfoque. Para gestion, perdida financiera. Para operacion, incidentes. Para impacto social, usuarios afectados.

Pregunta:

> Por que separar por tipo de ataque?

Respuesta:

> Porque no todos los ataques tienen el mismo comportamiento ni el mismo impacto economico.
