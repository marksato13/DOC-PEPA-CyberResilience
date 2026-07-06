# Presentacion de 9 paginas - PEPA CyberResilience

Esta guia convierte el proyecto PEPA CyberResilience en una presentacion clara para exponer y vender la solucion. Incluye texto sugerido para cada diapositiva, notas del expositor, imagenes/iconos recomendados, IDs para componentes y respuestas a observaciones del profesor.

## Convencion de IDs para la exposicion

Usen IDs visibles en la arquitectura y, si es posible, como etiquetas pequenas en las capturas del dashboard. Esto responde a la observacion del profesor sobre identificar componentes.

### Fuentes de datos

| ID | Componente | Archivo |
| --- | --- | --- |
| `T1` | Incidentes globales | `T1_global_threats.csv` |
| `T2` | Eventos tecnicos IA/ML | `T2_ai_ml_events.csv` |
| `T3` | Datos sinteticos/enriquecidos | `T3_synthesized.csv` |

### Proceso

| ID | Componente | Funcion |
| --- | --- | --- |
| `P1` | Ingesta | Lee CSV con Pandas/Spark |
| `P2` | Limpieza | Estandariza columnas y tipos |
| `P3` | Agregacion T2/T3 | Evita duplicados antes del join |
| `P4` | Join triple | Integra T1 + T2_agg + T3_agg |
| `P5` | Escritura Parquet | Guarda resultado particionado |
| `P6` | MLlib | Entrena/evalua modelos |
| `P7` | Event Generator | Simula eventos en vivo |
| `P8` | Dashboard Streamlit | Visualiza datos y feed |

### Almacenamiento y salida

| ID | Componente | Descripcion |
| --- | --- | --- |
| `DS1` | Parquet consolidado | `cybersecurity_joined` |
| `DS2` | Feed vivo CSV | `live_events.csv` |
| `UI1` | Dashboard publico | Streamlit + Cloudflare Tunnel |
| `FUT1` | Kafka futuro | Streaming real en modo produccion |

### Visualizaciones

| ID | Widget/Grafico | Dashboard |
| --- | --- | --- |
| `KPI-01` | Incidentes | General |
| `KPI-02` | Perdida USD | General |
| `KPI-03` | Usuarios afectados | General |
| `KPI-04` | Paises | General |
| `KPI-05` | Eventos vivo | General |
| `VIZ-01` | Distribucion por tipo de ataque | Dashboard 01 |
| `VIZ-02` | Perdida financiera por anio | Dashboard 01 |
| `VIZ-03` | Evolucion de incidentes | Dashboard 01 |
| `VIZ-04` | Top paises por incidentes | Dashboard 02 |
| `VIZ-05` | Perdida promedio por ataque | Dashboard 02 |
| `VIZ-06` | Perdida total por pais | Dashboard 02 |
| `VIZ-07` | Incidentes por industria | Dashboard 03 |
| `VIZ-08` | Severidad promedio | Dashboard 03 |
| `VIZ-09` | Datos comprometidos promedio | Dashboard 03 |
| `VIZ-10` | F1/Accuracy por algoritmo | Dashboard 04 |
| `VIZ-11` | Importancia de features | Dashboard 04 |
| `VIZ-12` | Feed en vivo | Dashboard 04 |

---

# PAGINA 1 - Titulo

## Titulo sugerido

**PEPA CyberResilience**

## Subtitulo

**Arquitectura Big Data para resiliencia informatica, analisis de ciberataques y monitoreo en vivo**

## Texto en diapositiva

- Plataforma de Eventos y Procesamiento Analitico.
- Spark + Parquet + Streamlit + ML + Feed en vivo.
- UPeU 2026-1.

## Autores

- Ruben Mark Salazar Tocas - Cod. 75184251
- Daniel H. Calderon Camacho - Cod. 71101519
- Uziel Elias Sauñe Fernandez - Cod. 75664788

## Imagen o icono recomendado

- Fondo: dashboard PEPA o captura del dashboard publico.
- Iconos: escudo de ciberseguridad, base de datos, grafico de barras, nube.
- Si quieren hacerlo mas personal: colocar el logo/nombre PEPA como marca principal.

## Nota para exponer

> PEPA significa Plataforma de Eventos y Procesamiento Analitico. Nuestro proyecto busca convertir datos dispersos de ciberseguridad en una arquitectura Big Data replicable, con procesamiento, almacenamiento, visualizacion, machine learning y una ruta hacia monitoreo en tiempo real.

---

# PAGINA 2 - Contexto del problema

## Titulo sugerido

**Contexto: la ciberseguridad ya es un problema de datos**

## Texto en diapositiva

- Las organizaciones generan miles o millones de eventos de seguridad.
- Los datos vienen de fuentes distintas: incidentes, logs, eventos tecnicos y datasets historicos.
- El reto no es solo almacenar datos, sino integrarlos y analizarlos a tiempo.
- Sin Big Data, el analisis queda fragmentado y reactivo.

## Imagen o icono recomendado

Usar una imagen conceptual con:

- Icono de alerta o ataque.
- Icono de servidor/logs.
- Icono de base de datos.
- Icono de analista SOC.

Ejemplo visual:

```text
Ataques -> logs dispersos -> analista saturado -> necesidad de Big Data
```

## Nota para exponer

> La ciberseguridad no solo es un problema tecnico, tambien es un problema de volumen, variedad y velocidad de datos. Los ataques dejan rastros en diferentes fuentes, pero si esas fuentes no se integran, la organizacion reacciona tarde.

## Pregunta probable

**Pregunta:** Por que Big Data en ciberseguridad?

**Respuesta:** Porque los eventos de seguridad son numerosos, heterogeneos y cambian rapido. Big Data permite procesarlos, correlacionarlos y extraer patrones.

---

# PAGINA 3 - Problema que queremos resolver

## Titulo sugerido

**Problema central: datos dispersos, poca correlacion y respuesta tardia**

## Texto en diapositiva

**Problema:**

> Como integrar fuentes heterogeneas de ciberseguridad para analizar ataques, medir impacto y preparar una capa de monitoreo en vivo?

**Dificultades:**

- T1, T2 y T3 tienen esquemas diferentes.
- T2 y T3 tienen mayor granularidad que T1.
- Un join directo podria duplicar registros.
- El dashboard necesita datos ya consolidados.
- El monitoreo en vivo requiere streaming o simulacion.

## Batch, streaming o ambos?

### Batch

Usado para:

- Datos historicos.
- CSV T1, T2 y T3.
- Limpieza, agregacion y join triple.
- Generacion de Parquet.

### Streaming

Usado para:

- Eventos en vivo.
- Feed del dashboard.
- Simulador `event_generator.py`.
- Futuro: logs reales con Kafka, Suricata, Wazuh o Syslog.

### Respuesta corta

> Necesitamos ambos. Batch para construir la base historica confiable y streaming para monitorear eventos recientes o en tiempo real.

## Imagen o icono recomendado

Dos columnas:

```text
Batch historico                  Streaming en vivo
CSV -> Spark -> Parquet          Logs/Eventos -> Feed -> Dashboard
```

Iconos:

- Batch: base de datos, engranaje, archivo CSV.
- Streaming: rayo, señal en vivo, cola de mensajes.

## Nota para exponer

> En nuestro proyecto, el batch ya esta implementado con Spark y Parquet. El streaming esta representado por un generador de eventos en vivo para modo demo. En modo produccion, ese generador puede reemplazarse por Kafka o logs reales.

## Pregunta probable

**Pregunta:** Donde aplicaron streaming?

**Respuesta:** En la capa de eventos en vivo. Actualmente se simula con `event_generator.py`, que escribe en `live_events.csv` y el dashboard lo refresca cada pocos segundos. En produccion, esa capa puede conectarse a Kafka, Suricata, Wazuh o Syslog.

---

# PAGINA 4 - Objetivos

## Titulo sugerido

**Objetivos del proyecto**

## Texto en diapositiva

### Objetivo 1 - Pipeline distribuido

Construir un pipeline con Apache Spark que integre T1, T2 y T3, evitando duplicados y generando un dataset Parquet particionado por tipo de ataque y anio.

### Objetivo 2 - Clasificacion ML con T3

Implementar modelos de clasificacion con Spark MLlib usando T3, porque esta tabla contiene variables tecnicas como datos comprometidos, duracion, severidad y tiempo de respuesta. El dashboard complementa este objetivo mostrando metricas, impacto y feed en vivo.

## Imagenes o iconos recomendados

Para Objetivo 1:

- Icono de pipeline.
- Icono de Spark.
- Icono de base de datos/Parquet.

Para Objetivo 2:

- Icono de machine learning.
- Icono de dashboard.
- Icono de grafico.

## Texto minimo para diapositiva

```text
OBJ-01 Pipeline Spark + Parquet
OBJ-02 ML con T3 + Dashboard + Feed vivo
```

## Nota para exponer

> El primer objetivo resuelve la parte de ingenieria de datos: integrar y preparar la informacion. El segundo objetivo usa T3 para la clasificacion ML, porque T3 tiene las variables tecnicas mas adecuadas para entrenar modelos; el dashboard comunica esos resultados junto con el analisis general.

## Aclaracion importante

```text
No decir: 123,000 registros consolidados finales.
Decir: 123,000 registros brutos procesados -> 3,000 incidentes enriquecidos.
Objetivo 2: ML con variables tecnicas de T3.
Modelo base: 4 variables numericas base.
```

---

# PAGINA 5 - Dataset utilizado

## Titulo sugerido

**Dataset utilizado: tres fuentes integradas**

## Texto en diapositiva

| ID | Fuente | Registros brutos | Uso en pipeline | Llave |
| --- | --- | ---: | --- | --- |
| `T1` | Global Cybersecurity Threats | 3,000 | Tabla maestra conservada | `Attack_Type`, `Year` |
| `T2` | AI/ML Cybersecurity Events | 20,000 | Eventos tecnicos filtrados/agregados | `Attack_Type`, `Year` |
| `T3` | Synthesized Cybersecurity Data | 100,000 | Metricas operativas / ML; muestra usada en pipeline | `Attack_Type` |

## Aclaracion importante

```text
T1 conserva sus 3,000 incidentes como tabla maestra.
T2 tiene 20,000 registros brutos, pero se filtra, muestrea y agrega por Attack_Type + Year.
T3 tiene 100,000 registros brutos, pero en pipeline_linux.py se filtra por tipos validos y se toma una muestra controlada de 6,000 registros para el procesamiento batch.
El Parquet final conserva los incidentes T1 enriquecidos, no 123,000 filas finales.
```

## Atributos principales por tabla

### T1 - Tabla maestra

| Campo pipeline | Uso |
| --- | --- |
| `Country` | Pais del incidente |
| `Year` | Anio del incidente |
| `Attack_Type` | Tipo de ataque y llave |
| `Target_Industry` | Industria afectada |
| `Financial_Loss_M` | Perdida financiera |
| `Affected_Users` | Usuarios afectados |
| `Attack_Source` | Origen del ataque |
| `Vulnerability_Type` | Vulnerabilidad explotada |
| `Defense_Mechanism` | Defensa usada |
| `Resolution_Hours` | Tiempo de resolucion |

### T2 - Eventos tecnicos

| Campo pipeline | Uso |
| --- | --- |
| `Event_ID` | Conteo de eventos |
| `Year` | Extraido desde `Timestamp` |
| `Source_IP` | IPs unicas |
| `Attack_Type` | Llave de integracion |
| `Attack_Severity` | Severidad categorizada |
| `Data_Exfiltrated` | Exfiltracion |
| `Threat_Intel` | Inteligencia de amenazas |
| `Response_Action` | Accion tomada |

Metricas generadas:

```text
t2_total_eventos
t2_avg_severity
t2_exfiltrations
t2_unique_ips
```

### T3 - Datos sinteticos/enriquecidos

| Campo pipeline | Uso |
| --- | --- |
| `attack_type` -> `Attack_Type` | Llave |
| `attacker_ip` | Conteo de sesiones |
| `data_compromised_GB` | Datos comprometidos |
| `attack_duration_min` | Duracion |
| `attack_severity` | Severidad |
| `response_time_min` | Tiempo de respuesta |
| `mitigation_method` | Mitigacion |

Metricas generadas:

```text
t3_total_sesiones
t3_avg_data_GB
t3_avg_duration_min
t3_avg_severity
t3_avg_response_min
t3_mitigations_count
```

## Imagen o icono recomendado

Usar tres tarjetas:

```text
T1 - Incidentes
T2 - Eventos tecnicos
T3 - Metricas operativas
```

Cada tarjeta con icono:

- T1: globo/alerta.
- T2: red/IP.
- T3: engranaje/metricas.

## Nota para exponer

> T1 es la tabla maestra. T2 y T3 no se unieron directamente fila por fila porque tienen mayor granularidad y generarian duplicados. Por eso primero se agregan y luego se hace LEFT JOIN para conservar los 3,000 incidentes T1 enriquecidos.

## Pregunta probable

**Pregunta:** Por que T2 y T3 se agregan antes del join?

**Respuesta:** Porque tienen mas registros que T1. Si se unen directamente, multiplicarian filas. La agregacion deja una fila por llave y conserva la integridad de T1.

---

# PAGINA 6 - Arquitectura

## Titulo sugerido

**Arquitectura PEPA CyberResilience**

## Diagrama recomendado

Usar IDs visibles:

```text
[T1] Incidentes globales CSV
[T2] Eventos tecnicos CSV
[T3] Datos sinteticos CSV
        |
        v
[P1] Ingesta Pandas/Spark
        |
        v
[P2] Limpieza y estandarizacion
        |
        v
[P3] Agregacion T2/T3 anti-duplicados
        |
        v
[P4] Join triple LEFT JOIN
        |
        v
[DS1] Parquet particionado Attack_Type/Year
        |
        v
[P8/UI1] Dashboard Streamlit
        |
        +--> [VIZ] KPIs, graficos, filtros
        +--> [P6] MLlib

[P7] Event Generator -> [DS2] live_events.csv -> [P8/UI1]

Futuro modo produccion:
Suricata/Wazuh/Zeek/Syslog -> [FUT1] Kafka/Parser -> [DS2] -> Dashboard
```

## Texto en diapositiva

- Batch: CSV -> Spark -> Parquet.
- Live: Event Generator -> live_events.csv -> Dashboard.
- Futuro: logs reales -> parser/Kafka -> dashboard.
- Streamlit funciona como capa de visualizacion.

## Como responder la observacion sobre Kafka

El profesor observo que otros grupos mostraban interaccion con landing page, BD y Kafka.

Respuesta recomendada:

> En nuestra arquitectura actual, la capa batch usa Parquet como almacenamiento analitico y Streamlit como interfaz. La capa live usa `live_events.csv` como mecanismo simple de demostracion. Kafka no esta implementado aun, pero esta definido como mejora del modo produccion para reemplazar el CSV vivo por una cola de eventos real.

## Donde esta el posible cuello de botella

- `P1`: carga inicial con Pandas si el dataset crece demasiado.
- `P4`: join triple si no se agregan T2/T3 antes.
- `DS2`: `live_events.csv` puede crecer mucho.
- `P8`: dashboard puede volverse lento si lee demasiados eventos en vivo.
- Spark local: limitado por RAM y CPU de una sola VM.

Respuesta corta:

> El cuello de botella principal puede estar en la lectura del feed vivo CSV si crece demasiado y en Spark local si el volumen aumenta. Por eso proponemos Kafka y almacenamiento historico como mejora.

## Imagen o icono recomendado

Usar un diagrama con flechas e IDs. No poner mucho texto.

Iconos:

- CSV/documento.
- Spark.
- Base de datos/Parquet.
- Dashboard.
- Kafka como futuro.
- Nube/tunel publico.

---

# PAGINA 7 - Demo del dashboard

## Titulo sugerido

**Demo: dashboard PEPA CyberResilience**

## URL publica

```text
https://immunology-doll-bios-treatments.trycloudflare.com
```

## Texto en diapositiva

- Dashboard interactivo con filtros.
- KPIs ejecutivos.
- Analisis financiero y geografico.
- Analisis por industria y severidad.
- ML y feed en vivo.

## Orden de demo recomendado

1. Abrir dashboard publico.
2. Mostrar KPIs superiores.
3. Mostrar filtros: periodo, tipo de ataque, industria.
4. Dashboard 01: tendencia y distribucion.
5. Dashboard 02: geografia y finanzas.
6. Dashboard 03: industria y severidad.
7. Dashboard 04: ML y feed en vivo.
8. Mostrar sidebar Sistema si preguntan infraestructura.

## IDs para explicar visualizaciones

Usar estas etiquetas al hablar:

```text
KPI-01 a KPI-05 -> resumen general
VIZ-01 a VIZ-03 -> tendencia y distribucion
VIZ-04 a VIZ-06 -> geografia y finanzas
VIZ-07 a VIZ-09 -> industria y severidad
VIZ-10 a VIZ-12 -> ML y feed vivo
```

## Texto para exponer los filtros

> Los filtros no modifican un solo grafico, modifican todo el dataframe filtrado. Si seleccionamos un periodo, tipo de ataque o industria, se recalculan KPIs y graficos, porque todos consumen el mismo dataset consolidado filtrado.

## Que decir sobre cada dashboard

### Dashboard 01

> Muestra vision ejecutiva: incidentes, perdidas, usuarios, paises y evolucion temporal.

### Dashboard 02

> Muestra impacto geografico y financiero: paises criticos y ataques mas costosos.

### Dashboard 03

> Muestra el valor del join: industria desde T1, severidad desde T2 y datos comprometidos desde T3.

### Dashboard 04

> Muestra la parte avanzada: ML, importancia de variables y feed de eventos en vivo.

## Imagen recomendada

Poner captura del dashboard con etiquetas pequenas:

```text
KPI-01, KPI-02, VIZ-01, VIZ-02, etc.
```

Esto responde a la observacion del profesor sobre poner IDs para identificar.

---

# PAGINA 8 - Conclusiones, limitaciones, mejoras, Git y modos

## Titulo sugerido

**Conclusiones y evolucion del proyecto**

## Conclusiones

- PEPA integra tres fuentes heterogeneas de ciberseguridad.
- Spark permite procesamiento batch reproducible.
- Parquet mejora el almacenamiento analitico.
- Streamlit permite visualizacion interactiva.
- El feed vivo demuestra una ruta hacia monitoreo en tiempo real.
- El proyecto queda abierto para implementacion y mejora.

## Limitaciones

- El feed actual es sintetico, no viene aun de un IDS real.
- Spark corre en modo local, no en cluster.
- El live feed usa CSV, que puede ser cuello de botella si crece mucho.
- Los modelos ML son demostrativos y requieren mas datos reales.
- El tunel publico sin dominio puede cambiar de URL.

## Mejoras futuras

- Integrar Kafka para streaming real.
- Integrar Suricata, Wazuh, Zeek o Syslog.
- Usar PostgreSQL/OpenSearch para historico operacional.
- Dockerizar la solucion.
- Agregar alertas por severidad.
- Desplegar con dominio y HTTPS.
- Mejorar ML con datos reales.

## Modos de implementacion

| Modo | Descripcion |
| --- | --- |
| Demo | Eventos sinteticos para presentar y probar |
| Laboratorio | Carga de datasets propios |
| Produccion | Logs reales normalizados con parsers/Kafka |

## GitHub

Repositorio del sistema:

```text
https://github.com/marksato13/PEPA-CyberResilience
```

Repositorio de documentacion:

```text
https://github.com/marksato13/DOC-PEPA-CyberResilience
```

## Nota para exponer

> La solucion actual ya funciona como laboratorio y demo. El siguiente paso es el modo produccion, reemplazando el generador sintetico por logs reales y una cola como Kafka.

---

# PAGINA 9 - Gracias

## Titulo sugerido

**Gracias**

## Texto en diapositiva

**PEPA CyberResilience**

Plataforma de Eventos y Procesamiento Analitico para ciberseguridad.

```text
Big Data + Ciberseguridad + Dashboard + ML + Monitoreo
```

## Datos para mostrar

- URL publica del dashboard.
- Repositorio GitHub.
- Nombres de los tres integrantes.

## Cierre oral

> Gracias. Con PEPA demostramos que es posible construir una arquitectura replicable para analizar ciberataques, visualizar impacto, aplicar ML y dejar preparada una evolucion hacia monitoreo real con logs de seguridad.

## Imagen recomendada

- Logo PEPA.
- Captura del dashboard.
- Icono de escudo/ciberseguridad.
- Foto del equipo o agradecimiento a PEPA si quieren cerrar con identidad del proyecto.

---

# Respuestas rapidas a observaciones del profesor

## 1. Arquitectura con landing/dashboard, BD y Kafka

Respuesta:

> Nuestra interfaz es Streamlit, que funciona como dashboard web. La base analitica es Parquet. Kafka no esta implementado en la version actual, pero esta planteado en el modo produccion para reemplazar el CSV vivo por streaming real.

## 2. Poner IDs en visualizaciones

Accion recomendada:

- En la diapositiva de demo, poner etiquetas `VIZ-01`, `VIZ-02`, etc.
- En arquitectura, poner `T1`, `T2`, `T3`, `P1`, `P2`, `DS1`, `DS2`, `UI1`, `FUT1`.

## 3. Donde aplicaste streaming

Respuesta:

> En la capa live. Actualmente se simula con `event_generator.py`, que genera eventos y los escribe en `live_events.csv`. El dashboard lee ese archivo con refresh automatico. En produccion, esa capa se reemplaza por Kafka o logs reales.

## 4. Donde puede ocurrir cuello de botella

Respuesta:

> Puede ocurrir en Spark local si crece el volumen, en el join si no se agregan T2 y T3 antes, y en el feed CSV si se acumulan demasiados eventos. Por eso proponemos Kafka y almacenamiento historico como mejora.

## 5. Por que batch y streaming

Respuesta:

> Batch sirve para datos historicos y generacion del Parquet consolidado. Streaming sirve para eventos recientes y monitoreo en vivo. La solucion necesita ambos porque ciberseguridad requiere analisis historico y respuesta en tiempo real.
