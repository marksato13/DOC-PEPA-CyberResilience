# Dashboard 02 - Geografia y Finanzas

## Objetivo

Analizar el impacto territorial y economico de los ataques.

Este dashboard responde:

- Que paises concentran mas incidentes?
- Que ataques son mas costosos en promedio?
- Que paises acumulan mayor perdida total?

## Grafico: Top 10 paises por incidentes

Tipo: barras horizontales.

Origen:

```python
dff.groupby("Country").size()
```

Interpretacion:

> Permite identificar concentracion geografica de incidentes.

## Grafico: Perdida financiera promedio por tipo

Tipo: barras horizontales.

Origen:

```python
dff.groupby("Attack_Type")["Financial_Loss_M"].mean()
```

Interpretacion:

> No mide cuantos ataques hay, sino cuanto cuesta en promedio cada tipo de ataque.

## Grafico: Perdida total por pais

Tipo: barras.

Origen:

```python
dff.groupby("Country")["Financial_Loss_M"].sum()
```

Interpretacion:

> Permite priorizar paises o regiones con mayor impacto economico.

## A quien va dirigido

- Gestion de riesgos.
- Equipos financieros.
- Direccion de seguridad.
- Analistas que priorizan recursos.

## Importancia

Este dashboard conecta ciberseguridad con impacto economico.

Frase para exposicion:

> No basta con contar incidentes; tambien debemos saber donde ocurren y cuanto cuestan.

## Preguntas probables

Pregunta:

> Por que un pais con menos incidentes puede aparecer como critico?

Respuesta:

> Porque puede tener menos casos, pero con perdidas financieras mas altas.

Pregunta:

> Que decision se podria tomar con este dashboard?

Respuesta:

> Priorizar controles, monitoreo o inversion en los paises y ataques con mayor impacto.
