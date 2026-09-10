# Pipeline de Calidad de Datos: Banco de Sangre (Hospital General de Medellín)

**Autoras:** Alba Luz Preciado Pinillo | Alina Maria Alvarez
**Fecha:** Agosto 2026

Este repositorio contiene la arquitectura inicial de datos (fase exploratoria y preparación) para el dataset de extracciones del Banco de Sangre del Hospital General de Medellín. El objetivo del proyecto es auditar la calidad de los datos crudos (capa Bronze) y aplicar transformaciones basadas en reglas de negocio para generar un conjunto de datos sanitizado (capa Silver), apto para modelos analíticos avanzados o pronósticos de series de tiempo.

## 🗂️ Estructura del Repositorio

* `Banco_de_sangre,_Hospital_General_de_Medellín_20260821.csv`: Dataset original (Crudo/Bronze).
* `BANCO_DE_SANGRE_LIMPIO.csv` / `banco_sangre_silver.parquet`: Datasets resultantes exportados (Limpios/Silver).
* `diagnostico_datos.ipynb`: Notebook de Auditoría y Análisis Exploratorio (EDA).
* `preparacion_datos.ipynb`: Notebook de Limpieza, Transformación e Imputación de datos.
* `requirements.txt`: Dependencias de librerías de Python.
* `.gitignore`: Archivos excluidos del control de versiones (entornos virtuales, temporales).

## 🔍 Fase 1: Diagnóstico y Auditoría (EDA)
En el archivo `diagnostico_datos.ipynb` se evaluó la integridad de la base de datos sin aplicar mutaciones al dataset original. 

**Hallazgos Principales:**
* **Falla de Integridad Temporal:** Se detectó un colapso crítico en la captura de datos entre mediados de 2023 y mediados de 2024, evidenciando un hueco sistémico en los registros.
* **Violación de Reglas de Negocio:** El 46.1% de los registros presentó discrepancias matemáticas entre la edad reportada y la fecha de nacimiento. Se identificaron edades (14 y 104 años) y estaturas (1.00m) biológica y legalmente imposibles para el proceso de donación.
* **Completitud y Sesgo:** 21.7% de registros nulos en la variable `BARRIO`, con un patrón condicionado (MAR) correlacionado fuertemente con donantes procedentes de municipios externos a Medellín.
* **Estandarización Textual:** Errores sistemáticos de digitación, incluyendo el uso del dígito `0` en lugar de la letra `O` para el factor RH, y alta cardinalidad sucia en variables geográficas.

## ⚙️ Fase 2: Preparación y Limpieza (Capa Silver)
En el archivo `preparacion_datos.ipynb` se ejecutaron las correcciones dictadas por la matriz de decisiones generada en el diagnóstico.

**Transformaciones Aplicadas:**
* **Recálculo Determinístico de Edad:** Se descartó la variable original y se recalculó la edad matemáticamente (`FECHA EXTRACCION` - `FECHA NACIMIENTO`). Los registros fuera del rango legal de donación (18-65 años) fueron anulados (NaN) para evitar sesgos estadísticos.
* **Imputación Fisiológica Robusta:** Se aislaron los pesos (< 50kg) y estaturas atípicas. Los valores nulos resultantes fueron imputados dinámicamente utilizando la **mediana agrupada por sexo**, preservando la varianza biológica real de los pacientes.
* **Sanitización Categórica:** Corrección del factor RH (reemplazo de ceros) y estandarización estructural (mayúsculas, eliminación de espacios laterales) en ciudades y barrios.
* **Imputación Semántica:** Los nulos en `BARRIO` se rellenaron con la categoría `"NO REGISTRA"` para retener la volumetría de las extracciones sin falsificar ubicaciones.
* **Optimización de Memoria:** Eliminación de variables redundantes y casteo correcto de tipos de datos antes de la exportación final.

## 🚀 Instalación y Uso

1. Clonar el repositorio localmente.
2. Activar el entorno virtual:
   ```bash
   # En Windows (PowerShell)
   .\.venv\Scripts\Activate.ps1
