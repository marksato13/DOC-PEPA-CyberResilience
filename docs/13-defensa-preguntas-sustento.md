# Defensa tecnica - preguntas, dudas y sustento

Este documento prepara respuestas para las observaciones que puede hacer el profesor durante la exposicion. La idea es responder con seguridad, sin entrar demasiado en codigo, pero mostrando que la arquitectura tiene sentido.

## Como usar esta guia

Para cada pregunta usen tres niveles:

1. **Respuesta corta:** lo que se dice primero en la exposicion.
2. **Sustento tecnico:** la razon que demuestra que no es solo una opinion.
3. **Que mostrar:** diapositiva, dashboard, ID o parte de la arquitectura que respalda la respuesta.

---

## 1. Donde se ve la interaccion entre landing/dashboard, datos y procesamiento?

**Respuesta corta:**

> En nuestra arquitectura, el dashboard Streamlit es la capa de visualizacion. No procesa los CSV brutos directamente; consume los resultados generados por el pipeline Spark y tambien lee el feed vivo simulado.

**Sustento tecnico:**

El flujo correcto es:

```text
T1 / T2 / T3
   -> P1 Ingesta
   -> P2 Limpieza
   -> P3 Agregacion
   -> P4 Join triple
   -> DS1 Parquet consolidado
   -> UI1 Dashboard Streamlit
```

Para eventos en vivo:

```text
P7 Event Generator
   -> DS2 live_events.csv
   -> UI1 Dashboard Streamlit
```

En modo produccion, `P7 Event Generator` se reemplaza por Kafka, logs reales o una herramienta SIEM/SOC.

**Que mostrar:**

- Pagina 6: arquitectura.
- ID `UI1`: dashboard.
- ID `DS1`: Parquet consolidado.
- ID `DS2`: feed vivo.
- ID `FUT1`: Kafka futuro.

**Como sustentarlo oralmente:**

> La visualizacion no esta aislada. Esta conectada a dos salidas del sistema: el Parquet consolidado para analisis historico y el CSV de eventos vivos para monitoreo demo. Por eso el dashboard representa tanto analisis batch como una aproximacion a streaming.

---

## 2. Usaron landing page o dashboard?

**Respuesta corta:**

> En esta version priorizamos el dashboard operativo. Si se requiere una landing page publica, iria antes del dashboard como capa de presentacion, pero la solucion principal es el panel analitico.

**Sustento tecnico:**

El producto actual esta enfocado en uso tecnico y academico:

```text
Usuario -> Dashboard Streamlit -> Parquet / feed vivo
```

Una landing page futura serviria para explicar el producto, mostrar autores, documentacion, enlaces y acceso al dashboard:

```text
Landing publica -> Boton Ver dashboard -> UI1 Dashboard
```

**Que mostrar:**

- Dashboard publico.
- README del GitHub.
- Diapositiva 8 con GitHub y modos de implementacion.

**Como sustentarlo oralmente:**

> No hicimos una landing de marketing porque el entregable funcional es el dashboard. Pero el README y el tunel publico cumplen esa funcion de acceso y documentacion. Como mejora, se puede agregar una landing simple que apunte al dashboard y a GitHub.

---

## 3. Por que es necesario poner IDs en la arquitectura y visualizaciones?

**Respuesta corta:**

> Los IDs ayudan a identificar cada fuente, proceso, almacenamiento y widget. Asi el profesor puede preguntar por una parte especifica y nosotros respondemos con precision.

**Sustento tecnico:**

IDs recomendados:

```text
T1, T2, T3       -> fuentes de datos
P1 - P8          -> procesos del pipeline
DS1, DS2         -> salidas de datos
UI1              -> dashboard
FUT1             -> Kafka futuro
KPI-01 - KPI-05  -> indicadores
VIZ-01 - VIZ-12  -> graficos
```

**Que mostrar:**

- Pagina 6: arquitectura con IDs.
- Pagina 7: demo del dashboard con IDs de widgets.
- Tabla de IDs en `docs/12-presentacion-9-paginas.md`.

**Como sustentarlo oralmente:**

> Los IDs no son decoracion. Funcionan como trazabilidad visual: permiten relacionar una tabla, un proceso y un grafico. Por ejemplo, si hablamos de `VIZ-08`, sabemos que viene de severidad agregada de T2 y se visualiza en el dashboard tecnico.

---

## 4. Donde y por que aplicaron streaming?

**Respuesta corta:**

> Aplicamos streaming en la capa de eventos en vivo del dashboard. Actualmente es modo demo con un generador de eventos, y en produccion se reemplazaria por Kafka o logs reales.

**Sustento tecnico:**

El proyecto tiene dos tipos de procesamiento:

```text
Batch:
CSV historicos -> Spark -> Parquet -> Dashboard

Streaming demo:
Event Generator -> live_events.csv -> Dashboard
```

El streaming es necesario porque los eventos de seguridad no siempre se analizan al final del dia. En un entorno real, alertas de firewall, IDS, SIEM o EDR deben observarse cerca del tiempo real.

**Que mostrar:**

- Pagina 3: batch vs streaming.
- Pagina 7: feed en vivo.
- Dashboard 04: `VIZ-12 Feed en vivo`.

**Como sustentarlo oralmente:**

> Batch nos sirve para consolidar historicos y obtener metricas confiables. Streaming nos sirve para monitoreo reciente. En esta etapa, el streaming esta simulado para demostrar el concepto sin depender de infraestructura externa.

---

## 5. Por que no usaron Kafka directamente?

**Respuesta corta:**

> Porque el alcance actual es academico y demostrativo. Usamos un generador de eventos para validar la visualizacion en vivo. Kafka queda como mejora natural para modo produccion.

**Sustento tecnico:**

Kafka tendria sentido cuando existan fuentes reales y continuas:

```text
Suricata / Wazuh / Zeek / Syslog / Firewall
   -> Kafka topic
   -> Spark Structured Streaming o consumidor Python
   -> Almacenamiento + Dashboard + alertas
```

En la version actual, meter Kafka sin fuentes reales agregaria complejidad sin mejorar la demostracion principal.

**Que mostrar:**

- Pagina 8: mejoras futuras.
- ID `FUT1`: Kafka futuro.
- Modos de implementacion: demo, laboratorio, produccion.

**Como sustentarlo oralmente:**

> Kafka no esta descartado. Esta ubicado como parte del modo produccion. Primero validamos el pipeline, el esquema, el dashboard y el feed. Luego Kafka reemplaza el archivo vivo cuando haya logs reales constantes.

---

## 6. En que parte puede ocurrir el cuello de botella?

**Respuesta corta:**

> Los cuellos de botella principales pueden aparecer en el join, en la lectura del dashboard si los datos crecen mucho, y en el feed vivo si se reemplaza por logs reales sin cola de mensajes.

**Sustento tecnico:**

Posibles cuellos de botella:

| Punto | Riesgo | Mitigacion |
| --- | --- | --- |
| `P3` Agregacion | T2/T3 tienen mas granularidad | Agregar antes del join |
| `P4` Join triple | Multiplicacion de filas si se une directo | Usar llaves y LEFT JOIN desde T1 |
| `DS1` Parquet | Muchos datos historicos | Particionar por `Attack_Type` y `Year` |
| `UI1` Dashboard | Leer demasiado en cada refresco | Cache, filtros y lectura columnar |
| `DS2` Feed vivo | Archivo crece o se bloquea | Kafka/cola en produccion |
| ML | Entrenamiento pesado | Muestreo, cluster Spark, tuning |

**Que mostrar:**

- Pagina 5: dataset y granularidad.
- Pagina 6: arquitectura.
- Pregunta de join en pagina 5.

**Como sustentarlo oralmente:**

> El riesgo mas importante es unir T1, T2 y T3 directamente. T2 y T3 tienen mas registros y podrian multiplicar filas. Por eso agregamos primero y despues hacemos LEFT JOIN. Esa decision reduce duplicados y hace mas estable el pipeline.

---

## 7. Por que hicieron agregacion antes del join?

**Respuesta corta:**

> Porque T2 y T3 tienen mayor granularidad que T1. Si se unen directamente, los incidentes de T1 pueden duplicarse.

**Sustento tecnico:**

T1 funciona como tabla maestra de incidentes. T2 y T3 enriquecen con metricas:

```text
T2 -> agregar por Attack_Type + Year
T3 -> agregar por Attack_Type
T1 LEFT JOIN T2_agg
resultado LEFT JOIN T3_agg
```

**Que mostrar:**

- Pagina 5: dataset utilizado.
- Pagina 6: arquitectura.
- Tabla de atributos T1/T2/T3.

**Como sustentarlo oralmente:**

> No queriamos inflar artificialmente los resultados. Por eso usamos T1 como base y agregamos T2 y T3 antes. Asi mantenemos la lectura de incidentes principales y agregamos informacion tecnica.

---

## 8. Por que T1 es la tabla maestra?

**Respuesta corta:**

> Porque T1 contiene los incidentes principales con pais, anio, industria, perdida financiera y usuarios afectados. Es la mejor base para analisis ejecutivo.

**Sustento tecnico:**

T1 permite responder:

- Donde ocurren los incidentes.
- En que anio.
- Que industria fue afectada.
- Cual fue el impacto financiero.
- Cuantos usuarios fueron afectados.

T2 y T3 complementan con severidad, eventos, duracion, datos comprometidos y variables ML.

**Que mostrar:**

- Pagina 5: T1 como tabla maestra.
- Dashboard 01 y 02.

**Como sustentarlo oralmente:**

> Si el objetivo es explicar impacto y resiliencia, necesitamos una tabla que tenga contexto de negocio. T1 aporta ese contexto; T2 y T3 aportan profundidad tecnica.

---

## 9. El Objetivo 2 usa solo T3?

**Respuesta corta:**

> Si. El Objetivo 2, que es ML, usa variables tecnicas de T3 porque son las mas adecuadas para clasificar tipos de ataque.

**Sustento tecnico:**

Variables base usadas:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

Modelo principal:

```text
RandomForestClassifier - Spark MLlib
```

T1 y T2 no se eliminan del proyecto. Se usan en el Objetivo 1 y en los dashboards de analisis e impacto.

**Que mostrar:**

- Pagina 4: objetivos.
- Dashboard 04: ML.
- Pregunta del profesor en `docs/10-preguntas-profesor.md`.

**Como sustentarlo oralmente:**

> El Objetivo 1 integra las tres fuentes. El Objetivo 2 usa T3 para ML porque tiene variables numericas tecnicas mas directas para entrenar el modelo. No es contradiccion; son objetivos distintos.

---

## 10. Que modelo usaron y por que?

**Respuesta corta:**

> Usamos Random Forest de Spark MLlib como modelo principal porque funciona bien para clasificacion multiclase, maneja relaciones no lineales y permite medir importancia de variables.

**Sustento tecnico:**

El problema es:

```text
Predecir attack_type usando variables tecnicas de T3
```

Evaluacion:

```text
CrossValidator
F1 Score
Accuracy
```

Version extendida:

```text
RandomForest
DecisionTree
Multilayer Perceptron
GBTClassifier
```

**Que mostrar:**

- Dashboard 04: F1/Accuracy.
- Dashboard 04: importancia de variables.

**Como sustentarlo oralmente:**

> Elegimos Random Forest como modelo principal por estabilidad e interpretabilidad. Ademas, en la version extendida comparamos otros algoritmos para no depender de un solo resultado.

---

## 11. Por que el modelo no tiene que ser perfecto?

**Respuesta corta:**

> Porque el objetivo academico es demostrar el pipeline ML y la comparacion de modelos, no afirmar que ya existe un detector listo para produccion.

**Sustento tecnico:**

Para un modelo productivo faltaria:

- Datos reales etiquetados.
- Balanceo de clases.
- Validacion temporal.
- Ajuste de hiperparametros.
- Monitoreo de drift.
- Evaluacion con falsos positivos y falsos negativos.

**Que mostrar:**

- Pagina 8: limitaciones y mejoras.
- Dashboard 04: metricas.

**Como sustentarlo oralmente:**

> En ciberseguridad, un modelo no se valida solo con accuracy. Tambien importan falsos positivos, falsos negativos y actualizacion con datos reales. Por eso lo presentamos como base ML, no como detector final.

---

## 12. Que aporta PEPA CyberResilience frente a un dashboard normal?

**Respuesta corta:**

> No es solo un dashboard. Es una arquitectura replicable: ingesta, limpieza, procesamiento distribuido, almacenamiento eficiente, visualizacion, ML y ruta a monitoreo en vivo.

**Sustento tecnico:**

Capas del sistema:

```text
Datos -> Spark -> Parquet -> Dashboard -> ML -> Feed vivo -> Produccion futura
```

Un dashboard normal solo visualiza. PEPA tambien documenta como construir el flujo de datos.

**Que mostrar:**

- README del GitHub.
- Pagina 6: arquitectura.
- Pagina 8: modos de implementacion.

**Como sustentarlo oralmente:**

> La diferencia es que nuestro producto tiene una estructura replicable. Otro equipo puede cambiar los datasets por sus propios logs y mantener la misma arquitectura.

---

## 13. A quien va dirigido el proyecto?

**Respuesta corta:**

> Va dirigido a estudiantes, docentes, laboratorios de ciberseguridad, analistas SOC y equipos que necesiten prototipar analitica de seguridad con Big Data.

**Sustento tecnico:**

Modos de uso:

| Modo | Usuario | Uso |
| --- | --- | --- |
| Demo | Profesor, jurado, visitante | Ver dashboard rapido |
| Laboratorio | Estudiantes, investigadores | Replicar pipeline y cambiar datos |
| Produccion | Equipo SOC / seguridad | Integrar logs reales y alertas |

**Que mostrar:**

- Pagina 8: modos de implementacion.
- GitHub open source.

**Como sustentarlo oralmente:**

> Lo vendemos como una base open source. No obliga a usar nuestros datos; permite reemplazarlos por datos propios y mantener la estructura.

---

## 14. Como lo usaria otra persona con sus propios datos?

**Respuesta corta:**

> Reemplazaria T1, T2 y T3 por fuentes equivalentes, respetando las columnas clave o adaptando el mapeo del pipeline.

**Sustento tecnico:**

Requisitos minimos:

```text
Una fuente maestra de incidentes
Una fuente tecnica de eventos
Una fuente operativa o ML
Llaves comunes como Attack_Type y Year
```

Si sus datos vienen de logs reales, primero se agrega una etapa de parsing:

```text
Logs reales -> parser -> esquema normalizado -> Spark -> Parquet -> Dashboard
```

**Que mostrar:**

- README del GitHub.
- Modos de implementacion.
- Arquitectura con IDs.

**Como sustentarlo oralmente:**

> Lo importante no son exactamente nuestros CSV, sino la arquitectura. Las fuentes pueden cambiar siempre que se normalicen a un esquema comun.

---

## 15. Por que se llama CyberResilience?

**Respuesta corta:**

> Porque no solo muestra ataques; ayuda a entender impacto, respuesta, defensa y recuperacion. Eso es resiliencia: observar, analizar y mejorar la capacidad de respuesta.

**Sustento tecnico:**

El proyecto mide:

- Ataques por tipo.
- Impacto financiero.
- Usuarios afectados.
- Severidad.
- Datos comprometidos.
- Tiempo de respuesta.
- Metodo de mitigacion.
- Feed vivo.

**Que mostrar:**

- KPIs principales.
- Dashboard 03: severidad y datos comprometidos.
- Dashboard 04: feed vivo y ML.

**Como sustentarlo oralmente:**

> CyberResilience significa pasar de solo registrar ataques a construir capacidad de respuesta. El dashboard ayuda a identificar patrones, impacto y posibles mejoras defensivas.

---

## 16. Que deben decir si preguntan por los IDs en los graficos?

**Respuesta corta:**

> Cada grafico tiene un ID para poder rastrear que dato responde y en que dashboard se encuentra.

**Sustento tecnico:**

Ejemplos:

```text
KPI-01 -> Incidentes totales
KPI-02 -> Perdida USD
VIZ-08 -> Severidad promedio
VIZ-10 -> Metricas ML
VIZ-12 -> Feed en vivo
```

**Que mostrar:**

- Capturas del dashboard con etiquetas si las agregan en la diapositiva.
- Tabla de IDs de la presentacion.

**Como sustentarlo oralmente:**

> Si el profesor pregunta por un grafico, usamos su ID para explicar de que tabla viene, que metrica calcula y por que es importante.

---

## 17. Que responder si preguntan si el feed vivo es streaming real?

**Respuesta corta:**

> Es streaming simulado en modo demo. La arquitectura esta preparada para reemplazarlo por streaming real en modo produccion.

**Sustento tecnico:**

Actual:

```text
Event Generator -> live_events.csv -> Dashboard
```

Produccion:

```text
Suricata/Wazuh/Zeek/Syslog -> Kafka -> parser -> dashboard/alertas
```

**Que mostrar:**

- Dashboard 04: feed en vivo.
- Pagina 8: modo produccion.

**Como sustentarlo oralmente:**

> No lo presentamos como SIEM completo todavia. Lo presentamos como base para evolucionar a mini-SIEM con logs parseados y cola de mensajes.

---

## 18. Que responder si dicen que faltan logs reales?

**Respuesta corta:**

> Es una limitacion reconocida. Por eso definimos tres modos: demo, laboratorio y produccion.

**Sustento tecnico:**

Modos:

```text
Modo demo        -> datos preparados y feed simulado
Modo laboratorio -> datos propios o capturas controladas
Modo produccion  -> logs reales, Kafka, parser, alertas
```

**Que mostrar:**

- Pagina 8.
- `docs/08-modos-implementacion.md`.

**Como sustentarlo oralmente:**

> La solucion esta ordenada por madurez. Primero funciona como demo, luego como laboratorio replicable, y finalmente como produccion con logs reales.

---

## 19. Que pregunta puede hacer el profesor sobre arquitectura?

**Pregunta probable:**

> Si el dashboard muestra resultados, donde esta la base de datos o almacenamiento?

**Respuesta sugerida:**

> El almacenamiento analitico es `DS1 Parquet consolidado`. No usamos una base relacional porque el proyecto esta orientado a analitica Big Data. Parquet es columnar, comprimido y eficiente para consultas por columnas. Para produccion podria agregarse PostgreSQL, Elasticsearch/OpenSearch o un data lake.

**Sustento:**

- Parquet es adecuado para analitica.
- Spark escribe el resultado consolidado.
- Streamlit consume datos ya procesados.

---

## 20. Que pregunta puede hacer sobre seguridad o SIEM?

**Pregunta probable:**

> Esto ya es un SIEM?

**Respuesta sugerida:**

> No es un SIEM completo. Es una base analitica de ciberresiliencia que puede evolucionar a mini-SIEM. Ya tiene pipeline, dashboard, ML y feed vivo; faltaria ingesta real continua, reglas de correlacion, alertas, gestion de casos y retencion historica.

**Sustento:**

Un SIEM completo necesita:

- Ingesta multi-fuente.
- Normalizacion de logs.
- Correlacion.
- Alertas.
- Busqueda historica.
- Gestion de incidentes.
- Control de accesos.

PEPA cubre la base analitica y deja clara la ruta futura.

---

## Respuestas cortas para memorizar

- **Batch:** historicos, limpieza, joins y Parquet.
- **Streaming:** feed vivo; ahora demo, luego Kafka/logs reales.
- **IDs:** trazabilidad visual de tablas, procesos y graficos.
- **Cuello de botella:** join, lectura del dashboard, feed vivo y entrenamiento ML.
- **Kafka:** futuro modo produccion, no necesario para demostrar el alcance actual.
- **T1:** tabla maestra de impacto.
- **T2:** eventos tecnicos y severidad.
- **T3:** variables operativas y ML.
- **Objetivo 2:** ML con T3.
- **Modelo:** RandomForestClassifier de Spark MLlib.
- **CyberResilience:** analizar impacto y respuesta, no solo listar ataques.
- **SIEM:** aun no es SIEM completo; es base para evolucionar a mini-SIEM.
