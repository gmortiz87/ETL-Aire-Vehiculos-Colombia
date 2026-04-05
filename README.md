# 🌬️ Análisis del Impacto del Parque Automotor en la Calidad del Aire en Colombia

Este proyecto desarrolla un pipeline ETL-P (Extracción, Transformación, Carga y Predicción) estructurado con un enfoque transversal hacia la **Ciencia de Datos e Inteligencia Artificial**. Su propósito principal es integrar y estructurar datos de calidad del aire y del crecimiento del parque automotor en territorio colombiano para habilitar modelos predictivos de impacto ambiental.

## 📌 Contexto y Definición del Problema
En Colombia, la relación entre el crecimiento vehicular (que superó los 19.8 millones de vehículos en 2024) y la salud pública es crítica. Sin embargo, la información suele estar dispersa. Este proyecto resuelve la fragmentación de datos respondiendo a:
*   ¿Existe relación matemática entre el parque automotor y la polución ($PM_{10}$)?
*   ¿Cómo influye la **antigüedad de la flota** frente al volumen total de vehículos?
*   ¿Podemos predecir la tendencia nacional de calidad del aire usando solo registros administrativos?

## 🎯 Objetivo General
Diseñar un **pipeline ETL-P** que extraiga, integre y persista datos del IDEAM y RUNT en la nube, transformando estructuras granulares en un **Feature Store** optimizado para modelos de *Machine Learning*.

## 🗄️ Diccionario y Fuentes de Datos
Los datos son consumidos mediante las APIs de Socrata de la plataforma de **Datos Abiertos de Colombia**.

| Característica | Descripción |
| :--- | :--- |
| **Fuentes** | IDEAM (SISAIRE) / RUNT 2.0 |
| **Volumen** | ~32.8 millones de registros base |
| **Unidad de Análisis** | Observación mensual por Municipio/Departamento |
| **Feature Store** | 269 registros consolidados |

## ⚙️ Arquitectura Técnica y Transformaciones

1.  **Extracción Programática:** Uso de `pandas` y peticiones REST filtradas para optimizar el uso de memoria RAM (Efficient Extraction).
2.  **Transformación (Data Wrangling):**
    *   **Normalización Territorial:** Resolución de discrepancias en nombres de municipios (ej: "Bogotá D.C." vs "BOGOTA").
    *   **Feature Engineering:** Cálculo de la *Antigüedad Promedio de la Flota* y transformación logarítmica de volúmenes.
    *   **Broadcasting Temporal:** Técnica de proyección para sincronizar censos anuales (RUNT) con mediciones mensuales (IDEAM).
3.  **Persistencia Cloud (Supabase):** Carga automatizada del Feature Store en una base de datos **PostgreSQL** en la nube, exponiendo los datos vía API REST (JWT) para su consumo en tableros de BI.
4.  **Modelado AI:** Implementación de un algoritmo **Random Forest Regressor** para la detección de patrones de contaminación.

## 🏆 Resultados y Hallazgos Principales

### 📈 Rendimiento del Modelo
*   **Precisión Nacional ($R^2$): 32.5%**. El modelo captura exitosamente un tercio de la tendencia macro de contaminación del país usando exclusivamente variables vehiculares.
*   **Error Promedio (RMSE): 3.37 μg/m³**. Un margen de error altamente competitivo para modelos basados en registros administrativos.

### 🔑 Insights de Negocio/Estado
*   **La Antigüedad es la clave:** La IA identificó que la **obsolescencia tecnológica** de los vehículos es un predictor de contaminación superior a la cantidad bruta de carros.
*   **Valor del Feature Store:** Se entregó una base de datos limpia de 72 municipios que permite predecir tendencias de impacto ambiental sin necesidad de sensores costosos en cada esquina.

---
*Desarrollado como Proyecto Final de ETL - Universidad Autónoma de Occidente.*
