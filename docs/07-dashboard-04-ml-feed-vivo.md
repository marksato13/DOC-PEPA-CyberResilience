# Dashboard 04 - ML y Feed en Vivo

## Objetivo

Mostrar la parte mas avanzada del proyecto:

- Evaluacion de modelos ML entrenados con T3.
- Importancia de variables tecnicas de T3.
- Feed de eventos en vivo.
- Ruta hacia monitoreo real.


## Modelo usado

Modelo principal:

```text
RandomForestClassifier - Spark MLlib
```

Tipo de problema:

```text
Clasificacion multiclase para predecir attack_type
```

Datos usados:

```text
T3_synthesized.csv
```

Variables base:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

Validacion:

```text
CrossValidator con F1 Score y Accuracy
```

Version extendida:

```text
ml_v2.py compara RandomForest, DecisionTree, MLP y GBTClassifier
```

## Grafico: Metricas F1 / Accuracy por algoritmo

Compara:

- RandomForest.
- DecisionTree.
- MLP Neural.
- GBT binario.

Metricas:

```text
F1 Score
Accuracy
```

Tambien se muestra una linea base:

```text
Bayes = 0.20
```

Por que 0.20:

```text
5 clases de ataque -> 1 / 5 = 0.20
```

## Grafico: Importancia relativa de features

Muestra variables relevantes para el modelo. En el objetivo 2, estas variables salen de T3, por ejemplo:

- Financial_Loss_M.
- Incidents_Reported.
- t2_avg_severity.
- t3_avg_response_time.
- Country OHE.
- Protocol Used OHE.
- Attack Source OHE.
- Target Industry OHE.

## Que es OHE

OHE significa One Hot Encoding.

Sirve para convertir categorias en columnas numericas.

Ejemplo:

```text
Country = Peru, USA, Brazil
```

Se convierte en columnas:

```text
Country_Peru
Country_USA
Country_Brazil
```

## Feed en vivo

Origen:

```text
event_generator.py -> live_events.csv -> dashboard.py
```

El feed muestra:

- Eventos recientes.
- Tipo de ataque.
- Severidad.
- Pais.
- Datos comprometidos.
- Resultado.
- Total acumulado.
- Ataque mas frecuente.
- Severidad promedio.
- Success rate.

## Es real o simulado?

En esta version es simulado.

Respuesta correcta para exposicion:

> El feed actual es sintetico y corresponde al modo demo. Su objetivo es demostrar la capa de monitoreo en vivo. En modo produccion se reemplaza por logs reales de Suricata, Wazuh, Zeek, Syslog, firewall o SIEM.

## A quien va dirigido

- Equipos SOC.
- DevSecOps.
- Analistas de seguridad.
- Investigadores ML.

## Preguntas probables

Pregunta:

> Por que los modelos no tienen accuracy alta?

Respuesta:

> Porque es una clasificacion multiclase entrenada sobre T3. Las variables tecnicas de T3 pueden no separar perfectamente los ataques. El objetivo es demostrar el pipeline de ML y comparacion de modelos, no afirmar que el modelo ya esta listo para produccion.

Pregunta:

> Que se necesita para mejorar ML?

Respuesta:

> Mas datos reales, mejor ingenieria de caracteristicas, balanceo de clases y validacion con datos de produccion.
