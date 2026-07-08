# 🚀 AWS Glue Data Pipeline Workshop

Este repositorio documenta y almacena la resolución del **Workshop de AWS Glue, Catálogos y Crawlers**. El objetivo principal de este proyecto es construir un pipeline de datos *end-to-end* en la nube, pasando por la ingesta, catalogación, transformación y validación de datos utilizando el ecosistema de Amazon Web Services.

---

## 📖 Acerca del Workshop

El proyecto simula un escenario real de Data Engineering donde se debe procesar un conjunto de datos crudos (schema-on-read) y transformarlo para que esté listo para su análisis en una capa curada. 

**Servicios de AWS utilizados:**
* **Amazon S3:** Almacenamiento de las capas `raw`, `curated` y resultados de consultas.
* **AWS Glue Data Catalog:** Repositorio central de metadatos.
* **AWS Glue Crawlers:** Escaneo automático e inferencia de esquemas.
* **AWS Glue ETL (Spark):** Transformación, limpieza y casteo de datos.
* **Amazon Athena:** Exploración y validación mediante consultas SQL estándar.

---

## 🎯 Consigna del Workshop

La misión principal consistió en:
1.  Ingerir un dataset público en una capa `raw` de S3.
2.  Catalogar su esquema automáticamente utilizando un Crawler.
3.  Desarrollar un job ETL en Glue para limpiar errores de tipos de datos, renombrar columnas, eliminar duplicados y escribir el resultado en formato particionado Parquet en una capa `curated`.
4.  Validar el resultado final con Amazon Athena para asegurar la calidad de la información y documentar todo el flujo.

Para leer las instrucciones originales completas del workshop, puedes consultar el archivo de la consigna.

---

## 📂 Estructura del Repositorio y Rutas Relativas

Este repositorio está organizado para separar la documentación de los archivos técnicos. A continuación se detalla el árbol de directorios y cómo navegar por él:

* `./` **(Raíz del repositorio)**
    * [`README.md`](./README.md): Este archivo de presentación.
    * [`PIPELINE_REPORT.md`](./PIPELINE_REPORT.md): El entregable final detallado.
* `./src/` **(Código fuente y datos)**
    * [`./src/dataset/`](./src/dataset/): Contiene el archivo CSV original utilizado en la capa RAW.
    * [`./src/scripts/`](./src/scripts/): (Opcional) Contiene los scripts de Python/Spark generados por AWS Glue ETL.
* `./assets/` **(Recursos visuales)**
    * [`./assets/`](./assets/): Contiene todas las capturas de pantalla de la arquitectura, configuración de nodos de Glue, y consultas de validación en Athena referenciadas en el reporte.

---

## 📄 El Reporte Final

El núcleo de la entrega se encuentra en el archivo [`PIPELINE_REPORT.md`](./PIPELINE_REPORT.md). Este documento (redactado en inglés) detalla paso a paso las decisiones técnicas tomadas durante el proyecto. Incluye:

* **Architecture:** Descripción del flujo S3 -> Crawler -> Job -> Athena.
* **Data Dictionary:** Explicación del esquema final tras la limpieza.
* **Transformations:** Detalle de los casteos y renombre de columnas aplicados para solucionar inconsistencias del origen.
* **Validation:** Evidencia de los resultados consultados, comparativas de volumen de datos escaneados y reducción de filas por la deduplicación.

---

## 💻 Código y Dataset (`src`)

Dentro del directorio [`src/`](./src/), se alojan los elementos técnicos que permiten reproducir este workshop:
* **Dataset:** Se utilizó el relevamiento muestral de avisos publicados para la venta de locales en la Ciudad Autónoma de Buenos Aires correspondiente al año 2020 (`locales-en-venta-2020.csv`).
* **Scripts:** Se incluye el código generado por la herramienta Visual ETL de AWS Glue, el cual contiene las transformaciones en Apache Spark (`Drop Duplicates`, `Apply Mapping` para casteo, etc.).