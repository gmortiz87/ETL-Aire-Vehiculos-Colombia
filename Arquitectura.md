# Walkthrough: Pipeline ETL Calidad del Aire vs. Parque Automotor

Este documento detalla la arquitectura del notebook y los métodos de extracción activos.

## 🏗️ Arquitectura del Sistema

El pipeline sigue una estructura modular de **5 Fases**:

### 1. Fase de Extracción (E) - **MÉTODOS ACTIVOS**
La extracción es **Dinámica y Parametrizada**. Los métodos detectados son:
*   **Fuente:** API Socrata (Servidor de `datos.gov.co`).
*   **Muestreo Estratificado:** Descarga por bloques anuales (2020-2026) para evitar saturación de memoria.
*   **Robustez:** Implementa un bucle de `intentos (max=3)` con `time.sleep` para manejar cortes de conexión del servidor (HTTPError/IncompleteRead).

| Dataset | Endpoint Socrata | Descripción |
| :--- | :--- | :--- |
| `air_colombia` | `g4t8-zkc3.csv` | Mediciones horarias/actuales por contaminante. |
| `air_annual` | `kekd-7v7h.csv` | Promedios anuales históricos para comparación. |
| `vehicle` | `u3vn-bdcy.csv` | Crecimiento del parque automotor RUNT 2.0. |

### 2. Fase de Transformación (T)
Se aplican técnicas de **Data Wrangling** para estandarizar los dominios:
*   **Normalización Unicode:** Función `normalizar_texto` que elimina acentos, convierte a minúsculas y remueve espacios extra.
*   **Feature Engineering:**
    *   Extracción de `anio_etl`, `mes_etl`, `dia_etl`.
    *   Creación de `periodo_etl` (Formato YYYY-MM) para cruces temporales precisos.
*   **Agregación Estadística:** Uso de `groupby` para obtener `concentracion_promedio`, `max` y `min` por ciudad y periodo.
*   **Pivoteo Dinámico:** Transformación de la columna `msfl_code` en múltiples columnas (una por contaminante: `cont_pm10`, `cont_o3`, etc.), facilitando el análisis multivariable.

### 3. Fase de Carga e Integración (L)
*   **Consolidación:** Se realiza un `inner join` basado en la llave `departamento_etl`.
*   **Optimización Asintótica:** Aplicación de escala logarítmica (`log1p`) a la columna `total_vehiculos` para normalizar la varianza antes de entrar a modelos de ML.

### 4. Destino (Feature Store)
*   **Formato:** CSV (`dataset_final_ml.csv`).
*   **Estado:** Generado exitosamente (15,000+ filas x 27 columnas).

---

## 📊 Métodos de Extracción Activos (Resumen Técnico)

1.  **Pandas I/O con URL directas:** `pd.read_csv(url_query)` con parámetros de límite.
2.  **API Rest Query:** Uso de cláusulas `$where` y `$limit` nativas de Socrata.
3.  **Buffer Sampling:** Concatenación de dataframes temporales en memoria para construir el dataset analítico final.
