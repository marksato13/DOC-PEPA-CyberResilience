# Dashboard 02 - Geografia y Finanzas

## Proposito del dashboard

Este dashboard analiza el impacto territorial y economico de los ataques. Su objetivo es responder:

- Que paises concentran mas incidentes?
- Que tipos de ataque generan mayor perdida promedio?
- Que paises acumulan mayor perdida total?
- Donde se deberia priorizar monitoreo, presupuesto o controles?

## A quien le beneficia

| Usuario | Beneficio |
| --- | --- |
| Gerencia de riesgos | Prioriza paises o regiones criticas |
| Equipo financiero | Evalua perdida acumulada y costo promedio por ataque |
| CISO / seguridad | Decide donde reforzar controles |
| Analista de ciberseguridad | Relaciona ubicacion con tipos de ataque |
| Docente/evaluador | Ve que el proyecto conecta ciberseguridad con impacto economico |

## Como se usaria

En exposicion, se usa despues del Dashboard 01:

> Ya vimos el panorama general. Ahora mostramos donde se concentra el problema y cuanto cuesta.

En una organizacion real, se usaria para:

- Priorizar paises con mas incidentes.
- Detectar paises con menos incidentes pero mayor perdida.
- Sustentar inversion en seguridad por region.
- Comparar impacto economico por tipo de ataque.

## Como se llego a mostrar esto desde la data

### Entrada principal

Este dashboard usa principalmente columnas de T1 ya consolidadas en el Parquet:

```text
Country
Attack_Type
Financial_Loss_M
Year
Target_Industry
```

### Flujo previo

```text
T1 -> limpieza -> join con T2/T3 -> Parquet -> dashboard.py
```

Aunque el analisis geografico y financiero usa sobre todo T1, se muestra dentro del dataset consolidado, no desde el CSV bruto.

### Script que lo muestra

```text
dashboard.py
```

El dashboard aplica filtros globales antes de graficar:

```text
Year
Attack_Type
Target_Industry
```

## Grafico: Top 10 paises por incidentes

Tipo:

```text
Barras horizontales
```

Origen:

```python
dff.groupby("Country").size()
```

Que muestra:

> Los paises con mayor cantidad de incidentes registrados en el dataset filtrado.

Como interpretarlo:

> Si un pais aparece arriba, no necesariamente significa que tenga la mayor perdida economica. Significa que concentra mas incidentes.

Como explicarlo:

> Este grafico ayuda a detectar concentracion geografica del riesgo.

## Grafico: Perdida financiera promedio por tipo

Tipo:

```text
Barras horizontales
```

Origen:

```python
dff.groupby("Attack_Type")["Financial_Loss_M"].mean()
```

Que muestra:

> Cuanto cuesta en promedio cada tipo de ataque.

Interpretacion importante:

> Este grafico no mide frecuencia, mide impacto promedio.

Ejemplo para explicar:

> Un ataque puede no ser el mas frecuente, pero si su perdida promedio es alta, debe considerarse critico.

## Grafico: Perdida total por pais

Tipo:

```text
Barras verticales
```

Origen:

```python
dff.groupby("Country")["Financial_Loss_M"].sum()
```

Que muestra:

> Los paises con mayor perdida financiera acumulada.

Como interpretarlo:

> Este grafico permite priorizar recursos en los lugares donde el impacto economico es mayor.

## Relacion con la visualizacion

Este dashboard transforma columnas de T1 en indicadores geograficos y financieros:

```text
Country -> ranking de paises
Financial_Loss_M -> perdida promedio y perdida total
Attack_Type -> comparacion por tipo de ataque
```

El dashboard permite filtrar por anio, ataque e industria para cambiar la lectura.

## Relacion con los objetivos

Se relaciona con el Objetivo 1:

```text
Dataset consolidado + analisis economico/geografico
```

Demuestra que el pipeline permite pasar de datos historicos a decisiones de priorizacion.

## Preguntas probables

### Por que un pais con pocos incidentes puede ser critico?

Respuesta:

> Porque puede tener menos casos, pero con perdidas financieras mas altas. Por eso mostramos incidentes y perdida total por separado.

### Que decision se puede tomar con este dashboard?

Respuesta:

> Priorizar controles, monitoreo, capacitacion o inversion en los paises y ataques con mayor impacto.

### Esto sirve solo para paises?

Respuesta:

> No. La misma logica se puede adaptar a regiones, sedes, areas internas o unidades de negocio si se cambian los datos de entrada.

### Por que se analiza perdida promedio y perdida total?

Respuesta:

> La perdida promedio muestra que tan costoso es un ataque tipico. La perdida total muestra el impacto acumulado. Ambas metricas responden preguntas diferentes.

## Frase para exposicion

> Este dashboard conecta ciberseguridad con impacto economico. Nos permite ver donde ocurren mas incidentes y donde se concentra la mayor perdida financiera.
