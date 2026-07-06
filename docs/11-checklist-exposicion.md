# Checklist final de exposicion

## Antes de exponer

- Verificar que el dashboard abre.
- Verificar que la URL publica responde.
- Tener capturas PDF listas.
- Tener claro que el PDF 02 tiene doble espacio en el nombre.
- Probar `./pepa.sh status`.
- Tener preparada la respuesta sobre el feed sintetico.
- Tener preparada la respuesta sobre ML.

## Comandos utiles

```bash
./pepa.sh status
./pepa.sh url
curl http://127.0.0.1:8501/_stcore/health
```

URL publica actual:

```text
https://immunology-doll-bios-treatments.trycloudflare.com
```

## Orden sugerido de demostracion

1. Mostrar README o arquitectura.
2. Abrir dashboard publico.
3. Explicar KPIs.
4. Mostrar filtros.
5. Dashboard 01.
6. Dashboard 02.
7. Dashboard 03.
8. Dashboard 04.
9. Explicar modos demo, laboratorio y produccion.
10. Cerrar con limitaciones y mejoras futuras.

## Frases clave

```text
PEPA no es solo un dashboard.
```

```text
Es una arquitectura replicable de Big Data para ciberseguridad.
```

```text
El modo demo usa eventos sinteticos, pero el modo produccion esta pensado para logs reales.
```

```text
Spark procesa, Parquet almacena eficientemente y Streamlit visualiza.
```

## Errores que deben evitar

- Decir que el feed sintetico es real.
- Decir que ML ya esta listo para produccion.
- Explicar solo graficos y no la arquitectura.
- Omitir limitaciones.
- No explicar de donde salen las tablas.
