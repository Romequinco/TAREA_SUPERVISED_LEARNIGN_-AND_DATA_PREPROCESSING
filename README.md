# TAREA_SUPERVISED_LEARNIGN_-AND_DATA_PREPROCESSING
Tarea del bloque supervised learning del grado mIAx

## Gestión de datos pesados

Por tamaño, los archivos de datos no se versionan en GitHub:

- `*.pickle` / `*.pkl`
- `*.parquet`

Esto evita superar límites de GitHub y mantener el repositorio ligero.

### Cómo reproducir los datos
1. Ejecutar las celdas del notebook que generan los datasets (`pickle -> parquet/csv`).
2. O descargar los ficheros desde almacenamiento externo (por ejemplo, Google Drive) y colocarlos en `data/`.

> Nota: en este proyecto, los `.parquet` se generan localmente para análisis, pero no se suben al repositorio.