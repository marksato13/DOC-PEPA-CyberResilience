# Modos de implementacion

PEPA se presenta con tres modos.

## Modo 1 - Demo

Fuente:

```text
event_generator.py
```

Uso:

```bash
./pepa.sh run --mode demo
```

Sirve para:

- Presentacion rapida.
- Clases.
- Validacion visual.
- Demostracion sin datos reales.

## Modo 2 - Laboratorio

Fuente:

```text
CSV / JSON historicos del usuario
```

Uso:

```bash
cp mis_datos/*.csv tablas/
./pepa.sh pipeline
./pepa.sh run --mode lab
```

Sirve para:

- Tesis.
- Investigacion.
- Practicas academicas.
- Comparacion de datasets.
- Adaptacion a datos propios.

## Modo 3 - Produccion

Fuente:

```text
Logs reales normalizados
```

Posibles origenes:

- Suricata.
- Wazuh.
- Zeek.
- Syslog.
- pfSense.
- Firewalls.
- SIEM.
- Kafka.

Uso conceptual:

```bash
python3 scripts/parsers/normalizer_template.py   --input /var/log/suricata/eve.json   --output ~/bigdata/output/live_events.csv   --source suricata   --follow

./pepa.sh run --mode production
```

## Importancia del modo 3

El modo 3 es el trabajo futuro mas importante.

Frase para exposicion:

> El modo produccion convierte PEPA de un laboratorio analitico en una solucion conectada a fuentes reales de monitoreo.

## Diferencia entre los modos

```text
Demo        -> datos sinteticos
Laboratorio -> datos historicos propios
Produccion  -> logs reales en tiempo real
```
