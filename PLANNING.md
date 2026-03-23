# Planning - Workshop 1 (`MIAX ML - 03_05 Workshop Part 1 of 2`)

## Contexto y enfoque

El notebook `MIAX ML - 03_05 Workshop Part 1 of 2` contiene principalmente el contexto, objetivos y tesis financiera, pero no un desarrollo técnico completo.  
Por tanto, el enfoque correcto es construir el flujo de trabajo aplicando:

- técnicas de `MIAX ML - 01 Data Preprocessing.ipynb`
- técnicas de `MIAX ML - 03 Linear Regression.ipynb`
- datos base: `navs.pickle` + `Asia_Pacific_ex_Japan_3_Factors_Daily.csv`

El objetivo es priorizar la **explicación y justificación de decisiones** sobre la complejidad del código.

---

## Plan paso a paso
  AÑADIR FILTRADO TEMPORAL PARA SELECIONAR FONDOS

### 1) Estructura del notebook

Organizar el notebook en secciones:

1. Objetivo y tesis financiera  
2. Carga de datos  
3. Exploración de calidad de datos (EDA)  
4. Limpieza y preprocesado  
5. Feature engineering  
6. Modelado supervisado  
7. Evaluación y visualización  
8. Conclusiones y limitaciones  

**Decisión clave:** mantenerlo simple, legible y argumentado.

HACER AL FINAL

---
**Pendiente transversal de documentación (fase 2):**
- [ ] Revisar y homogeneizar todos los markdown de esta fase (2, 2.1, 2.2, 2.3, 2.4, 2.5) para que reflejen exactamente lo implementado y las decisiones metodológicas.
---

### 6) Split temporal (sin leakage)

- Separar train/test en orden temporal (ej. 80/20).
- No usar `shuffle`.
- Ajustar `StandardScaler` solo con train y transformar test.

**Qué justificar:** en series temporales financieras no se debe mezclar pasado y futuro.

---

### 7) Modelado supervisado (simple)

Aplicar técnicas del notebook 03:

- `LinearRegression` para objetivo continuo.
- `LogisticRegression` para objetivo binario (dirección).

Opcional para comparación breve:

- `Ridge`, `Lasso` o `ElasticNet`.

**Regla:** menos modelos, más interpretación.

---

### 8) Evaluación y visualización

Si haces **regresión**:

- `R2`
- `MSE`
- gráfico real vs predicho

Si haces **clasificación**:

- `classification_report`
- `confusion_matrix`
- ROC-AUC (opcional)

Además, para enfoque financiero:

- mini backtest long/cash
- usar `signal.shift(1)` para evitar look-ahead bias
- curva acumulada vs benchmark simple

---

### 9) Conclusiones (parte clave de la entrega)

Estructura sugerida:

- Qué hipótesis se confirma y cuál no.
- Qué variables/factores aportaron más señal.
- Qué limitaciones tiene el enfoque.
- Riesgos de sobreajuste o leakage.
- Próximos pasos realistas:
  - validación walk-forward
  - regularización
  - control de costes de transacción
  - mayor robustez temporal

---

## Decisiones que deben quedar explícitas en el notebook

- Umbral de faltantes para excluir fondos.
- Método de imputación elegido.
- Frecuencia temporal final usada.
- Definición exacta de `y` y horizonte de predicción.
- Método de partición temporal train/test.
- Métricas seleccionadas y por qué.
- Cómo se evita leakage (alineación temporal y `shift` en señal).

---

## Organización práctica para que sea claro y evaluable

- Bloques cortos: 1 celda de código + 1 celda Markdown de interpretación.
- Comentarios simples en el código: qué hace y por qué.
- Cada gráfico con título y breve interpretación.
- Tabla final resumen con:
  - decisiones de preprocesado
  - métricas principales
  - conclusión ejecutiva

---

## Objetivo final de la tarea

Demostrar que sabes aplicar de forma coherente:

- **Preprocessing** (`MIAX ML - 01`)
- **Supervised learning** (`MIAX ML - 03`)

sobre un caso financiero real, justificando decisiones y presentando resultados de manera simple, reproducible y comprensible.