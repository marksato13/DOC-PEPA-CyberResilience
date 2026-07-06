# Pagina 8 - Conclusiones, limitaciones, mejoras, Git y modos

## Que poner en la diapositiva

**Titulo:**

```text
Conclusiones y evolucion del proyecto
```

## Conclusiones

```text
PEPA integra tres fuentes heterogeneas.
Spark permite procesamiento batch reproducible.
Parquet mejora el almacenamiento analitico.
Streamlit permite visualizacion interactiva.
El feed vivo demuestra una ruta hacia monitoreo real.
```

## Limitaciones

```text
El feed actual es sintetico.
Spark corre en modo local.
live_events.csv puede ser cuello de botella.
ML es demostrativo y requiere mas datos reales.
El tunel publico sin dominio puede cambiar de URL.
```

## Mejoras futuras

```text
Integrar Kafka para streaming real.
Integrar Suricata, Wazuh, Zeek o Syslog.
Usar PostgreSQL/OpenSearch para historico.
Dockerizar la solucion.
Agregar alertas por severidad.
Desplegar con dominio y HTTPS.
Mejorar ML con datos reales.
```

## Modos

| Modo | Uso |
| --- | --- |
| Demo | Eventos sinteticos para presentar |
| Laboratorio | Datasets propios para pruebas |
| Produccion | Logs reales con parsers/Kafka |

## GitHub

Sistema:

```text
https://github.com/marksato13/PEPA-CyberResilience
```

Documentacion:

```text
https://github.com/marksato13/DOC-PEPA-CyberResilience
```

## Que explicar oralmente

> La solucion actual ya funciona como demo y laboratorio. El siguiente paso es produccion: reemplazar el generador sintetico por logs reales, usar Kafka para streaming y almacenar historico en una base mas adecuada.

## Mensaje clave

```text
Las limitaciones no invalidan el proyecto; definen su ruta de evolucion.
```
