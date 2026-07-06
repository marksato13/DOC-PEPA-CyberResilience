# DOC - PEPA CyberResilience

Documentacion de exposicion, defensa y venta tecnica de **PEPA CyberResilience**.

Repositorio principal del sistema:

```text
https://github.com/marksato13/PEPA-CyberResilience
```

Dashboard publico actual:

```text
https://immunology-doll-bios-treatments.trycloudflare.com
```

## Objetivo de esta documentacion

Este repositorio contiene guias en Markdown para explicar el proyecto durante una exposicion con 3 integrantes. Incluye:

- Guion de exposicion.
- Reparto por expositor.
- Explicacion de arquitectura, tablas y pipeline.
- Explicacion de filtros.
- Explicacion de cada dashboard.
- Preguntas probables del profesor.
- Limitaciones y mejoras futuras.
- Como vender la solucion como producto open source.

## Orden recomendado de lectura

1. [Guion general de exposicion](docs/00-guion-general.md)
2. [Reparto para 3 expositores](docs/01-reparto-3-expositores.md)
3. [Arquitectura, tablas y pipeline](docs/02-arquitectura-tablas-pipeline.md)
4. [Filtros del dashboard](docs/03-filtros.md)
5. [Dashboard 01 - Tendencia y Distribucion](docs/04-dashboard-01-tendencia-distribucion.md)
6. [Dashboard 02 - Geografia y Finanzas](docs/05-dashboard-02-geografia-finanzas.md)
7. [Dashboard 03 - Industria y Severidad](docs/06-dashboard-03-industria-severidad.md)
8. [Dashboard 04 - ML y Feed en Vivo](docs/07-dashboard-04-ml-feed-vivo.md)
9. [Modos de implementacion](docs/08-modos-implementacion.md)
10. [Limitaciones y mejoras futuras](docs/09-limitaciones-mejoras.md)
11. [Preguntas probables del profesor](docs/10-preguntas-profesor.md)
12. [Checklist final de exposicion](docs/11-checklist-exposicion.md)
13. [Presentacion de 9 paginas](docs/12-presentacion-9-paginas.md)
14. [Defensa tecnica - preguntas, dudas y sustento](docs/13-defensa-preguntas-sustento.md)
15. [Flujo detallado de arquitectura y scripts usados](docs/14-flujo-arquitectura-y-scripts.md)


## Paginas individuales para la presentacion

- [Pagina 1 - Titulo](docs/paginas/pagina-01-titulo.md)
- [Pagina 2 - Contexto del problema](docs/paginas/pagina-02-contexto-problema.md)
- [Pagina 3 - Problema, batch y streaming](docs/paginas/pagina-03-problema-batch-streaming.md)
- [Pagina 4 - Objetivos](docs/paginas/pagina-04-objetivos.md)
- [Pagina 5 - Dataset y tablas](docs/paginas/pagina-05-dataset-tablas.md)
- [Pagina 6 - Arquitectura](docs/paginas/pagina-06-arquitectura.md)
- [Pagina 7 - Demo del dashboard](docs/paginas/pagina-07-demo-dashboard.md)
- [Pagina 8 - Conclusiones y mejoras](docs/paginas/pagina-08-conclusiones-limitaciones-mejoras.md)
- [Pagina 9 - Gracias](docs/paginas/pagina-09-gracias.md)

## Resumen para vender la solucion

PEPA CyberResilience no es solo un dashboard. Es una arquitectura replicable de Big Data para ciberseguridad:

```text
CSV / Logs / Eventos
        -> Spark
        -> Join triple
        -> Parquet particionado
        -> Streamlit Dashboard
        -> ML + Feed en vivo
        -> Ruta futura a mini-SIEM
```

La solucion permite pasar de datos dispersos a analisis ejecutivo, tecnico y operativo.
