# Dashboard 04 - ML y Feed en Vivo

## Proposito del dashboard

Este dashboard representa la parte mas avanzada del producto. Tiene dos componentes:

```text
1. Objetivo 2: Machine Learning con T3
2. Feed live demo: eventos en vivo simulados
```

La parte principal para defender como Objetivo 2 es ML. El feed live complementa la ruta hacia monitoreo en tiempo real.

## A quien le beneficia

| Usuario | Beneficio |
| --- | --- |
| Analistas SOC | Ven una aproximacion a monitoreo live y clasificacion de ataques |
| Equipo de ciencia de datos | Evalua modelos y variables predictivas |
| DevSecOps | Entiende como se podria integrar con logs reales |
| Investigadores | Compara algoritmos y limitaciones del dataset |
| Docente/evaluador | Verifica que existe un componente ML y una ruta a produccion |

## Como se usaria

En exposicion, se debe presentar asi:

> Este dashboard corresponde al Objetivo 2. Usamos T3 para entrenar modelos de clasificacion con Spark MLlib. Ademas, incluimos un feed live demo para mostrar como la arquitectura podria evolucionar a monitoreo en tiempo real.

En un caso real, se podria usar para:

- Comparar modelos de clasificacion.
- Ver si las variables tecnicas ayudan a distinguir ataques.
- Monitorear eventos recientes.
- Preparar integracion futura con SIEM, Kafka o logs reales.

---

# Parte A - Objetivo 2: Machine Learning

## Que se quiere resolver

El Objetivo 2 busca construir un modelo predictivo de clasificacion de ataques a escala.

Problema ML principal:

```text
Predecir attack_type usando variables tecnicas de T3
```

Tipo de problema:

```text
Clasificacion multiclase
```

Clases esperadas:

```text
DDoS
Malware
Phishing
Ransomware
SQL Injection
```

## Como se llego a mostrar esto desde la data

### Entrada ML

```text
T3_synthesized.csv
```

T3 se usa porque contiene variables tecnicas adecuadas para clasificacion:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
target_system
outcome
security_tools_used
user_role
industry
mitigation_method
```

### Scripts usados

```text
pipeline_linux.py -> ML v1 base
ml_v2.py -> ML v2 mejorado
```

### Flujo ML v1

```text
T3 sampled
 -> StringIndexer
 -> VectorAssembler
 -> StandardScaler
 -> RandomForestClassifier
 -> CrossValidator
 -> F1 / Accuracy
```

Variables base v1:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

### Flujo ML v2

```text
T3 completo filtrado
 -> variables numericas
 -> variables categoricas con OneHotEncoding
 -> modelos RF / DT / MLP
 -> metricas F1 y Accuracy
```

Variables numericas:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

Variables categoricas con OHE:

```text
target_system
outcome
security_tools_used
user_role
industry
mitigation_method
```

### Tarea adicional en ML v2

Tambien se entrena una tarea binaria:

```text
Predecir outcome Success/Failure con GBTClassifier
```

Metricas:

```text
AUC-ROC
Accuracy
F1
```

## Modelos usados

Modelo principal:

```text
RandomForestClassifier - Spark MLlib
```

Modelos comparados:

```text
RandomForest
DecisionTree
Multilayer Perceptron
GBTClassifier
```

## Grafico: Metricas F1 / Accuracy por algoritmo

Este grafico compara el desempeno de los modelos.

Metricas:

```text
F1 Score
Accuracy
AUC-ROC para GBT binario
```

Como interpretarlo:

> Si F1 y Accuracy estan cerca de 0.20 en clasificacion de 5 clases, el modelo esta cerca de una referencia aleatoria. Eso no invalida el pipeline; muestra que el dataset sintetico no tiene senal predictiva fuerte.

Linea base:

```text
5 clases -> 1 / 5 = 0.20
```

Como explicarlo:

> La parte importante del Objetivo 2 no es afirmar que el modelo ya es perfecto, sino demostrar el flujo ML completo: preparacion de datos, encoding, entrenamiento, validacion y comparacion de modelos.

## Grafico: Importancia relativa de features

En el Objetivo 2, las variables deben interpretarse desde T3.

Variables correctas para explicar:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
target_system_ohe
outcome_ohe
security_tools_used_ohe
user_role_ohe
industry_ohe
mitigation_method_ohe
```

Importante:

> Si en alguna visualizacion aparecen nombres de ejemplo que mezclan T1/T2, en la defensa oral se debe aclarar que el Objetivo 2 se sustenta en T3. Las variables tecnicas reales usadas por los scripts ML salen de T3.

## Que es OHE

OHE significa One Hot Encoding.

Sirve para convertir categorias en columnas numericas.

Ejemplo:

```text
industry = Finance, Healthcare, Education
```

Se convierte en:

```text
industry_Finance
industry_Healthcare
industry_Education
```

Esto permite que algoritmos de ML trabajen con variables categoricas.

## Relacion con el Objetivo 2

Este dashboard es la evidencia visual del Objetivo 2:

```text
T3 -> Spark MLlib -> modelos -> metricas -> dashboard
```

Debe explicarse asi:

> El Objetivo 2 no depende del join completo. Usa T3 porque contiene variables tecnicas mas apropiadas para clasificacion. El dashboard muestra los resultados comparativos de los modelos.

---

# Parte B - Feed en vivo

## Proposito

El feed live muestra una ruta hacia monitoreo en tiempo real.

Flujo implementado:

```text
event_generator.py -> live_events.csv -> dashboard.py
```

## Como se llego a mostrar esto desde la data

El feed no viene de T1/T2/T3 historicos. Viene de un generador:

```text
event_generator.py
```

Campos generados:

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

El dashboard lee:

```text
live_events.csv
```

Y refresca la vista cada pocos segundos.

## Que muestra

- Eventos recientes.
- Tipo de ataque.
- Severidad.
- Pais.
- Industria.
- Datos comprometidos.
- Resultado.
- Total acumulado.
- Ataque mas frecuente.
- Severidad promedio.
- Success rate.

## Como interpretarlo

> El feed permite simular que el sistema esta recibiendo eventos recientes. En esta version es modo demo, pero la estructura permite reemplazarlo por logs reales.

## Es real o simulado?

Respuesta correcta:

> Es live demo o streaming simulado. No es Kafka real todavia. En produccion se reemplazaria por logs reales de Suricata, Wazuh, Zeek, Syslog, firewall o SIEM.

## Relacion con produccion

Ruta futura:

```text
Logs reales -> Kafka -> Parser -> Dashboard / Alertas
```

Esto permitiria evolucionar el producto hacia un mini-SIEM.

---

# Como defender este dashboard

## Que parte es Objetivo 2?

La parte ML:

```text
T3 -> Spark MLlib -> modelos -> metricas
```

## Que parte es complemento live?

El feed:

```text
event_generator.py -> live_events.csv -> dashboard.py
```

## Como se relaciona con visualizacion?

El dashboard une dos salidas:

```text
Metricas ML -> panel ML
live_events.csv -> feed en vivo
```

No entrena en vivo; visualiza resultados y eventos.

## Preguntas probables

### Que modelo usaron para el Objetivo 2?

Respuesta:

> El modelo principal es RandomForestClassifier de Spark MLlib. Tambien comparamos DecisionTree, MLP y GBT en la version mejorada.

### Que datos usa el Objetivo 2?

Respuesta:

> Usa T3, porque T3 contiene variables tecnicas como datos comprometidos, duracion del ataque, severidad y tiempo de respuesta.

### Por que el modelo no tiene accuracy alta?

Respuesta:

> Porque es una clasificacion multiclase con datos sinteticos y las variables no separan perfectamente las clases. El objetivo es demostrar el pipeline ML completo, no afirmar que ya tenemos un detector productivo.

### Que significa F1 cercano a 0.20?

Respuesta:

> Como son 5 clases, una referencia aleatoria esta cerca de 1/5, es decir 0.20. Si el modelo esta cerca de eso, significa que necesita mejores datos o mejores features.

### Para que sirve entonces el ML?

Respuesta:

> Sirve como prueba de arquitectura ML: carga de datos, transformacion, encoding, entrenamiento, validacion y comparacion de modelos. Con datos reales podria mejorar y convertirse en clasificador operativo.

### El feed vivo es streaming real?

Respuesta:

> Es streaming simulado en modo demo. El generador escribe eventos constantemente y el dashboard los refresca. En produccion se reemplaza por Kafka o logs reales.

### Esto ya es un SIEM?

Respuesta:

> No es un SIEM completo. Es una base para evolucionar a mini-SIEM porque ya tiene dashboard, feed live, ML y arquitectura preparada para logs reales.

### Que se necesita para mejorar ML?

Respuesta:

> Datos reales etiquetados, balanceo de clases, mejores features, validacion temporal, control de falsos positivos y despliegue de modelos versionados.

## Frase para exposicion

> Este dashboard corresponde al Objetivo 2 porque muestra el flujo de machine learning con T3: preparacion de variables, entrenamiento, comparacion de modelos y metricas. El feed en vivo complementa la vision mostrando como el producto puede evolucionar hacia monitoreo real con logs y Kafka.


---

# Guion listo para exponer el Dashboard 04

Usar este guion cuando se muestre el Dashboard 04 en la demo. La idea es explicar de arriba hacia abajo y separar claramente el Objetivo 2 del feed live.

## 1. Apertura del dashboard

Texto sugerido:

> Este es el Dashboard 04: ML y Feed en Vivo. Es el dashboard mas avanzado del proyecto porque conecta dos partes: primero, el Objetivo 2, que es machine learning usando T3; y segundo, una capa live demo que simula monitoreo en vivo.

Idea clave:

```text
Objetivo 2 = ML con T3
Feed vivo = complemento de monitoreo live demo
```

No decir que el feed vivo es el Objetivo 2. El Objetivo 2 es la parte de machine learning.

## 2. Proposito del dashboard

Texto sugerido:

> Este dashboard demuestra que PEPA CyberResilience no solo integra datos historicos. Tambien permite evaluar modelos de clasificacion y mostrar una ruta hacia monitoreo en tiempo real. La parte principal es ML, y el feed en vivo complementa la vision de produccion.

Que se debe recalcar:

- El dashboard muestra resultados, no entrena en vivo.
- ML viene de T3.
- El feed viene de `event_generator.py`.
- Kafka/logs reales quedan como mejora futura.

## 3. A quien beneficia

Texto sugerido:

> Este dashboard beneficia a diferentes perfiles. A un analista SOC le sirve para ver eventos recientes y severidad. A un equipo de ciencia de datos le sirve para comparar modelos. A DevSecOps le muestra como podria integrarse con logs reales. Y para la evaluacion academica, demuestra que el proyecto tiene una capa predictiva y una ruta hacia produccion.

Resumen rapido:

```text
SOC -> monitoreo
Data Science -> modelos
DevSecOps -> integracion futura
Docente -> evidencia del Objetivo 2
```

## 4. Como se usaria

Texto sugerido:

> En una organizacion real, este dashboard podria usarse para comparar modelos de clasificacion, revisar si las variables tecnicas ayudan a distinguir ataques y observar eventos recientes. En nuestro proyecto, lo usamos para demostrar el Objetivo 2 y la ruta hacia monitoreo live.

Ejemplo practico:

> Si el equipo detecta que Ransomware tiene mayor severidad o que el modelo falla en ciertas clases, podria ajustar datos, features o reglas de monitoreo.

## 5. Parte A - Objetivo 2: Machine Learning

Texto sugerido:

> Ahora explicamos la parte principal: el Objetivo 2. Aqui buscamos clasificar tipos de ataque usando variables tecnicas de T3. Es un problema de clasificacion multiclase porque el modelo intenta predecir si un evento corresponde a DDoS, Malware, Phishing, Ransomware o SQL Injection.

Frase corta para defender:

> El Objetivo 2 usa T3 porque T3 contiene variables tecnicas mas apropiadas para ML.

## 6. Datos usados para ML

Texto sugerido:

> Para machine learning usamos `T3_synthesized.csv`. No usamos el dataset consolidado completo para entrenar el modelo principal, porque T3 tiene variables tecnicas como datos comprometidos, duracion del ataque, severidad y tiempo de respuesta.

Variables a mencionar:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

Si hay tiempo, agregar:

```text
target_system
industry
mitigation_method
security_tools_used
```

Respuesta corta si preguntan por T1/T2:

> T1 y T2 son esenciales para el Objetivo 1 y los dashboards historicos. Para el Objetivo 2 usamos T3 porque tiene mejores variables tecnicas para clasificacion.

## 7. Scripts usados

Texto sugerido:

> Esta parte se construyo con dos scripts. `pipeline_linux.py` contiene una version base con RandomForest y cuatro variables numericas. Luego `ml_v2.py` mejora el enfoque comparando RandomForest, DecisionTree, MLP y GBT con variables numericas y categoricas.

Relacion script-funcion:

```text
pipeline_linux.py -> ML v1 base
ml_v2.py -> ML v2 comparativo
```

## 8. Flujo de ML

Texto sugerido:

> El flujo ML empieza con T3. Primero se prepara la etiqueta `attack_type`, luego se ensamblan las variables, se escalan, se entrena el modelo y se evalua con metricas como F1 y Accuracy.

Flujo simple:

```text
T3 -> StringIndexer -> VectorAssembler -> StandardScaler -> Modelo -> Metricas
```

Para la version mejorada:

```text
T3 -> numericas + categoricas OHE -> RF / DT / MLP / GBT -> metricas
```

## 9. Modelos usados

Texto sugerido:

> El modelo principal es RandomForestClassifier de Spark MLlib. Lo usamos porque es estable para clasificacion, permite comparar desempeno y puede dar importancia de variables. Ademas, en la version mejorada comparamos DecisionTree, MLP y GBT.

Respuesta corta:

> Modelo principal: RandomForestClassifier de Spark MLlib.

## 10. Grafico F1 / Accuracy

Texto sugerido:

> Este grafico compara el desempeno de los modelos. F1 y Accuracy nos dicen que tan bien clasifica cada algoritmo. Como trabajamos con cinco clases, una referencia aleatoria esta cerca de 0.20, porque 1 dividido entre 5 es 0.20.

Como interpretar resultados bajos:

> Si los valores estan cerca de 0.20, significa que las variables del dataset sintetico no separan muy bien las clases. Eso no invalida el proyecto, porque aqui estamos demostrando el pipeline ML completo, no vendiendo un detector final de produccion.

Frase de defensa:

> El valor del Objetivo 2 esta en demostrar preparacion de datos, entrenamiento, validacion y comparacion de modelos con Spark MLlib.

## 11. Importancia de features

Texto sugerido:

> Esta seccion busca mostrar que variables influyen mas en el modelo. Para explicarlo correctamente, debemos relacionarlo con T3: datos comprometidos, duracion, severidad, tiempo de respuesta y variables categoricas como industria o metodo de mitigacion.

Variables correctas a mencionar:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
target_system_ohe
industry_ohe
mitigation_method_ohe
security_tools_used_ohe
```

Aclaracion importante:

> Si alguna etiqueta visual mezcla nombres de ejemplo, la defensa oral debe ser clara: el Objetivo 2 se sustenta en variables tecnicas de T3.

## 12. Que es OHE

Texto sugerido:

> OHE significa One Hot Encoding. Sirve para convertir texto en columnas numericas. Por ejemplo, si la industria es Finance, Healthcare o Education, el modelo no entiende esas palabras directamente; por eso se convierten en columnas binarias.

Ejemplo corto:

```text
industry = Finance -> industry_Finance = 1
industry = Healthcare -> industry_Healthcare = 1
```

## 13. Parte B - Feed en vivo

Texto sugerido:

> Ahora pasamos al feed en vivo. Esta parte no viene de T1, T2 ni T3 historicos. Viene de `event_generator.py`, que genera eventos simulados y los escribe en `live_events.csv`. El dashboard lee ese archivo cada pocos segundos.

Flujo:

```text
event_generator.py -> live_events.csv -> dashboard.py
```

Que muestra:

- Ultimos eventos.
- Tipo de ataque.
- Severidad.
- Pais.
- Industria.
- Datos comprometidos.
- Resultado.
- Success rate.

## 14. Es real o simulado

Texto sugerido:

> Es live demo o streaming simulado. No es Kafka real todavia. Lo usamos para demostrar como se veria una capa de monitoreo en vivo sin depender de infraestructura externa.

Respuesta de defensa:

> En produccion, `event_generator.py` se reemplazaria por logs reales de Suricata, Wazuh, Zeek, Syslog, firewall o SIEM, y la cola podria ser Kafka.

## 15. Relacion con produccion

Texto sugerido:

> La ruta futura seria conectar logs reales a Kafka, luego pasar por un parser para normalizar los eventos y finalmente alimentar el dashboard o generar alertas. Eso permitiria evolucionar PEPA hacia un mini-SIEM.

Ruta:

```text
Logs reales -> Kafka -> Parser -> Dashboard / Alertas
```

## 16. Cierre del dashboard

Texto sugerido:

> En resumen, este dashboard demuestra el Objetivo 2 porque muestra el flujo de machine learning con T3: preparacion de variables, entrenamiento, comparacion de modelos y metricas. El feed en vivo complementa la vision mostrando como el producto puede evolucionar hacia monitoreo real con logs y Kafka.

Cierre corto:

> ML demuestra capacidad predictiva; el feed live demuestra ruta operativa hacia monitoreo.

---

# Preguntas rapidas para responder durante la demo

## Que parte es realmente el Objetivo 2?

> La parte de machine learning: T3 -> Spark MLlib -> modelos -> metricas.

## El feed en vivo tambien es Objetivo 2?

> No. El feed en vivo es complemento de monitoreo live demo. El Objetivo 2 es ML.

## Que modelo usaron?

> RandomForestClassifier de Spark MLlib como modelo principal. Tambien se compararon DecisionTree, MLP y GBT.

## Que datos usaron para ML?

> T3, porque contiene variables tecnicas como datos comprometidos, duracion, severidad y tiempo de respuesta.

## Por que los resultados no son altos?

> Porque los datos son sinteticos y no separan perfectamente las clases. El objetivo fue demostrar el pipeline ML completo.

## El dashboard entrena modelos en vivo?

> No. El entrenamiento se hace en `pipeline_linux.py` y `ml_v2.py`. El dashboard muestra metricas y resultados.

## El feed vivo es Kafka?

> No. Es `event_generator.py` escribiendo en `live_events.csv`. Kafka queda para modo produccion.

## Como se llevaria a produccion?

> Reemplazando el generador por logs reales, agregando Kafka, parser, reglas de alerta y almacenamiento historico.
