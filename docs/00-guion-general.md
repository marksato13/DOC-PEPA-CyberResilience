# Guion general de exposicion

## Apertura recomendada

> PEPA CyberResilience es una Plataforma de Eventos y Procesamiento Analitico aplicada a ciberseguridad. Nuestro objetivo fue construir una arquitectura Big Data replicable que permita analizar ataques, perdidas financieras, severidad, industrias afectadas y eventos en vivo usando Spark, Parquet, Streamlit y una base preparada para integrarse con logs reales tipo SIEM.

## Problema

Las organizaciones generan muchos datos de seguridad, pero normalmente estan dispersos:

- CSV historicos.
- Logs de IDS o firewall.
- Eventos de SIEM.
- Datos de incidentes.
- Datos tecnicos de severidad y respuesta.

El problema es que esos datos no siempre estan integrados ni preparados para analisis.

## Solucion propuesta

PEPA une varias fuentes, las procesa con Spark, genera un dataset Parquet y permite visualizar resultados en dashboards interactivos.

```text
T1_global_threats.csv
T2_ai_ml_events.csv
T3_synthesized.csv
        -> pipeline Spark
        -> join triple
        -> Parquet particionado
        -> dashboard Streamlit
        -> ML + feed en vivo
```

## Mensaje de venta

> No construimos solo graficos. Construimos un flujo completo: ingesta, transformacion, almacenamiento analitico, visualizacion, ML y una ruta hacia monitoreo en tiempo real.

## Cierre recomendado

> PEPA demuestra como una arquitectura Big Data puede aplicarse a ciberseguridad de forma practica. Procesa datos historicos, los enriquece con eventos tecnicos, permite analisis financiero y operativo, incorpora modelos de machine learning y deja preparada una ruta hacia monitoreo en tiempo real con logs reales.
