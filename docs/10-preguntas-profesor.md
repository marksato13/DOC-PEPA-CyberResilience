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

## Por que los modelos ML no son perfectos?

Porque la clasificacion multiclase de ataques requiere datos reales, variables fuertes y balanceo de clases. El objetivo actual es demostrar la arquitectura ML, no vender un modelo final de produccion.

## A quien va dirigido?

- Estudiantes.
- Docentes.
- Investigadores.
- Analistas SOC.
- Equipos de ciberseguridad.
- Personas que necesitan un laboratorio Big Data replicable.

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
