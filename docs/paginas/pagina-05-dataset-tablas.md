# Pagina 5 - Dataset utilizado

## Que poner en la diapositiva

**Titulo:**

```text
Dataset utilizado: tres fuentes integradas
```

**Tabla resumen:**

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

## Atributos que poner

### T1 - Tabla maestra

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

### T2 - Eventos tecnicos

```text
Event_ID
Timestamp -> Year
Source_IP
Destination_IP
Attack_Type
Attack_Severity -> t2_avg_severity
Data_Exfiltrated -> t2_exfiltrations
Threat_Intel
Response_Action
```

Metricas generadas:

```text
t2_total_eventos
t2_avg_severity
t2_exfiltrations
t2_unique_ips
```

### T3 - Metricas operativas / ML

```text
attack_type -> Attack_Type
attacker_ip -> t3_total_sesiones
data_compromised_GB -> t3_avg_data_GB
attack_duration_min -> t3_avg_duration_min
attack_severity -> t3_avg_severity
response_time_min -> t3_avg_response_min
mitigation_method -> t3_mitigations_count
```

## Imagen o icono sugerido

Tres tarjetas horizontales:

```text
[T1] Incidentes globales
[T2] Eventos tecnicos
[T3] Metricas operativas
```

Iconos:

- T1: globo + alerta.
- T2: red/IP.
- T3: engranaje + metrica.

## Que explicar oralmente

> T1 es la tabla maestra porque contiene los incidentes principales. T2 y T3 tienen mayor granularidad, por eso se agregan antes del join. Asi evitamos duplicados y conservamos los 3,000 incidentes principales de T1 enriquecidos con metricas tecnicas.

## Pregunta probable

**Por que no unieron todo directamente?**

> Porque T2 y T3 tienen mayor granularidad. Un join directo multiplicaria registros y alteraria la tabla maestra. Por eso se agregan primero por llaves y luego se aplica LEFT JOIN para conservar T1.
