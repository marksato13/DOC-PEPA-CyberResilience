# Pagina 6 - Arquitectura

## Que poner en la diapositiva

**Titulo:**

```text
Arquitectura PEPA CyberResilience
```

**Diagrama con IDs:**

```text
[T1] CSV incidentes
[T2] CSV eventos tecnicos
[T3] CSV metricas operativas
        |
        v
[P1] Ingesta Pandas/Spark
        |
        v
[P2] Limpieza y estandarizacion
        |
        v
[P3] Agregacion anti-duplicados T2/T3
        |
        v
[P4] Join triple LEFT JOIN
        |
        v
[DS1] Parquet particionado Attack_Type/Year
        |
        v
[UI1] Dashboard Streamlit
        |
        +--> KPIs y visualizaciones
        +--> MLlib

[P7] Event Generator -> [DS2] live_events.csv -> [UI1]

Futuro produccion:
Suricata/Wazuh/Zeek/Syslog -> [FUT1] Kafka/Parser -> [DS2] -> [UI1]
```

## Imagen o icono sugerido

- CSV/documentos para T1, T2, T3.
- Logo o icono de Spark para P1-P4.
- Base de datos para Parquet.
- Dashboard para Streamlit.
- Rayo/en vivo para Event Generator.
- Kafka como componente futuro.

## Que explicar oralmente

> La arquitectura tiene dos caminos. El primero es batch: las tres fuentes CSV pasan por Spark, se limpian, se agregan, se unen y se guardan en Parquet. El segundo es live: el generador de eventos escribe en `live_events.csv` y el dashboard lo actualiza. En produccion, esa parte live se puede reemplazar por Kafka o logs reales.

## Respuesta sobre Kafka

> Kafka no esta implementado en la version actual. Esta planteado como mejora del modo produccion para reemplazar `live_events.csv` por una cola real de eventos.

## Cuello de botella

Posibles cuellos de botella:

```text
P1: lectura inicial si los datos crecen demasiado.
P4: join si no se agregan T2/T3 antes.
DS2: live_events.csv si crece mucho.
UI1: dashboard si lee demasiados eventos.
Spark local: limitado por RAM y CPU de una VM.
```

Respuesta corta:

> El cuello de botella principal puede estar en Spark local y en el feed CSV si crece mucho. Por eso proponemos Kafka y almacenamiento historico como mejora.
