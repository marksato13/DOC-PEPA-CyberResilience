# Pagina 7 - Demo del dashboard

## Que poner en la diapositiva

**Titulo:**

```text
Demo: dashboard PEPA CyberResilience
```

**URL publica:**

```text
https://immunology-doll-bios-treatments.trycloudflare.com
```

**Texto corto:**

```text
Dashboard interactivo con filtros, KPIs, analisis financiero, severidad, ML y feed en vivo.
```

## Imagen sugerida

Usar una captura del dashboard con etiquetas pequenas:

```text
KPI-01 Incidentes
KPI-02 Perdida USD
KPI-03 Usuarios afectados
VIZ-01 Distribucion por ataque
VIZ-02 Perdida por anio
VIZ-12 Feed en vivo
```

Esto responde a la observacion del profesor sobre poner IDs.

## Que mostrar en vivo

Orden recomendado:

1. Abrir la URL publica.
2. Mostrar KPIs superiores.
3. Abrir filtros.
4. Filtrar por periodo.
5. Filtrar por tipo de ataque.
6. Mostrar Dashboard 01.
7. Mostrar Dashboard 02.
8. Mostrar Dashboard 03.
9. Mostrar Dashboard 04.
10. Mostrar feed en vivo.

## Que explicar oralmente sobre filtros

> Los filtros se aplican sobre el dataframe consolidado. Si cambiamos periodo, tipo de ataque o industria, se recalculan todos los KPIs y graficos que dependen del dataframe filtrado.

## Que explicar por dashboard

### Dashboard 01

> Muestra vision ejecutiva: incidentes, perdidas, usuarios afectados, paises y tendencia temporal.

### Dashboard 02

> Muestra impacto geografico y financiero: paises criticos y ataques mas costosos.

### Dashboard 03

> Demuestra el valor del join: industria desde T1, severidad desde T2 y datos comprometidos desde T3.

### Dashboard 04

> Muestra ML, importancia de variables y feed en vivo. El modelo principal es RandomForestClassifier de Spark MLlib, entrenado con variables tecnicas de T3.

## Pregunta probable

**El feed es real?**

> En esta version es sintetico para modo demo. En modo produccion se reemplaza por logs reales de Suricata, Wazuh, Zeek, Syslog o Kafka.
