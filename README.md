# TAREA_SUPERVISED_LEARNIGN_AND_DATA_PREPROCESSING
> **Advertencia importante:** el archivo `navs.pickle` no está incluido en este repositorio por tamaño.  
> Sin ese archivo, **no se puede re-ejecutar el notebook completo desde cero**.

El notebook `TAREA.ipynb` está **debidamente ejecutado y documentado** con resultados finales.

## Referencias usadas
- `MIAX ML - 01 Data Preprocessing.ipynb`
- `MIAX ML - 03 Linear Regression.ipynb`

## Datos principales
- `navs.pickle` (no incluido en repo)
- Factores Fama-French Asia ex-Japan (sí incluidos/procesados)

## Nota de reproducibilidad
Para reproducir todo el pipeline:
1. Añadir `navs.pickle` en la ruta esperada del proyecto.
2. Ejecutar `TAREA.ipynb` de inicio a fin.

## Gestión de datos pesados

Por tamaño, los archivos de datos no se versionan en GitHub:

- `*.pickle` / `*.pkl`
- `*.parquet`

> Nota: en este proyecto, los `.parquet` se generan localmente para análisis, pero no se suben al repositorio.