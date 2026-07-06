# Pagina 4 - Objetivos

## Que poner en la diapositiva

**Titulo:**

```text
Objetivos del proyecto
```

**Objetivo 1:**

```text
OBJ-01 Pipeline distribuido
Integrar T1, T2 y T3 con Apache Spark, evitando duplicados y generando Parquet particionado.
```

**Objetivo 2:**

```text
OBJ-02 Clasificacion ML con T3 + analitica
Entrenar modelos con variables tecnicas de T3 para clasificar tipos de ataque; el dashboard complementa con KPIs, impacto y feed en vivo.
```

Usar poco texto. Que se vea como dos tarjetas grandes.

## Imagen o icono sugerido

Para OBJ-01:

- Icono Spark.
- Icono pipeline.
- Icono base de datos/Parquet.

Para OBJ-02:

- Icono inteligencia artificial.
- Icono tabla T3 / datos tecnicos.
- Icono dashboard como visualizacion complementaria.

## Que explicar oralmente

> El primer objetivo es de ingenieria de datos: limpiar, transformar e integrar T1, T2 y T3. El segundo objetivo, en la parte ML, usa T3 porque contiene variables tecnicas numericas y categoricas apropiadas para entrenar modelos de clasificacion de ataques. El dashboard muestra ese resultado junto con el analisis integrado.

## Mensaje clave

```text
OBJ-01 integra las fuentes. OBJ-02 usa T3 para ML y el dashboard para comunicar los resultados.
```


## Modelo usado en el Objetivo 2

El modelo principal usado para el Objetivo 2 es:

```text
RandomForestClassifier de Spark MLlib
```

Se usa como clasificador multiclase para predecir `attack_type` a partir de variables tecnicas de T3:

```text
data_compromised_GB
attack_duration_min
attack_severity
response_time_min
```

La evaluacion se realiza con:

```text
CrossValidator
F1 Score
Accuracy
```

En la version extendida `ml_v2.py` tambien se comparan:

```text
RandomForest
DecisionTree
Multilayer Perceptron (MLP)
GBTClassifier
```

Frase para exposicion:

> Para el Objetivo 2 usamos Spark MLlib con Random Forest como modelo principal de clasificacion multiclase. El modelo se entrena con variables tecnicas de T3 para predecir el tipo de ataque. Luego comparamos metricas como F1 Score y Accuracy, y en la version extendida evaluamos tambien Decision Tree, MLP y GBT.

## Nota para evitar observacion

No decir que el resultado final tiene 123,000 filas consolidadas. Lo correcto es:

```text
123,000 registros brutos procesados
3,000 incidentes T1 conservados y enriquecidos en el Parquet final
```

El Objetivo 2 usa **variables tecnicas de T3** para ML. Si se menciona el modelo base, decir **4 variables numericas base**.
