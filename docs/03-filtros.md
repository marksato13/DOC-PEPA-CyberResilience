# Filtros del dashboard

## Filtros disponibles

El dashboard tiene filtros interactivos dentro del boton de filtros:

```text
Periodo
Tipo de ataque
Industria
```

## Como funcionan

Los filtros se aplican sobre el dataframe consolidado que viene del Parquet.

En el codigo se crea un dataframe filtrado llamado `dff`:

```python
filt = (
    df["Year"].between(sel_years[0], sel_years[1]) &
    df["Attack_Type"].isin(sel_attacks) &
    df["Target_Industry"].isin(sel_ind)
)
dff = df[filt]
```

Todos los KPIs y graficos usan `dff`, por eso cambian automaticamente cuando se cambia un filtro.

## Filtro Periodo

Permite analizar un rango de anios.

Ejemplo de exposicion:

> Si seleccionamos 2020 a 2025, todos los graficos muestran solo incidentes de ese periodo.

## Filtro Tipo de ataque

Permite seleccionar ataques especificos:

- DDoS.
- Malware.
- Man-in-the-Middle.
- Phishing.
- Ransomware.
- SQL Injection.

Ejemplo:

> Si seleccionamos solo Ransomware, podemos analizar su perdida financiera, severidad y datos comprometidos sin mezclarlo con otros ataques.

## Filtro Industria

Permite analizar sectores especificos:

- Healthcare.
- Finance.
- Government.
- Education.
- Retail.
- Technology.
- Energy.
- Manufacturing.

Ejemplo:

> Si filtramos Finance, el dashboard se convierte en una vista especializada para riesgo del sector financiero.

## Importancia de los filtros

Los filtros hacen que el dashboard no sea una imagen estatica. Lo convierten en una herramienta de exploracion.

Sirven para responder preguntas como:

- Que ataque fue mas costoso en un periodo?
- Que industria fue mas afectada?
- Que paises concentran mas incidentes?
- Como cambia la severidad si filtro solo un ataque?
- Que ocurre si comparo ransomware contra phishing?

## Pregunta probable del profesor

Pregunta:

> Los filtros modifican solo un grafico o todo el dashboard?

Respuesta:

> Modifican todos los KPIs y graficos que dependen del dataframe filtrado `dff`. Por eso el analisis es consistente en toda la vista.
