# Limitaciones y mejoras futuras

## Limitaciones actuales

### 1. Feed sintetico

El feed en vivo actual no viene de un IDS real. Es generado por `event_generator.py`.

Como explicarlo:

> Es una limitacion controlada. Sirve para demostrar la arquitectura en modo demo y puede reemplazarse por logs reales.

### 2. ML demostrativo

Los modelos ML muestran comparacion de algoritmos, pero requieren mas datos reales para produccion.

### 3. Spark local

Actualmente Spark corre en modo local:

```text
local[*]
```

No es aun un cluster distribuido real.

### 4. Dependencia de columnas

El pipeline espera nombres de columnas especificos. Si el usuario trae otros datos, debe adaptar el mapeo.

### 5. URL publica temporal

El demo publico usa Cloudflare Quick Tunnel sin dominio. La URL puede cambiar si se reinicia el tunel.

## Mejoras futuras

- Integrar Suricata en tiempo real.
- Integrar Wazuh.
- Crear parsers para Zeek, Syslog y firewalls.
- Usar Kafka para streaming.
- Guardar historico en PostgreSQL u OpenSearch.
- Generar Parquet diario para historico.
- Dockerizar el proyecto.
- Desplegar con dominio y HTTPS.
- Mejorar modelos ML con datos reales.
- Agregar alertas automaticas por severidad.
- Crear roles de usuario.
- Ejecutar Spark en cluster.

## Como presentar las limitaciones

No ocultarlas. Decir:

> Estas limitaciones no invalidan el proyecto; marcan claramente la ruta de evolucion hacia produccion.
