# Pagina 3 - Problema que queremos resolver

## Que poner en la diapositiva

**Titulo:**

```text
Problema central: datos dispersos, poca correlacion y respuesta tardia
```

**Problema:**

```text
Como integrar fuentes heterogeneas de ciberseguridad para analizar ataques, medir impacto y preparar monitoreo en vivo?
```

**Dificultades:**

```text
T1, T2 y T3 tienen esquemas diferentes.
T2 y T3 tienen mas granularidad que T1.
Un join directo puede duplicar registros.
El dashboard necesita datos consolidados.
El monitoreo requiere eventos recientes o en tiempo real.
```

## Batch, streaming o ambos

**Batch:**

```text
CSV historicos -> Spark -> Join triple -> Parquet
```

Sirve para construir la base historica y analitica.

**Streaming:**

```text
Eventos recientes -> live_events.csv / Kafka futuro -> Dashboard
```

Sirve para monitoreo en vivo.

**Respuesta final:**

```text
Necesitamos ambos: batch para datos historicos confiables y streaming para eventos recientes.
```

## Imagen o icono sugerido

Diagrama en dos columnas:

```text
BATCH                         STREAMING
CSV -> Spark -> Parquet       Logs/Eventos -> Feed -> Dashboard
```

Iconos:

- Batch: archivo CSV, engranaje, base de datos.
- Streaming: rayo, señal en vivo, cola de mensajes/Kafka.

## Que explicar oralmente

> En nuestro proyecto, el batch ya esta implementado con Spark y Parquet. La parte streaming se representa con un generador de eventos en vivo que alimenta el dashboard. En produccion, esa capa se puede reemplazar por Kafka, Suricata, Wazuh o Syslog.

## Pregunta probable del profesor

**Donde aplicaron streaming?**

> En la capa live: `event_generator.py` genera eventos, los escribe en `live_events.csv` y el dashboard los refresca automaticamente. En modo produccion se reemplaza por logs reales o Kafka.
