# Preguntas probables del profesor

## Por que usaron Spark?

Porque Spark permite procesar grandes volumenes, hacer transformaciones reproducibles, agregaciones y joins. Ademas, permite escalar en el futuro a un cluster.

## Por que usaron Parquet?

Porque Parquet es columnar, comprimido y eficiente para analisis. Es mejor que CSV para consultas analiticas y funciona bien con Spark y Pandas.

## El feed en vivo es real?

En esta version es simulado. Corresponde al modo demo. En produccion se reemplaza por logs reales de Suricata, Wazuh, Zeek, Syslog, firewall o SIEM.

## Que dashboard es mas importante?

Depende del publico:

- Dashboard 01: vision ejecutiva.
- Dashboard 02: impacto financiero/geografico.
- Dashboard 03: valor tecnico del join entre tablas.
- Dashboard 04: ML, feed en vivo y ruta a produccion.

## Que aporta frente a Excel?

PEPA aporta:

- Pipeline reproducible.
- Procesamiento con Spark.
- Almacenamiento Parquet.
- Dashboard interactivo.
- Filtros globales.
- Feed en vivo.
- Ruta hacia logs reales.
- Posibilidad de ML.

## Que aporta T2 y T3?

T2 aporta severidad y eventos tecnicos.

T3 aporta datos comprometidos, duracion, respuesta y mitigacion.

Juntas enriquecen T1.


## Que modelo usaron para el Objetivo 2?

El modelo principal es `RandomForestClassifier` de Spark MLlib. Se usa para clasificacion multiclase, intentando predecir el tipo de ataque (`attack_type`) con variables tecnicas de T3.

Variables base:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

La evaluacion usa `CrossValidator`, `F1 Score` y `Accuracy`.

En la version extendida `ml_v2.py` tambien se comparan `DecisionTree`, `Multilayer Perceptron` y `GBTClassifier`.

Respuesta corta para exposicion:

> Usamos Random Forest con Spark MLlib como modelo principal del Objetivo 2. Se entrena con variables tecnicas de T3 para clasificar el tipo de ataque.

## El objetivo 2 usa solo T3?

Si. En el codigo actual, la parte ML del objetivo 2 se entrena con T3. T3 contiene las variables tecnicas mas apropiadas para clasificacion: `data_compromised_GB`, `attack_duration_min`, `attack_severity`, `response_time_min` y variables categoricas como sistema objetivo, industria y metodo de mitigacion.

T1 y T2 son esenciales para el objetivo 1 y para los dashboards de impacto, severidad y finanzas, pero el entrenamiento ML del objetivo 2 usa T3.

## Por que los modelos ML no son perfectos?

Porque la clasificacion multiclase de ataques se entrena sobre T3 y requiere datos reales, variables fuertes y balanceo de clases. El objetivo actual es demostrar la arquitectura ML, no vender un modelo final de produccion.

## A quien va dirigido?

- Estudiantes.
- Docentes.
- Investigadores.
- Analistas SOC.
- Equipos de ciberseguridad.
- Personas que necesitan un laboratorio Big Data replicable.

## Observaciones criticas de defensa

Para las preguntas sobre arquitectura, IDs, Kafka, streaming y cuellos de botella, usar tambien la guia completa:

```text
docs/13-defensa-preguntas-sustento.md
```

Respuestas rapidas:

- Arquitectura: el dashboard `UI1` consume `DS1 Parquet` y `DS2 live_events.csv`; Kafka queda como `FUT1` para produccion.
- IDs: usar `T1/T2/T3`, `P1-P8`, `DS1/DS2`, `UI1`, `FUT1`, `KPI-01` y `VIZ-01` a `VIZ-12`.
- Streaming: actualmente es demo con `event_generator.py`; en produccion se reemplaza por Kafka/logs reales.
- Cuello de botella: puede aparecer en agregacion, join triple, lectura del dashboard, feed vivo y entrenamiento ML.
- Sustento principal: se agregan T2 y T3 antes del join para evitar duplicar los incidentes de T1.

## Como se podria llevar a produccion?

Con modo 3:

```text
Logs reales -> parser -> esquema normalizado -> dashboard / almacenamiento / alertas
```

Fuentes posibles:

- Suricata.
- Wazuh.
- Zeek.
- Syslog.
- Firewall.
- SIEM.
