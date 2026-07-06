# Flujo detallado de arquitectura y scripts usados

Este documento explica el flujo completo de la arquitectura PEPA CyberResilience en relacion con el diagrama final. Sirve para defender la exposicion, explicar cada conector y demostrar que la imagen corresponde al producto implementado.

## Resumen ejecutivo del flujo

La arquitectura tiene tres flujos principales:

```text
1. Flujo batch historico
   T1/T2/T3 -> pipeline_linux.py -> Spark -> Parquet -> dashboard.py

2. Flujo ML
   T3 -> pipeline_linux.py / ml_v2.py -> metricas ML -> dashboard.py

3. Flujo live demo
   event_generator.py -> live_events.csv -> dashboard.py
```

La imagen del Word/Draw.io representa correctamente la arquitectura del producto si se entiende asi:

- El bloque central es el pipeline batch implementado con Spark.
- El bloque de almacenamiento contiene el Parquet historico y el CSV live.
- El dashboard lee el Parquet y el CSV live.
- La parte Kafka/logs reales es una evolucion futura, no algo implementado en la version actual.

## Scripts usados por fase

| Fase | Script principal | Rol | Estado |
| --- | --- | --- | --- |
| Fase 1 - Ingesta | `pipeline_linux.py` | Lee T1, T2 y T3 desde CSV con Pandas | Implementado |
| Fase 2 - Spark Session | `pipeline_linux.py` | Crea Spark local `local[*]` con configuracion de memoria | Implementado |
| Fase 3 - Transformacion | `pipeline_linux.py` | Limpia nombres, extrae anio y filtra ataques validos | Implementado |
| Fase 4 - Agregacion pre-join | `pipeline_linux.py` | Agrupa T2 y T3 antes del join para evitar duplicados | Implementado |
| Fase 5 - Join triple | `pipeline_linux.py` | Une T1 + T2_agg + T3_agg con LEFT JOIN | Implementado |
| Fase 6 - Escritura Parquet | `pipeline_linux.py` | Escribe `cybersecurity_joined` particionado por ataque y anio | Implementado |
| Fase 7 - EDA post join | `pipeline_linux.py` | Muestra resumenes analiticos por consola | Implementado |
| Fase 8 - ML v1 | `pipeline_linux.py` | Entrena RandomForest con variables base de T3 | Implementado |
| ML v2 mejorado | `ml_v2.py` | Compara RF, DT, MLP y GBT con variables de T3 | Implementado |
| Feed en vivo | `event_generator.py` | Genera eventos simulados y escribe `live_events.csv` | Implementado |
| Dashboard | `dashboard.py` | Lee Parquet y CSV live; muestra KPIs, graficos, ML y feed | Implementado |

## Rutas de scripts en el proyecto

En el proyecto implementado, los scripts principales estan en:

```text
codigo_sistema/pipeline_linux.py
codigo_sistema/ml_v2.py
codigo_sistema/event_generator.py
codigo_sistema/dashboard.py
```

Tambien existen copias de entrega en:

```text
entrega_tecnica/scripts/pipeline_linux.py
entrega_tecnica/scripts/ml_v2.py
entrega_tecnica/scripts/event_generator.py
entrega_tecnica/scripts/dashboard.py
```

Para exposicion, mencionar los nombres de los scripts, no es necesario mostrar codigo completo.

---

# Flujo por fases

## Fase 0 - Fuentes de datos

### Componentes del diagrama

```text
[T1] Global Threats
[T2] AI/ML Events
[T3] Synthesized Data
[P7] Generador en vivo
```

### Archivos de entrada

```text
T1_global_threats.csv
T2_ai_ml_events.csv
T3_synthesized.csv
```

### Explicacion

T1, T2 y T3 son las fuentes historicas del pipeline batch. No tienen el mismo nivel de detalle, por eso no se unen directamente desde el inicio.

T1 es la tabla maestra porque contiene los incidentes principales:

```text
Country
Year
Attack_Type
Target_Industry
Financial_Loss_M
Affected_Users
Attack_Source
Vulnerability_Type
Defense_Mechanism
Resolution_Hours
```

T2 aporta eventos tecnicos:

```text
Event_ID
Timestamp
Source_IP
Destination_IP
Attack_Type
Attack_Severity
Data_Exfiltrated
Threat_Intel
Response_Action
```

T3 aporta variables operativas y variables para ML:

```text
attack_type
attacker_ip
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
mitigation_method
```

### Conector correcto en el diagrama

```text
T1/T2/T3 -> pipeline Spark
```

El generador en vivo no debe entrar al pipeline Spark batch. Su conector correcto es:

```text
event_generator.py -> live_events.csv -> Dashboard
```

### Que decir en exposicion

> El sistema parte de tres fuentes historicas. T1 aporta el contexto de negocio, T2 aporta eventos tecnicos y T3 aporta metricas operativas y variables ML. Ademas, existe una fuente live simulada que no pasa por el batch, sino que alimenta el dashboard en tiempo casi real.

---

## Fase 1 - Ingesta con Pandas

### Script usado

```text
pipeline_linux.py
```

### Entrada

```text
~/bigdata/tablas/T1_global_threats.csv
~/bigdata/tablas/T2_ai_ml_events.csv
~/bigdata/tablas/T3_synthesized.csv
```

### Proceso

El script lee los tres CSV con Pandas:

```text
pd.read_csv(T1_PATH)
pd.read_csv(T2_PATH)
pd.read_csv(T3_PATH)
```

Ademas, T3 se filtra por tipos de ataque validos y se muestrea para el pipeline base:

```text
T3 completo -> filtro attack_type valido -> muestra de 6,000 filas
```

### Salida

```text
pd_t1
pd_t2
pd_t3
```

### Que explicar

> La ingesta inicia con Pandas porque facilita leer los CSV y hacer un primer control de volumen. Luego esos datos pasan a Spark para el procesamiento distribuido.

### Pregunta probable

**Por que usan Pandas si el proyecto es Big Data?**

Respuesta:

> Pandas se usa solo como etapa inicial de lectura y prefiltrado. El procesamiento importante, las agregaciones, joins y ML se hacen con Spark.

---

## Fase 2 - Spark Session

### Script usado

```text
pipeline_linux.py
```

### Proceso

Se crea una sesion Spark local:

```text
SparkSession.builder
  .appName("BigData_UPeU_Pipeline")
  .master("local[*]")
  .config("spark.driver.memory", "5g")
  .config("spark.sql.shuffle.partitions", "4")
```

Luego los DataFrames de Pandas pasan a Spark:

```text
pd_t1 -> t1_raw
pd_t2 -> t2_raw
pd_t3 -> t3_raw
```

### Salida

```text
t1_raw
t2_raw
t3_raw
```

### Que explicar

> Spark es el motor de procesamiento. En esta version corre en modo local porque estamos en una VM de laboratorio, pero la misma logica puede migrar a un cluster.

### Pregunta probable

**Spark esta en cluster?**

Respuesta:

> En esta version corre en modo local `local[*]`, usando los nucleos disponibles de la VM. La arquitectura esta preparada para escalar a cluster si el volumen crece.

---

## Fase 3 - Transformacion y limpieza

### Script usado

```text
pipeline_linux.py
```

### Proceso en T1

Se renombran columnas para tener nombres limpios y consistentes:

```text
Attack Type -> Attack_Type
Target Industry -> Target_Industry
Financial Loss (in Million $) -> Financial_Loss_M
Number of Affected Users -> Affected_Users
Attack Source -> Attack_Source
Security Vulnerability Type -> Vulnerability_Type
Defense Mechanism Used -> Defense_Mechanism
Incident Resolution Time (in Hours) -> Resolution_Hours
```

### Proceso en T2

Se extrae el anio desde `Timestamp`, se renombran columnas y se filtran ataques validos:

```text
Timestamp -> Year
Attack Type -> Attack_Type
Attack Severity -> Attack_Severity
Event ID -> Event_ID
Source IP -> Source_IP
Destination IP -> Destination_IP
Data Exfiltrated -> Data_Exfiltrated
```

Tambien se hace una muestra de T2:

```text
T2 limpio -> sample 25%
```

### Proceso en T3

Se extrae el anio desde `timestamp` y se conserva el subconjunto filtrado:

```text
timestamp -> Year
T3 filtrado -> 6,000 filas base en pipeline v1
```

### Salida

```text
t1
t2_sampled
t3_sampled
```

### Que explicar

> Esta fase convierte datos heterogeneos en estructuras compatibles. Sin esta limpieza, el join y las visualizaciones fallarian o tendrian nombres inconsistentes.

---

## Fase 4 - Agregacion pre-join

### Script usado

```text
pipeline_linux.py
```

### Por que existe esta fase

Esta es una de las fases mas importantes para defender la arquitectura.

T2 y T3 tienen mayor granularidad que T1. Si se hace un join directo, se pueden multiplicar filas de T1 y distorsionar KPIs.

### Agregacion de T2

T2 se agrupa por:

```text
Attack_Type
Year
```

Metricas generadas:

```text
t2_total_eventos
t2_avg_severity
t2_exfiltrations
t2_unique_ips
```

### Agregacion de T3

T3 se agrupa por:

```text
Attack_Type
```

Metricas generadas:

```text
t3_total_sesiones
t3_avg_data_GB
t3_avg_duration_min
t3_avg_severity
t3_avg_response_min
t3_mitigations_count
```

### Salida

```text
t2_agg
t3_agg
```

### Conector correcto

```text
T2 limpio -> T2_agg -> Join triple
T3 limpio -> T3_agg -> Join triple
```

### Que explicar

> Antes del join agregamos T2 y T3 para evitar duplicados. Esta estrategia mantiene a T1 como tabla maestra y permite enriquecerla con metricas tecnicas.

### Pregunta probable

**Por que no unieron las tres tablas directamente?**

Respuesta:

> Porque T2 y T3 tienen mas registros y mayor granularidad. Un join directo multiplicaria incidentes. Por eso agregamos primero y despues hacemos LEFT JOIN.

---

## Fase 5 - Join triple

### Script usado

```text
pipeline_linux.py
```

### Proceso

El join implementado es:

```text
T1 LEFT JOIN T2_agg ON Attack_Type + Year
Resultado LEFT JOIN T3_agg ON Attack_Type
```

### Por que T1 queda como base

T1 contiene el incidente principal. T2 y T3 enriquecen ese incidente, pero no reemplazan la tabla maestra.

### Salida

```text
resultado
```

Este resultado conserva la base de T1 y agrega columnas enriquecidas:

```text
t2_total_eventos
t2_avg_severity
t2_exfiltrations
t2_unique_ips
t3_total_sesiones
t3_avg_data_GB
t3_avg_duration_min
t3_avg_severity
t3_avg_response_min
t3_mitigations_count
```

### Que explicar

> El join triple es el centro del Objetivo 1. Permite pasar de fuentes separadas a un dataset consolidado para analisis.

### Pregunta probable

**Por que el resultado final tiene cerca de 3,000 filas si las fuentes suman mas de 123,000 registros?**

Respuesta:

> Porque T1 es la tabla maestra. T2 y T3 se agregan antes del join. El objetivo no es multiplicar filas, sino enriquecer los incidentes principales de T1.

---

## Fase 6 - Escritura Parquet

### Script usado

```text
pipeline_linux.py
```

### Proceso

El resultado se guarda en formato Parquet:

```text
resultado.write
  .mode("overwrite")
  .partitionBy("Attack_Type", "Year")
  .parquet("~/bigdata/output/cybersecurity_joined")
```

### Salida

```text
~/bigdata/output/cybersecurity_joined
```

### Por que Parquet

Parquet es adecuado para analitica porque:

- Es columnar.
- Comprime mejor que CSV.
- Permite leer solo columnas necesarias.
- Funciona bien con Spark y Pandas.
- Permite particionar por `Attack_Type` y `Year`.

### Conector correcto

```text
Join triple -> Parquet cybersecurity_joined -> dashboard.py
```

### Que explicar

> Parquet es nuestro almacenamiento analitico. El dashboard no lee los CSV originales; lee el resultado consolidado ya procesado.

---

## Fase 7 - EDA post join

### Script usado

```text
pipeline_linux.py
```

### Proceso

Despues del join, el script genera resumenes por consola:

```text
Top Attack Types por perdida financiera promedio
Incidentes y perdidas por anio
Severidad T2 vs datos comprometidos T3
```

### Salida

No genera un archivo principal nuevo; sirve como validacion y analisis exploratorio.

### Que explicar

> Esta fase valida que el dataset integrado tenga sentido antes de visualizarlo. Es una revision tecnica del resultado del join.

---

## Fase 8 - ML Pipeline v1

### Script usado

```text
pipeline_linux.py
```

### Entrada

```text
t3_sampled
```

### Variables usadas

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

### Modelo principal

```text
RandomForestClassifier - Spark MLlib
```

### Proceso

```text
StringIndexer -> VectorAssembler -> StandardScaler -> RandomForestClassifier -> CrossValidator
```

### Evaluacion

```text
F1 Score
Accuracy
CrossValidator 3-fold
```

### Que explicar

> El Objetivo 2 usa T3 porque T3 contiene variables tecnicas mas adecuadas para clasificacion. El modelo principal es Random Forest con Spark MLlib.

---

## ML Pipeline v2 mejorado

### Script usado

```text
ml_v2.py
```

### Entrada

```text
~/bigdata/tablas/T3_synthesized.csv
```

### Tarea 1 - Clasificacion multiclase

Objetivo:

```text
Predecir attack_type
```

Modelos comparados:

```text
RandomForest
DecisionTree
Multilayer Perceptron
```

Variables numericas:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

Variables categoricas con OneHotEncoding:

```text
target_system
outcome
security_tools_used
user_role
industry
mitigation_method
```

### Tarea 2 - Clasificacion binaria

Objetivo:

```text
Predecir outcome Success/Failure
```

Modelo:

```text
GBTClassifier
```

Metricas:

```text
AUC-ROC
Accuracy
F1
```

### Conector correcto

```text
T3 -> ml_v2.py -> metricas ML -> dashboard.py
```

Si el diagrama muestra un bloque `Modelos ML resultados`, debe entenderse como metricas calculadas del entrenamiento.

### Nota importante para exposicion

El dashboard actual muestra metricas ML como resultados comparativos. Si preguntan si el dashboard entrena en vivo, la respuesta correcta es:

> No entrena en vivo. El entrenamiento se ejecuta en los scripts ML y el dashboard presenta las metricas resultantes.

---

## Flujo live demo

### Script usado

```text
event_generator.py
```

### Entrada

No usa T1/T2/T3. Genera eventos simulados.

### Proceso

Cada 0.8 a 2.5 segundos genera una fila con campos como:

```text
timestamp
attack_type
severity
country
industry
data_GB
outcome
duration_min
```

### Salida

```text
~/bigdata/output/live_events.csv
```

### Conector correcto

```text
event_generator.py -> live_events.csv -> dashboard.py
```

### Que explicar

> El feed vivo es modo demo. Simula eventos para demostrar monitoreo en tiempo casi real. En produccion, esta parte se reemplazaria por Kafka, Syslog, Suricata, Wazuh, Zeek o firewall logs.

### Pregunta probable

**El streaming es real?**

Respuesta:

> Es streaming simulado en modo demo. La arquitectura deja la ruta para produccion, donde `live_events.csv` puede ser reemplazado por Kafka y logs reales parseados.

---

## Dashboard Streamlit

### Script usado

```text
dashboard.py
```

### Entradas

```text
~/bigdata/output/cybersecurity_joined
~/bigdata/output/live_events.csv
```

### Lectura estatica

El dashboard lee el Parquet consolidado:

```text
pd.read_parquet(PARQUET)
```

Cache:

```text
ttl = 60 segundos
```

### Lectura live

El dashboard lee el CSV vivo:

```text
pd.read_csv(LIVE_CSV, parse_dates=["timestamp"])
```

Cache:

```text
ttl = 2 segundos
```

Autorefresh:

```text
cada 3 segundos
```

### Salidas visuales

El dashboard muestra:

```text
KPI Cards
Graficas Plotly
Filtros por anio, tipo de ataque e industria
Panel ML
Feed en vivo
Tabla del Parquet filtrado
```

### Conector correcto

```text
Parquet -> dashboard.py -> KPIs/graficos/filtros/tabla
live_events.csv -> dashboard.py -> feed vivo/KPI live
metricas ML -> dashboard.py -> panel ML
```

### Que explicar

> El dashboard es la capa de consumo. No reemplaza al pipeline; muestra lo que el pipeline ya preparo y agrega el feed vivo de eventos simulados.

---

# Correcciones recomendadas para el diagrama

## 1. Corregir textos

Cambiar:

```text
FUETES DE DATOS
reasultados
```

Por:

```text
FUENTES DE DATOS
resultados
```

## 2. Separar el feed vivo del pipeline batch

El conector correcto es:

```text
Generados en vivo -> live_events.csv -> Dashboard
```

No debe parecer que `event_generator.py` entra al Spark batch.

## 3. Agregar doble lectura del dashboard

El dashboard debe tener dos flechas claras:

```text
cybersecurity_joined Parquet -> Dashboard
live_events.csv -> Dashboard
```

## 4. Aclarar bloque ML

Cambiar:

```text
Modelos ML (resultados)
```

Por:

```text
Metricas ML calculadas
RF / DT / MLP / GBT
```

Si no se genera archivo fisico de resultados ML, usar flecha punteada hacia dashboard.

## 5. Aclarar Kafka como futuro

Si se agrega Kafka, debe decir:

```text
FUTURO / MODO PRODUCCION
Logs reales -> Kafka -> Parser -> Dashboard / Alertas
```

No decir que Kafka ya esta implementado.

## 6. Aclarar Objetivo 2

Agregar texto cerca del bloque ML:

```text
Objetivo 2 usa T3 para ML
```

Esto evita confundir el join del Objetivo 1 con el entrenamiento del Objetivo 2.

---

# Guion oral para explicar la arquitectura

> Nuestra arquitectura tiene tres flujos. El primero es el flujo batch: T1, T2 y T3 entran al script `pipeline_linux.py`. Primero se leen con Pandas, luego se convierten a Spark DataFrames, se limpian, se agregan T2 y T3 para evitar duplicados, se hace el join triple y se guarda el resultado en Parquet particionado por tipo de ataque y anio.
>
> El segundo flujo es machine learning. Para el Objetivo 2 usamos T3 porque contiene variables tecnicas como datos comprometidos, duracion, severidad y tiempo de respuesta. Con `pipeline_linux.py` entrenamos un Random Forest base y con `ml_v2.py` comparamos RandomForest, DecisionTree, MLP y GBT.
>
> El tercer flujo es live demo. El script `event_generator.py` genera eventos simulados y los escribe en `live_events.csv`. El dashboard `dashboard.py` lee ese archivo cada pocos segundos y muestra el feed en vivo. En produccion, esa parte se puede reemplazar por Kafka y logs reales.
>
> Finalmente, el dashboard Streamlit consume el Parquet consolidado para KPIs y graficos historicos, y consume `live_events.csv` para eventos en vivo. Por eso no es solo un dashboard, sino una arquitectura completa de ingesta, procesamiento, almacenamiento, visualizacion, ML y ruta a monitoreo real.

---

# Preguntas de defensa sobre la arquitectura

## La imagen es igual al producto implementado?

Si, representa el producto implementado como arquitectura conceptual. Lo implementado actualmente es:

```text
Spark batch
Parquet
Dashboard Streamlit
ML con Spark MLlib
Feed vivo simulado
```

Lo futuro es:

```text
Kafka
logs reales
parser SIEM
alertas productivas
```

## Donde esta el cuello de botella?

Posibles puntos:

```text
P1 - lectura inicial si crecen los CSV
P3 - agregacion si T2/T3 crecen mucho
P4 - join triple si no se agregan antes
DS1 - lectura del Parquet si no se filtra bien
DS2 - live_events.csv si crece indefinidamente
UI1 - dashboard si recarga demasiados datos
ML - entrenamiento si se usa todo T3 sin cluster
```

## Por que el feed vivo no usa Spark?

Porque en modo demo el feed se resuelve con un CSV vivo para demostrar la visualizacion. Spark Streaming o Kafka se justifica en modo produccion con logs reales continuos.

## Por que T3 se usa en ML?

Porque T3 tiene variables tecnicas mas directas para clasificar ataques:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

## Por que el dashboard lee Parquet y no CSV?

Porque el Parquet ya contiene el resultado consolidado del pipeline. Leer CSV brutos desde el dashboard romperia la separacion entre procesamiento y visualizacion.
