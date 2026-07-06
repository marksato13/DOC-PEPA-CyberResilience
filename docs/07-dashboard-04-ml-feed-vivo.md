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
