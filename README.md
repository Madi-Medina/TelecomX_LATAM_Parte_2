# Predicción de Churn en Telecom X

## Descripción del Proyecto

Este proyecto tiene como objetivo predecir la probabilidad de que los clientes de Telecom X abandonen (churn) el servicio. Utilizando un conjunto de datos histórico de clientes, se desarrollan y evalúan modelos de Machine Learning para identificar los factores clave que influyen en la cancelación y proponer estrategias de retención efectivas.

## Contenido del Repositorio

*   `df_final.csv`: El conjunto de datos original utilizado para el análisis.
*   `Este Notebook`: Contiene todo el proceso de ETL (Extracción, Transformación, Carga), EDA (Análisis Exploratorio de Datos), modelado predictivo y análisis de resultados.

## Metodología

El proceso se dividió en las siguientes etapas:

### 1. Preparación de los Datos

*   **Extracción:** Carga de los datos desde un archivo CSV.
*   **Eliminación de columnas irrelevantes:** Se eliminó la columna `customerID` por no ser útil para el modelado.
*   **Encoding:** Se aplicó One-Hot Encoding a las variables categóricas (`InternetService`, `Contract`, `PaymentMethod`) y se mapeó la variable `gender` a valores numéricos (Female: 0, Male: 1). Las columnas booleanas resultantes del One-Hot Encoding se convirtieron a tipo entero. La variable objetivo `Churn` también se convirtió a entero.
*   **Verificación de la proporción de cancelación:** Se analizó el desbalance de clases en la variable objetivo `Churn` (73.46% 'Stayed' vs 26.54% 'Churned').
*   **Estandarización:** Se escalaron las características numéricas utilizando `StandardScaler` para preparar los datos para el modelado.

### 2. Correlación y Selección de Variables

*   **Análisis de correlación:** Se calculó la correlación de cada variable con la variable `Churn` para identificar los factores más influyentes.
    *   **Variables que más aumentan el churn:** `Contract_Month-to-month`, `InternetService_Fiber optic`, `PaymentMethod_Electronic check`, `Cuentas_Diarias`, `Charges_Monthly`.
    *   **Variables que más disminuyen el churn:** `tenure`, `Contract_Two year`, `InternetService_No`, `Charges_Total`, `OnlineSecurity`.
*   **Análisis Dirigido:** Se realizaron visualizaciones (diagramas de caja) para explorar la relación entre `tenure` y `Charges_Total` con `Churn`.

### 3. Modelado Predictivo

*   **Separación de datos:** Los datos se dividieron en conjuntos de entrenamiento y prueba (80% / 20%) utilizando `stratify=y` para mantener la proporción de clases en `Churn`.
*   **Creación de Modelos:** Se entrenaron dos modelos:
    *   **Regresión Logística (`LogisticRegression`)**
    *   **Random Forest Classifier (`RandomForestClassifier`)**

### 4. Evaluación de los Modelos

Se evaluó el rendimiento de ambos modelos utilizando `classification_report`, `confusion_matrix` y las puntuaciones de entrenamiento/prueba.

| Modelo              | Precision (Stayed) | Recall (Stayed) | F1-Score (Stayed) | Precision (Churned) | Recall (Churned) | F1-Score (Churned) | Accuracy | Train Score | Test Score |
| :------------------ | :----------------: | :-------------: | :---------------: | :-----------------: | :--------------: | :-----------------: | :------: | :---------: | :--------: |
| **Regresión Logística** |        0.84        |       0.89      |        0.86       |        0.64         |       0.52       |        0.57         |   0.79   |    0.810    |    0.793   |
| **Random Forest**   |        0.82        |       0.89      |        0.86       |        0.62         |       0.47       |        0.54         |   0.78   |    0.998    |    0.782   |

### Overfitting / Underfitting

*   **Regresión Logística (Train: 81% | Test: 79.3%):** Sin overfitting significativo. La diferencia de solo 2% indica que el modelo generaliza bien a datos nuevos.
*   **Random Forest (Train: 99.8% | Test: 78.2%):** Claro overfitting. El modelo memorizó los datos de entrenamiento casi perfectamente (99.8%) pero su rendimiento cae a 78.2% en datos nuevos.

### Conclusión del Análisis de Modelos

La **Regresión Logística** es el modelo más confiable y preferido por tres razones:
1.  Mayor `recall` en la clase 'Churned' (52% vs 47%), lo cual es crucial para identificar clientes en riesgo.
2.  Mejor capacidad de generalización y ausencia de overfitting.
3.  Mayor interpretabilidad de sus coeficientes.

### 5. Análisis de la Importancia de las Variables

Se visualizaron las importancias de las características para el modelo Random Forest y los coeficientes para la Regresión Logística para entender la contribución de cada variable a la predicción de churn.

#### Top 10 Feature Importance - Random Forest

1.  Charges_Total
2.  tenure
3.  Charges_Monthly
4.  Cuentas_Diarias
5.  Contract_Month-to-month
6.  PaymentMethod_Electronic check
7.  InternetService_Fiber optic
8.  gender
9.  PaperlessBilling

#### Feature Coefficients - Logistic Regression

**Factores que aumentan la probabilidad de cancelación (coeficientes positivos):**

*   `InternetService_Fiber optic`
*   `Contract_Month-to-month`
*   `PaymentMethod_Electronic check`
*   `Charges_Monthly`
*   `StreamingTV`, `StreamingMovies`
*   `PaperlessBilling`
*   `SeniorCitizen`

**Factores que disminuyen la probabilidad de cancelación (coeficientes negativos):**

*   `tenure`
*   `Contract_Two year`
*   `OnlineSecurity`
*   `TechSupport`
*   `InternetService_No`
*   `PaymentMethod_Bank transfer (automatic)`, `PaymentMethod_Credit card (automatic)`

## Estrategias de Retención Propuestas

Basándose en los factores identificados, se proponen las siguientes estrategias para Telecom X:

1.  **Incentivar Contratos a Largo Plazo:** Ofrecer descuentos o beneficios exclusivos a clientes que opten por contratos de uno o dos años.
2.  **Mejorar la Experiencia de Fibra Óptica:** Investigar las causas de insatisfacción entre usuarios de fibra óptica y ofrecer soporte proactivo o paquetes de valor añadido.
3.  **Optimizar Métodos de Pago:** Promover métodos de pago automáticos (transferencia bancaria, tarjeta de crédito) con incentivos y analizar las preferencias de los usuarios de cheque electrónico.
4.  **Programas de Lealtad para Clientes Antiguos:** Recompensar la antigüedad del cliente con beneficios exclusivos para reforzar su lealtad.
5.  **Valorar Servicios de Seguridad y Soporte:** Promover activamente `OnlineSecurity` y `TechSupport` como valores añadidos que mejoran la experiencia del cliente y reducen el churn.
6.  **Monitoreo Proactivo de Clientes con Altos Cargos:** Identificar y contactar a clientes con cargos mensuales o totales elevados para ofrecer revisiones de planes o alternativas.
7.  **Segmentación para Senior Citizens:** Investigar necesidades específicas de clientes mayores y diseñar planes o soporte adaptado.

Al implementar estas estrategias, Telecom X puede abordar las causas fundamentales de la cancelación y mejorar significativamente sus tasas de retención de clientes.

