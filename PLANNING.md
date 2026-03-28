## Plan de rehacer el notebook y retoques 
### Tabla rápida: Objetivo y Acción

| Item | Descripción |
|---|---|
| Objetivo | Rehacer `TAREA.ipynb` para medir sesgo Asia con OLS (β, p, t, R² adj.) con limpieza mínima |
| Acción | Duplicar el notebook, recortar a lo esencial y añadir bloques de fines de semana, outliers, OLS y umbrales |

### Retoques a implementar
- Fines de semana: eliminar sábados y domingos antes de cualquier `ffill` para no aplanar la serie.
- Outliers: tratar outliers en retornos (no en NAV) por fondo, preferible tras remuestreo semanal.
- OLS (statsmodels): `excess_ret ~ const + Mkt-RF + SMB + HML + WML`, extrayendo β, p, t, R² y R² ajustado.
- Umbral Asia: definir “sesgo Asia verdadero” con thresholds sobre `beta_mkt`, significancia de `p_mkt` y `R²_adj`.
- Retorno en exceso: usar `excess_ret = ret - RF` en semanal, perfectamente alineado con factores.
- scaler?????????????????
- mencionar la bilbiografia para el modelooo
- retocar todos los mds d ela parte 4 para delante (semana diltro)


### Bloques de código para el “nuevo” notebook

2) Remuestreo semanal y retornos con tratamiento de outliers

```python
# Remuestreo semanal a miércoles (coherente con factores)
nav_w = (daily_panel
        .set_index('date')
        .groupby('fund_id')['nav']
        .resample('W-WED').last()
        .reset_index())
# Log-retornos semanales por fondo
nav_w['ret'] = nav_w.groupby('fund_id')['nav'].apply(lambda s: np.log(s).diff())
# Winsorización por fondo (IQR) en retornos semanales
def winsorize_iqr(x, k=3.0):
    q1, q3 = np.nanpercentile(x, [25, 75])
    iqr = q3 - q1
    lo, hi = q1 - k*iqr, q3 + k*iqr
    return np.clip(x, lo, hi)
nav_w['ret_winz'] = nav_w.groupby('fund_id')['ret'].transform(winsorize_iqr)
```

3) Factores alineados + retorno en exceso

```python
# factors_df diario con ['date','Mkt-RF','SMB','HML','WML','RF'] en %
factors_df['date'] = pd.to_datetime(factors_df['date'])
# Filtrar fines de semana también en factores
factors_df = factors_df[factors_df['date'].dt.weekday < 5]
# Remuestreo a miércoles y pasar de % a decimales
fac_w = (factors_df.set_index('date').resample('W-WED').mean().reset_index())
for c in ['Mkt-RF','SMB','HML','WML','RF']:
    fac_w[c] = fac_w[c] / 100.0
# Unir y crear retorno en exceso
df = nav_w.merge(fac_w, on='date', how='inner')
df['excess_ret'] = df['ret_winz'] - df['RF']
```

4) OLS por fondo (β, p, t, R², R² ajustado)

```python
import statsmodels.api as sm
def fit_ols_group(g):
    y = g['excess_ret']
    X = g[['Mkt-RF','SMB','HML','WML']]
    mask = y.notna() & X.notna().all(axis=1)
    y, X = y[mask], X[mask]
    if len(y) < 52:  # mínimo 1 año semanal
        return pd.Series({k: np.nan for k in [
            'alpha','beta_mkt','beta_smb','beta_hml','beta_wml',
            'p_alpha','p_mkt','p_smb','p_hml','p_wml',
            't_alpha','t_mkt','t_smb','t_hml','t_wml',
            'r2','r2_adj','n_obs'
        ]} | {'n_obs': len(y)})
    X = sm.add_constant(X)
    model = sm.OLS(y, X).fit()
    return pd.Series({
        'alpha': model.params.get('const', np.nan),
        'beta_mkt': model.params.get('Mkt-RF', np.nan),
        'beta_smb': model.params.get('SMB', np.nan),
        'beta_hml': model.params.get('HML', np.nan),
        'beta_wml': model.params.get('WML', np.nan),
        'p_alpha': model.pvalues.get('const', np.nan),
        'p_mkt': model.pvalues.get('Mkt-RF', np.nan),
        'p_smb': model.pvalues.get('SMB', np.nan),
        'p_hml': model.pvalues.get('HML', np.nan),
        'p_wml': model.pvalues.get('WML', np.nan),
        't_alpha': model.tvalues.get('const', np.nan),
        't_mkt': model.tvalues.get('Mkt-RF', np.nan),
        't_smb': model.tvalues.get('SMB', np.nan),
        't_hml': model.tvalues.get('HML', np.nan),
        't_wml': model.tvalues.get('WML', np.nan),
        'r2': model.rsquared,
        'r2_adj': model.rsquared_adj,
        'n_obs': int(model.nobs)
    })
ols_by_fund = df.groupby('fund_id', group_keys=False).apply(fit_ols_group).reset_index()
```

5) Umbral para “sesgo Asia verdadero”

```python
# Ajusta estos umbrales según la distribución real de tus resultados
UMBRAL_BETA = 0.30
ALFA_SIG = 0.05
UMBRAL_R2ADJ = 0.10
asia_biased = ols_by_fund[
    (ols_by_fund['beta_mkt'] >= UMBRAL_BETA) &
    (ols_by_fund['p_mkt'] <= ALFA_SIG) &
    (ols_by_fund['r2_adj'] >= UMBRAL_R2ADJ)].copy()
```

6) Resumen OLS (sanity check)

```python
pd.Series({
    'fondos_total': ols_by_fund['fund_id'].nunique(),
    'fondos_validos': ols_by_fund['r2'].notna().sum(),
    'beta_mkt_mediana': ols_by_fund['beta_mkt'].median(),
    'beta_mkt_p95': ols_by_fund['beta_mkt'].quantile(0.95),
    'asia_biased_n': asia_biased['fund_id'].nunique()
})
```

### Resumen OLS (tabla de métricas)

| métrica | valor |
|---|---|
| fondos_total | — |
| fondos_validos | — |
| beta_mkt_mediana | — |
| beta_mkt_p95 | — |
| asia_biased_n | — |

### Estructura final sugerida del “nuevo” notebook
- Contexto y objetivo (medir sesgo Asia con OLS).
- Carga de datos y factores (reutilizar 2.1–2.5).
- EDA mínima (calidad/frecuencia básicas para justificar reglas).
- Limpieza focalizada:
  - Filtrar fines de semana.
  - `ffill(limit=3)` por fondo.
  - Outliers en retornos semanales (winsorización por fondo).
- OLS statsmodels (β, p, t, R² y R² ajustado).
- Selección por umbrales de “Asia-biased”.
- Conclusiones y siguientes pasos hacia MIAX ML - 09_05.

### Notas clave
- Retorno en exceso: usar siempre `excess_ret = ret - RF` en semanal y con fechas alineadas a factores.
- Volatilidad: filtrar fines de semana antes de `ffill` y limitar `ffill` evita aplanar la serie.
- Significancia: p-values y t-stats por coeficiente vienen del summary OLS; usa también R² adj. como control de calidad.

