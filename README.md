# Análisis del Impacto del Parque Automotor en la Calidad del Aire en Colombia

Este proyecto desarrolla un pipeline ETL estructurado con un enfoque transversal hacia la **Ciencia de Datos e Inteligencia Artificial**. Su propósito principal es integrar y estructurar datos de calidad del aire y del crecimiento del parque automotor en territorio colombiano para analizar su comportamiento temporal y territorial, posibilitando la preparación de la información para modelos predictivos y exploratorios.

## 📌 Contexto y Definición del Problema
La calidad del aire es uno de los principales factores ambientales que afectan la salud pública, la calidad de vida de la población y la sostenibilidad urbana. En Colombia, el monitoreo se realiza a través de estaciones consolidadas por el IDEAM. Acorde a la OMS, la contaminación atmosférica causa 7 millones de muertes prematuras anuales a nivel mundial.

En el caso de Colombia, esta problemática presenta una correlación inherente con el crecimiento sostenido del parque automotor, el cual superó los 19.8 millones de vehículos a finales de 2024 (Boletín 005 - RUNT). Dado que gran parte de la información se publica operativamente (sin métricas directas para contrastes transversales), surgen preguntas base para el proyecto:
*   ¿Existe relación entre el crecimiento vehicular y la calidad del aire en las ciudades?
*   ¿Qué territorios presentan mayor presión ambiental asociada al transporte?
*   ¿Cómo evolucionan simultáneamente la contaminación y el parque automotor?
*   ¿Puede el comportamiento vehicular ayudar a explicar variaciones en contaminantes?

## 🎯 Objetivo General
Diseñar un **pipeline ETL** que posibilite extraer, integrar y estructurar datos de calidad del aire y del crecimiento del parque automotor en Colombia provenientes de múltiples fuentes estatales. A través de este proceso se busca transformar la estructura granular de la información original en variables *cross-section* comparables por dominio espacial y temporal.

## 🗄️ Diccionario y Fuentes de Datos
Los datos son consumidos desde la plataforma de **Datos Abiertos de Colombia** en formatos API estructurados.

| Característica | Descripción |
| :--- | :--- |
| **Entidad Reguladora** | IDEAM – Subsistema SISAIRE |
| **Tipo de datos** | Datos estructurados, series de tiempo ambientales |
| **Formato** | API (JSON) y CSV |
| **Volumen (#Registros)** | ~32.8 millones escalables. |
| **Dimensiones (#Variables)** | 18 columnas originales promedio. |
| **Unidad de análisis** | Medición individual de contaminante en una estación durante un intervalo de tiempo. |
| **Variables destacadas** | Estación, contaminante (`MSFL_CODE`), concentración, fecha (inicio/fin), latitud/longitud, departamento. |
| **Tipos de variables** | Texto, métricas numéricas y marcas de tiempo (*timestamps*). |

### Datasets en Uso:
1.  [Calidad del Aire en Colombia](https://www.datos.gov.co/resource/g4t8-zkc3.csv)
2.  [Calidad Del Aire En Colombia (Promedio Anual)](https://www.datos.gov.co/resource/kekd-7v7h.csv)
3.  [CRECIMIENTO DEL PARQUE AUTOMOTOR RUNT 2.0](https://www.datos.gov.co/resource/u3vn-bdcy.csv)

## ⚙️ Arquitectura Técnica y Transformaciones (ETL)

El archivo primario del repositorio (`ETL ideam.ipynb`) se encarga de transaccionar los datos mediante las siguientes etapas:

1.  **Extracción Programática:** Utilización de las bibliotecas de `pandas` y consultas REST limitadas para capturar volúmenes iniciales representativos de los 3 dominios de datos.
2.  **Limpieza y Tratamiento (Data Wrangling):**
    *   Estandarización y normalización unicode (eliminación de caracteres especiales/tilde).
    *   Formateo riguroso de *timestamps* a objetos temporales.
    *   Derivación dimensional: Agrupación mensual o anual (Creación de llaves universales como `Periodo (YYYY-MM)`).
    *   Validación y eliminación focalizada de valores nulos estructurales (`dropna()`).
3.  **Matriz de Ingeniería de Características (Pivot/Feature Engineering):** 
    *   Resúmenes estadísticos (media, min, max).
    *   Transformación pivotante para pasar de estructura larga a ancha, convirtiendo métricas de aire (`pm10`, `o3`, `dviento`) en `Features` de modelado.
4.  **Integración de Dimensiones Cruzadas (Join):** Combinación transversal usando `inner join` para correlacionar los volúmenes limpios del RUNT (por Departamento y Periodo) contra los registros condensados de polución atmosférica, junto a una normalización logarítmica algorítmica (`log_total_vehiculos`) útil para suprimir distribuciones asimétricas en modelos de *Machine Learning*.
