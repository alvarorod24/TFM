# Trabajo Fin de Máster
## Evaluación de la interpretabilidad de modelos de Machine Learning en la predicción de ictus mediante métricas de consenso intermodelo (XAIqi y XAIci)

**Autor:** Álvaro Rodríguez González

**Programa:** Máster Universitario en Bioinformática y Bioestadística (UOC)  
**Curso académico:** 2025 - 2026

---

## 1. Descripción del proyecto

Este repositorio contiene el código y los notebooks asociados al Trabajo Fin de Máster, cuyo objetivo es evaluar dos métricas de explicabilidad desarrolladas por el grupo de investigación del tutor, XAIqi (estabilidad y calidad de la importancia de cada variable) y XAIci (consistencia inter-modelo y complejidad de la tarea predictiva), aplicadas a la predicción de ictus a partir de datos públicos.

El estudio adopta una perspectiva intermodelo, por lo que, en lugar de seleccionar un único modelo óptimo, se entrenan cinco algoritmos heterogéneos sobre cada dataset y las explicaciones SHAP resultantes se evalúan mediante métricas de consenso. Este enfoque permite analizar la interpretabilidad a tres niveles complementarios: complejidad de la tarea, robustez frente a perturbaciones del espacio de variables, y fiabilidad de las explicaciones generadas.

---

## 2. Datasets utilizados

Los datos empleados son públicos y no se incluyen en este repositorio por motivos de tamaño y licencia. Pueden obtenerse directamente desde las fuentes originales:

- **Stroke Prediction Dataset (Kaggle)**  
  Variables: 12 (clínicas, demográficas y de estilo de vida).  
  Tamaño: 5.110 observaciones, prevalencia de ictus del 4,87%.  
  Fuente: https://www.kaggle.com/datasets/fedesoriano/stroke-prediction-dataset

- **NHANES (National Health and Nutrition Examination Survey)**  
  Subconjunto curado del programa NHANES (CDC, EE. UU.), ciclos 2017 - 2020.  
  Variables: 54 (clínicas, demográficas, analíticas y de estilo de vida).  
  Tamaño: 7.785 observaciones, prevalencia de ictus del 4,71%.  
  Fuente: https://wwwn.cdc.gov/nchs/nhanes/

Ambos datasets se han utilizado respetando sus condiciones de acceso y licencia. El procedimiento de descarga y preparación se documenta en los notebooks de cada bloque.

---

## 3. Estructura del repositorio

El repositorio se organiza en torno a una carpeta principal `notebooks/`, que contiene los ocho cuadernos del pipeline analítico, junto con el archivo de dependencias y el presente documento.

**notebooks/** (cuadernos del pipeline analítico)

Bloque Kaggle:
- `Kaggle_EDA.ipynb`: análisis exploratorio del dataset Kaggle.
- `Kaggle_Prep.ipynb`: preprocesamiento, split estratificado y técnicas de balanceo.
- `Kaggle_train_val.ipynb`: entrenamiento, validación y ajuste de hiperparámetros de los cinco modelos.
- `Kaggle_XAI.ipynb`: interpretabilidad SHAP y cálculo de XAIqi y XAIci.

Bloque NHANES:
- `NHANES_EDA.ipynb`: análisis exploratorio del subconjunto NHANES.
- `NHANES_Prep.ipynb`: preprocesamiento, split estratificado y técnicas de balanceo.
- `NHANES_train_val.ipynb`: entrenamiento, validación y ajuste de hiperparámetros de los cinco modelos.
- `NHANES_XAI.ipynb`: interpretabilidad SHAP y cálculo de XAIqi y XAIci.

**Archivos en la raíz:**
- `requirements.txt`: dependencias exactas del entorno de ejecución.
- `README.md`: documentación del repositorio.

Cada bloque (Kaggle y NHANES) está dividido en cuatro cuadernos numerados de forma implícita por su rol en el pipeline. La ejecución debe seguir el orden EDA, Prep, train_val, XAI.I.

---

## 4. Pipeline metodológico

### 4.1 Análisis exploratorio (`*_EDA.ipynb`)
- Caracterización descriptiva de variables numéricas, categóricas y ordinales.
- Análisis de valores faltantes y outliers.
- Estudio de la distribución de la variable objetivo (Stroke).
- Recodificación de variables NHANES desde sus códigos originales.

### 4.2 Preprocesamiento (`*_Prep.ipynb`)
- División estratificada train/test (80/20).
- Imputación de valores ausentes.
- Codificación de variables categóricas (one-hot, ordinal).
- Escalado de variables numéricas mediante MinMaxScaler.
- Gestión del desbalanceo de clases (Undersampling en ambos datasets; SMOTE adicional en Kaggle a efectos comparativos).

### 4.3 Entrenamiento y validación (`*_train_val.ipynb`)
Se entrenan cinco algoritmos por dataset:
- Regresión Logística
- Random Forest
- Support Vector Machine
- XGBoost
- Red neuronal (Keras / SciKeras)

Para cada algoritmo se aplica RandomizedSearchCV con validación cruzada estratificada de 5 folds, optimizando AUROC como métrica principal y reportando además F1, Recall, Precision, Accuracy y Balanced Accuracy.

### 4.4 Interpretabilidad y métricas XAI (`*_XAI.ipynb`)
- Cálculo de valores SHAP por modelo (TreeExplainer, KernelExplainer y DeepExplainer según corresponda).
- Cálculo de XAIqi (estabilidad por variable) y XAIci (consenso global entre modelos).
- Comparación intramodelo (modelos basados en árboles vs. resto).
- Experimentos de ablación: eliminación de las K variables más importantes y reevaluación de AUROC y XAIci.
- Análisis de coherencia epidemiológica de los rankings XAIqi frente a la literatura (INTERSTROKE, GBD 2021, WSO).

---

## 5. Instalación del entorno

Se recomienda crear un entorno virtual e instalar las dependencias mediante:

```bash
pip install -r requirements.txt
```

El archivo `requirements.txt` recoge las versiones exactas utilizadas en los experimentos para garantizar la reproducibilidad.

---

## 6. Tecnologías utilizadas

- Python 3.11
- scikit-learn
- XGBoost
- TensorFlow / Keras (vía SciKeras)
- SHAP
- pandas, numpy, scipy
- imbalanced-learn
- matplotlib, seaborn

---

## 7. Reproducibilidad

Todos los experimentos pueden reproducirse ejecutando los notebooks en el orden indicado en la sección 3. Se ha fijado `random_state = 24` en todos los procesos estocásticos (splits, balanceo, RandomizedSearchCV, inicialización de modelos). Los resultados numéricos se reportan en la memoria. Los resultados pueden presentar variaciones residuales debidas a la aleatoriedad propia del entrenamiento de redes neuronales y a la estimación de SHAP mediante muestreo. 

---

## 8. Notas sobre el uso de datos

- Los datasets son públicos y de uso libre para investigación, pero deben descargarse manualmente desde sus fuentes oficiales.
- Ningún dato personal identificable se almacena ni se procesa fuera de los formatos originalmente publicados por Kaggle y NHANES.
- El tratamiento se ajusta a los principios éticos detallados en la sección correspondiente de la memoria (epígrafe 1.3).

---

## 9. Licencia

El código de este repositorio se distribuye con fines exclusivamente académicos en el marco del TFM. Los datasets pertenecen a sus respectivos autores y plataformas, y se utilizan conforme a las licencias originales de cada fuente.
