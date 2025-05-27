# ovni-bigdata-gcp
Proyecto de análisis de avistamientos de OVNIs usando Google Cloud
# 🛸 Análisis de Avistamientos OVNI - Google Cloud Big Data
> **Evaluación Parcial 2 - Asignatura: Big Data (AVY1101)**  
> **Estudiante:**  Liliana Heracles, Diego Sanhueza y Lidia Nuñez 
> **Seccion: AVY1101 .**
>
> ## 📌 Índice
1. [Introducción](#introducción)
2. [Objetivo del proyecto](#objetivo-del-proyecto)
3. [Origen y carga de datos](#origen-y-carga-de-datos)
4. [Preparación y limpieza de datos](#preparación-y-limpieza-de-datos)
5. [Consultas SQL y análisis](#consultas-sql-y-análisis)
6. [Visualización en Looker Studio](#visualización-en-looker-studio)
7. [Conclusiones](#conclusiones)

8. ## 📖 Introducción

El presente informe tiene como objetivo presentar el desarrollo de un proceso completo de análisis de datos en un entorno Big Data, utilizando herramientas del ecosistema de Google Cloud. La actividad se enmarca en la Evaluación Parcial N°2 del curso Big Data (AVY1101), y consiste en la construcción de un flujo de trabajo que permita la ingesta, limpieza, transformación, análisis y visualización de grandes volúmenes de datos mediante procesos batch.

Para este propósito, se utilizó un archivo con registros históricos de avistamientos de objetos voladores no identificados (OVNIs), el cual fue cargado inicialmente en Cloud Storage como parte del diseño de un Data Lake. Posteriormente, el conjunto de datos fue procesado mediante Cloud Dataprep (Trifacta), donde se llevaron a cabo tareas de limpieza y preparación de datos para asegurar su calidad. Una vez procesado, el archivo fue cargado en una tabla del servicio BigQuery, lo que permitió ejecutar diversas consultas SQL para obtener insights relevantes.

Finalmente, se construyeron visualizaciones interactivas en Looker Studio, enfocadas en responder preguntas de negocio relacionadas con la frecuencia, ubicación y características de los avistamientos registrados. Este informe documenta cada una de las etapas del proceso, incluyendo capturas, consultas y análisis obtenidos, evidenciando el desarrollo de competencias clave en el manejo de tecnologías de Big Data.
## 🎯 Objetivo del Proyecto

El objetivo principal del proyecto es diseñar y ejecutar un proceso completo de análisis de datos en un entorno Big Data, utilizando herramientas de Google Cloud Platform, con el fin de gestionar, transformar y visualizar información proveniente de un conjunto de datos sobre avistamientos de OVNIs.

A través de este proyecto, se busca implementar un flujo de trabajo que incluya la ingesta de datos mediante Cloud Storage, la limpieza y transformación del archivo utilizando Cloud Dataprep, la carga estructurada en BigQuery y la creación de visualizaciones interactivas en Looker Studio. Todo esto con el propósito de obtener insights relevantes que permitan responder preguntas clave relacionadas con la frecuencia, características y ubicación de los avistamientos registrados.

## 📥 Origen y Carga de Datos

El conjunto de datos utilizado en este proyecto corresponde a un archivo denominado `ufo_sighting.csv`, el cual contiene registros históricos de avistamientos de objetos voladores no identificados (OVNIs). Este dataset incluye información relevante como fecha y hora del evento (`Date_time`), ubicación (`city`, `state/province`, `country`), características del objeto (`UFO_shape`), duración estimada del encuentro, descripción del suceso, y coordenadas geográficas (`latitude` y `longitude`), entre otros campos.

Como primer paso, el archivo fue almacenado en **Cloud Storage**, servicio que permitió crear un Data Lake accesible y escalable. Para ello, se creó un bucket denominado `nombre-alumno` (reemplazado por el nombre real del estudiante), donde se subió el archivo original en formato `.csv`.

Posteriormente, se conectó este archivo desde la herramienta **Cloud Dataprep (Trifacta)**, con el objetivo de revisar y preparar los datos antes de su análisis. Esta conexión facilitó el acceso al contenido del archivo para su posterior limpieza, validación y transformación, como parte del proceso de preparación de datos batch previo a su carga en BigQuery.

---

## 🧹 Limpieza con Dataprep

Para asegurar la calidad de los datos y permitir un análisis confiable, se realizó un proceso de limpieza utilizando la herramienta Cloud Dataprep (Trifacta). Dentro de este proceso:

- Se eliminaron filas que contenían valores nulos en columnas críticas como `Date_time`, `UFO_shape`, `state/province` y `country`, ya que dichos campos eran esenciales para el análisis posterior.  
- Se corrigieron los tipos de datos, asegurando que campos como `Date_time` fueran reconocidos como tipo fecha/hora y que los campos `latitude`, `longitude` y `length_of_encounter_seconds` fueran tratados como valores numéricos.
- Se filtraron los registros para conservar únicamente aquellos correspondientes a Estados Unidos (`country = 'us'`).
- Se estandarizaron nombres de columnas para evitar caracteres especiales o espacios que pudieran generar errores en BigQuery.  
  Por ejemplo:  
  - Se cambió el nombre de la columna `state/province` por `state_province`.

> ⚠️ También fue necesario asignar el rol de *Cloud Dataflow Service Agent* para poder ejecutar correctamente el procesamiento desde Dataprep a BigQuery.

## 💾 Consultas SQL destacadas

### 1. Top 5 formas de OVNIs más vistas
```sql
SELECT UFO_shape, COUNT(*) AS Sightings
FROM `proyecto.ovni.avistamientos`
WHERE country = 'us'
GROUP BY UFO_shape
ORDER BY Sightings DESC
LIMIT 5;
