# 🔧 Modelo Predictivo de Fallas en Vehículos Eléctricos

Este proyecto implementa un modelo de **machine learning** para predecir fallas en vehículos eléctricos (EV) a partir de datos históricos de telemetría. El objetivo es apoyar estrategias de **mantenimiento predictivo**, reduciendo tiempos de inactividad, costos operativos y riesgos de seguridad asociados a fallos críticos. :contentReference[oaicite:0]{index=0}  

---

## 🎯 Objetivo

Construir un **clasificador binario** que estime la probabilidad de que un vehículo presente una falla (código DTC) en una ventana temporal próxima, utilizando variables como:

- Estado de carga y salud de la batería (SOC, SOH)
- Ciclos de carga
- Temperaturas de batería y motor
- RPM y torque del motor
- Presión de neumáticos y desgaste de frenos
- Códigos de diagnóstico (DTC) :contentReference[oaicite:1]{index=1}  

---

## 📊 Dataset

- Fuente: archivo `heavy_user.csv` (perfil de uso intensivo).
- Registros: ~43 800 filas con datos horarios (2020–2024).
- Características principales:
  - `soc`, `soh`, `charging_cycles`
  - `battery_temp`, `motor_temp`
  - `motor_rpm`, `motor_torque`
  - `tire_pressure`, `brake_pad_wear`
  - `dtc` (variable objetivo, binarizada: 0 = sin fallo, 1 = fallo) :contentReference[oaicite:2]{index=2}  

Características clave del dataset:

- **Sin valores nulos.**
- Columna de voltaje de carga (`charging_voltage`) eliminada por ser constante.
- Columna temporal (`timestamp`) convertida a `datetime` para análisis y luego eliminada para el modelo.
- **Fuerte desbalance de clases**: la mayoría de registros son “sin fallo”, lo que obliga a usar métricas robustas (F1, recall, ROC-AUC).   

---

## 🧪 Metodología

### 1. Preprocesamiento de datos

- Normalización de nombres de columnas a `snake_case`.
- Ajuste de tipos:
  - `timestamp` → `datetime`
  - `dtc` → `category` / entero binario (0/1)
- Eliminación de columnas sin variabilidad (p. ej. `charging_voltage`) y de índice innecesario.
- Análisis de **outliers**:
  - Se decidió **conservar** outliers de variables como `battery_temp`, `motor_rpm`, `motor_torque` y `motor_temp`, ya que representan condiciones críticas reales que preceden a las fallas.   

### 2. EDA (Análisis Exploratorio de Datos)

- Estadísticos descriptivos (`.describe()`).
- Histogramas y KDE para todas las variables numéricas.
- Boxplots para identificar rangos normales vs. eventos extremos.
- Análisis detallado de la variable objetivo `dtc`:
  - Identificación de desbalance severo entre clases.
  - Discusión de impacto en métricas (accuracy engañosa vs. F1/recall).   

### 3. Modelado

Se evaluaron distintos algoritmos de clasificación:

- Regresión Logística
- Random Forest
- XGBoost (modelo principal seleccionado)   

Estrategia:

- División en conjuntos de entrenamiento y prueba.
- Manejo del desbalance con:
  - Ajuste de pesos de clase / parámetros del modelo.
  - Enfoque en métricas como **F1-score** y **Recall** para la clase de fallos.
- Comparación de modelos con:
  - Matriz de confusión
  - ROC-AUC
  - Precision, Recall, F1-score

### 4. Optimización

Para el modelo XGBoost:

- Búsqueda de hiperparámetros usando:
  - `GridSearchCV`
  - `RandomizedSearchCV`
- Selección del modelo final en función de F1 y ROC-AUC sobre el conjunto de validación. :contentReference[oaicite:7]{index=7}  

---
└── README.md
