# Convenio PLUS TI – Universidad del Valle 2025  
**“Métricas custom para reducción de falsos positivos en clasificación binaria - fraude”**

##  Descripción General

Este proyecto tiene como objetivo diseñar, implementar y evaluar modelos de clasificación binaria enfocados en la detección de fraudes financieros, aplicando **LightGBM** y técnicas avanzadas de ingeniería de variables y optimización de umbrales. El trabajo se enmarca en el contexto del Convenio PLUS TI 2025 y busca reducir los falsos positivos sin comprometer significativamente la capacidad de detección (recall).

##  Metodología

- Se simuló el comportamiento de **transacciones internacionales**, calculando la distancia entre el comercio y el centroide de su estado usando la fórmula de **Haversine**.
- Se asignó un **radio estimado** a cada estado para determinar si una transacción es **is_international**.
- Se diseñaron múltiples variables geográficas como:
  - `dist_to_state_center`, `state_radius_ratio`, `is_borderline`, `dist_category`, `extreme_distance_flag`, etc.

Además, se aplicaron variables generales como:
- `time_since_last_tx`, `delta_amt`, `z_amt`, `tx_rolling_freq`, `amt_per_tx`, `repeat_rate`, `amt_ratio_month`.

##  Visualización del Contexto Geográfico

Puedes abrir el archivo `estados.html` para visualizar un **mapa interactivo de los Estados Unidos** donde se muestra el centro geográfico y el radio aproximado de cada estado. Esta visualización permite entender la lógica detrás de la creación de la variable `is_international`.

##  Modelos Entrenados

Se entrenaron distintos modelos usando **LightGBM**, tanto generales como específicos para transacciones etiquetadas como internacionales.  
También se aplicaron **métricas de evaluación personalizadas** como:

- `ratio_fp_tp`: penaliza falsos positivos.
- `f1_penalizado`: penaliza el F1 si hay muchos FP.
- `precision_at_recall`: exige mínimo Recall para validar precisión.

Además, se implementaron funciones para encontrar umbrales óptimos bajo restricciones como `Recall ≥ 0.90` y `Ratio (TP+FP)/TP ≤ 3.0`.

##  Resultados

**Modelo General (con todo el dataset):**

| Conjunto    | AUC    | Precisión | Recall | F1    | Ratio FP/TP |
|-------------|--------|-----------|--------|-------|--------------|
| Validación  | 0.9997 | 0.946     | 0.919  | 0.933 | 1.057        |
| Test (0.5)  | 0.9983 | 0.934     | 0.818  | 0.872 | 1.071        |
| Test (adj)  | 0.9983 | 0.698     | 0.903  | 0.787 | 1.433        |

**Modelo de transacciones internacionales:**

| Conjunto | AUC    | Precisión | Recall | F1    | Ratio FP/TP |
|----------|--------|-----------|--------|-------|--------------|
| Test     | 0.9989 | 0.879     | 0.764  | 0.817 | 1.137        |

**Modelos personalizados:**

| Modelo                | AUC    | Precisión | Recall | F1    | Ratio FP/TP | Umbral |
|-----------------------|--------|-----------|--------|-------|--------------|--------|
| F1_Penalizado         | 0.9981 | 0.922     | 0.822  | 0.869 | 1.085        | 0.7075 |
| Precision@Recall≥0.8  | 0.9890 | 0.746     | 0.806  | 0.775 | 1.341        | 0.0051 |
| Ratio_FP_TP           | 0.9780 | 0.560     | 0.802  | 0.659 | 1.787        | 0.0111 |

##  Estructura del Repositorio

- `Informe_Fraude_LightGBM.docx`: Informe técnico completo con resultados, conclusiones y justificación del modelo final.
- `estados.html`: Mapa interactivo de los estados de EE.UU. con sus radios geográficos estimados.
- `plusTI.ipynb`: Notebook con todo el proceso de carga, limpieza, ingeniería de variables, entrenamiento y evaluación de modelos.
- `README.md`: Este archivo.

##  Recomendación

El modelo **F1_Penalizado** demostró el mejor balance entre precisión y recall, manteniendo un ratio de falsos positivos bajo. Se recomienda su uso en producción con monitoreo y actualización periódica.

---

**Autoría**: Universidad del Valle de Guatemala – Convenio PLUS TI 2025  
**Tema**: Optimización de métricas personalizadas para clasificación de fraude en datos financieros.
