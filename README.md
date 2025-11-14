# Proyecto gobierno de datos
# Proyecto de Predicción de Churn de Clientes

## Descripción general del proyecto y objetivo de negocio

El objetivo de este proyecto es desarrollar un modelo de **Machine Learning** capaz de **predecir la probabilidad de que un cliente abandone la empresa (churn)**, utilizando información histórica de comportamiento de compra.  
A partir de los datos transaccionales de clientes, órdenes y productos, se busca identificar patrones que anticipen la inactividad prolongada de un cliente y permitan **diseñar estrategias de retención efectivas**.

Las principales fuentes de datos utilizadas son:
- `clients_*.csv`: información de clientes.
- `orders_*.csv`: historial de compras y órdenes.
- `products_*.csv`: detalles de productos.

---

## Justificación de la definición de churn

En este proyecto, un cliente se considera **en churn (target = 1)** si **no realizó ninguna compra durante más de 90 días consecutivos** a partir de la fecha de referencia (`2024-06-01`).

En la práctica, se definió una **ventana de observación de 4 meses (junio–septiembre 2024)**.  
- Si el cliente **no realizó ninguna compra en esa ventana**, se asume que **ha abandonado la empresa**.  
- Si el cliente **sí realizó al menos una compra**, se considera **activo (target = 0)**.

Esta definición refleja una política de negocio en la que **un cliente con más de 90 días de inactividad ya no se considera activo**, por lo que su retención requiere acciones específicas de marketing o incentivos.

---

## Instrucciones para reproducir el entorno y el flujo de trabajo

### 1️Requisitos

- **Python 3.9+**
- Librerías necesarias:
  ```bash
  pip install pandas numpy scikit-learn matplotlib seaborn mlflow
Estructura recomendada del repositorio:
project_root/
│
├── data/
│   ├── clients_YYYYMMDDHHMM.csv
│   ├── orders_YYYYMMDDHHMM.csv
│   ├── products_YYYYMMDDHHMM.csv
│
├── notebooks_or_scripts/
│   └── churn_modeling.py
│
└── README.md
**Flujo de trabajo del proyecto**

Carga y preprocesamiento de datos

Lectura de los archivos .csv.

Unión de órdenes con productos y limpieza de tipos de datos.

Conversión de fechas y creación del dataset principal.

Construcción de métricas por cliente

Generación de variables agregadas mediante la función build_customer_metrics().

Cálculo de indicadores como: gasto total, frecuencia de compra, categorías preferidas, ratio de envío express, etc.

Definición del target (churn)

Fecha de referencia: 2024-06-01.

Ventana futura: 2024-06-01 → 2024-09-30.

Clientes sin compras en esa ventana → target = 1.

Entrenamiento y evaluación de modelos

División train/test (80/20) estratificada por la variable objetivo.

Modelos evaluados:

Regresión Logística

Árbol de Decisión

Random Forest

Gradient Boosting

Métricas: Accuracy, F1, Precision, Recall, ROC-AUC, PR-AUC.

Interpretabilidad

Cálculo y visualización de feature importances para modelos de árboles.

Seguimiento y trazabilidad con MLflow (ver siguiente sección).
**Uso de MLflow y conexión a la base de datos**

Se utiliza MLflow para el seguimiento de experimentos, registro de hiperparámetros, métricas y versiones de modelos.
Esto permite comparar el desempeño de distintos algoritmos y mantener la trazabilidad del proceso de entrenamiento.

Ejemplo de uso (sin exponer credenciales):
import mlflow
import mlflow.sklearn

mlflow.set_tracking_uri("http://<mlflow_server>:5000")
mlflow.set_experiment("churn_prediction")

with mlflow.start_run(run_name="RandomForest"):
    mlflow.log_params({"n_estimators": 100, "random_state": 42})
    mlflow.log_metrics(results["RandomForest"])
    mlflow.sklearn.log_model(pipe, "model")

  ***  Nota de seguridad:**
Las credenciales o URIs deben almacenarse en variables de entorno o archivos .env:
export MLFLOW_TRACKING_URI="http://mlflow.myserver.com:5000"
export DATABASE_URL="postgresql://user:password@host:port/dbname"

**Resultados esperados**

Modelos con desempeño competitivo en métricas como ROC-AUC y F1-score.

Identificación de variables con mayor impacto en la probabilidad de abandono.

Pipeline reproducible y auditado con MLflow.
**Próximos pasos**

Ajuste fino de hiperparámetros.

Inclusión de datos adicionales (interacciones, soporte, visitas web).

Despliegue del modelo en un entorno productivo o dashboard analítico.
