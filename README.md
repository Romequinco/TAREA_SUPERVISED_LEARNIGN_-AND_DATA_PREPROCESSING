# TAREA - Data Preprocessing and Supervised Learning
---

## Contexto y enfoque

El dataset principal es `navs.pickle`, complementado con factores Fama-French que se descargan durante el flujo de trabajo.

La lógica del proyecto separa claramente:

1. **Estructura de datos** (qué variables y objetos existen)
2. **Alineación temporal** (consistencia cronológica)
3. **Persistencia** (versionado y reutilización del dato)

Esta separación mejora la trazabilidad y evita mezclar limpieza avanzada con decisiones de alineación temporal.

---

## Objetivo y tesis financiera

### Hipótesis

Asia tendrá mejor comportamiento relativo frente a otras regiones.

### Objetivo principal

Construir un fondo de fondos con sesgo Asia, con una cartera robusta, interpretable y metodológicamente consistente.

### Restricciones de inversión

- Asignación objetivo: **70% Asia / 30% diversificación**
- Límite por fondo: **5%**
- Aplicación de filtros mínimos de calidad

---

## Datos y reproducibilidad

> **Importante:** por limitaciones de tamaño, el archivo `navs.pickle` no está incluido en el repositorio ni embebido en el notebook.

Sin este archivo, no es posible re-ejecutar el pipeline completo desde cero.  
Para reproducir localmente, debes añadir `navs.pickle` en la ruta esperada por el notebook.

---

## Flujo general del trabajo

1. Carga de NAVs desde `navs.pickle`
2. Descarga e integración de factores Fama-French
3. Estandarización de fechas y orden cronológico
4. Unificación del universo en formato diario largo
5. Controles de calidad y tratamiento de faltantes
6. Modelado supervisado (regresión y variantes)
7. Evaluación de resultados y análisis financiero

---

## Estructura sugerida del repositorio

.
├── FONDO.ipynb
├── README.md
├── data/
│   └── daily_universe/
└── DOCS_CLASE/
    └── MachineLearning/
        └── dataset/
            └── navs.pickle   # (no versionado)
