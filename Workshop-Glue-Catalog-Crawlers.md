# Workshop · AWS Glue · Catálogos y Crawlers

## Objetivos

1. **Pipeline end-to-end:** construir un pipeline de datos de punta a punta — ingerir un dataset en un data lake, catalogar su esquema automáticamente, transformarlo en una capa `curated` lista para analizar, validar el resultado con consultas SQL y dejar el flujo documentado en inglés para que otro equipo pueda entenderlo y reproducirlo.
2. **Servicios AWS:** conocer y aplicar en soluciones prácticas **Amazon S3**, **AWS Glue Data Catalog**, **Glue Crawlers**, **Glue ETL Jobs** y **Amazon Athena**.

## Antes de empezar

- Tené acceso a la consola de AWS y revisá los límites del Free Tier (Glue cobra por tiempo de ejecución del job; usá jobs cortos y borrá lo que no uses al terminar).
- Elegí un dataset de BA Data ([https://data.buenosaires.gob.ar/dataset/](https://data.buenosaires.gob.ar/dataset/)) y subilo a un bucket nuevo.
- Creá una carpeta de proyecto para tu entregable: un único `.md` en inglés con capturas de pantalla (y bloques de código si hace falta). El trabajo práctico se hace en la consola y en jobs de Glue — no hace falta notebook.
- Tiempo estimado: 3 días. Priorizá un pipeline simple que funcione antes de agregar complejidad.

⸻

## 1. Planificación express

- Elegí el dataset y anotá: URL, formato, tamaño aproximado y 3–5 campos clave.
- Definí un nombre único para tu bucket y una estructura mínima: `raw/`, `curated/` y `athena-results/`. Podés agregar `analytics/` si te alcanza el tiempo.
- Subí el dataset en una carpeta dedicada bajo `raw/` (ej.: `raw/<nombre-del-dataset>/`).
- Pensá el particionado desde el día 0 (ej.: `year=2025/month=06/`). Sin particiones, Athena lee todo y tu billetera llora.
- Definí tags para el bucket y los objetos (`owner`, `env`, `dataset`, `layer`).

⸻

## 2. Bucket y carga RAW (desde cero)

- Creá un bucket S3 nuevo en tu región (con configuración por default).
- Creá las carpetas `raw/`, `curated/` y `athena-results/`.
- Subí el dataset sin transformar a `raw/<nombre-del-dataset>/` usando un nombre de archivo en minúscula, sin espacios y descriptivo.
- Si el dataset es CSV/JSON, dejalo como llegó. Si es grande, podés subir una muestra representativa (documentá el criterio en tu reporte).
- Verificá en consola: ruta, tamaño total y tags aplicados (`layer=raw`).

⸻

## 3. Glue Data Catalog y Crawler

- Creá una base de datos en Glue Data Catalog (ej.: `workshop_glue_<tu_nombre>`).
- Creá un Crawler con esta configuración:
  - Data source: `s3://<tu-bucket>/raw/`
  - Target database: tu base Glue
  - Frecuencia: manual (On demand)
- Ejecutalo y validá que se cree la tabla esperada en el catálogo.
- Revisá tipos inferidos: ¿fechas quedaron como string? ¿decimales como double? El crawler infiere, no garantiza — validá antes de confiar en el esquema.
- Recordá: almacenar en S3 ≠ catalogar. Los archivos viven en S3; el catálogo guarda metadatos (nombre de tabla, columnas, tipos, ubicación). Athena necesita ambos.

⸻

## 4. Explorar con Athena (y no pagar de más)

- Configurá Athena con resultados en `s3://<tu-bucket>/athena-results/`.
- Seleccioná tu base Glue y corré 2–3 consultas sobre tu tabla: COUNT, GROUP BY y un filtro por fecha o dimensión de negocio.
- Compará una query con filtro de partición vs una sin filtro (si particionaste). Anotá la diferencia de datos escaneados que muestra Athena y explicá: Por que importa? Hubo diferencias significativas? A que se debe?
- Athena lee directamente desde S3 usando el catálogo — no copia los datos a otro lugar. Eso es schema-on-read en la práctica.

⸻

## 5. Job de Glue ETL: raw → curated

- Creá un job de Glue que lea desde `raw/` y escriba en `curated/` en formato Parquet.
- Transformaciones mínimas (elegí al menos 3): renombrar columnas, castear tipos, filtrar filas inválidas, eliminar duplicados o agregar una columna derivada simple.
- Corregí en el ETL lo que el crawler inferió mal (ej.: castear string a date).
- Usá **Python**: el editor visual de Glue Studio o un job **Python Shell** con un script breve. Lo importante es que el resultado sea reproducible.
- Ejecutá el job, verificá que `curated/` tenga archivos Parquet y tamaño razonable.

⸻

## 6. Documentación y entrega (`PIPELINE_REPORT.md`)

Un solo archivo en inglés — **`PIPELINE_REPORT.md`** — con capturas de pantalla y, si aplica, fragmentos de código en bloques markdown (script del job Glue, queries Athena, etc.). Debe incluir al menos:

- **Overview** — qué hace el pipeline en 2–3 oraciones.
- **Architecture** — diagrama o descripción del flujo (bucket, crawler, catálogo, job, capas). Incluí capturas de la consola donde ayuden (S3, Glue, Athena).
- **Data sources** — URL del dataset, licencia y supuestos de la muestra (si usaste un subset).
- **Data dictionary** — tabla curated: campo → type → description.
- **Transformations** — qué hace el job ETL (mínimo 3 transformaciones). Podés pegar el script o un extracto relevante.
- **Validation** — resultados de Athena sobre raw y curated (queries + capturas o output). Compará conteos y explicá diferencias (filtros, deduplicación, etc.).
- **How to run** — pasos para re-ejecutar crawler y job (sin credenciales en el texto).
- **Summary (español)** — qué hicieron, decisiones, supuestos y cómo reproducir.
- **Cost notes** — qué recursos borrar al terminar.

Revisá ortografía y que un compañero que no estuvo en la clase pueda entenderlo solo con ese archivo.

⸻

## 7. Cierre y validación

- Volvé a correr un Crawler sobre `curated/` (o actualizá el catálogo) y validá con Athena.
- Completá la sección **Validation** del reporte con evidencia (capturas, conteos, queries).
- Deberías poder explicar:
  - Qué rol cumple S3, qué resuelve el catálogo, para qué sirve el crawler.
  - Cómo Athena consulta sin mover datos y qué agrega el job ETL al flujo.
- Subí o entregá según indique tu equipo: **`PIPELINE_REPORT.md`** 

⸻

## ★ Limpieza (importante para no gastar)

- Detené y borrá los jobs de Glue que no uses.
- Eliminá crawlers y tablas de prueba del Data Catalog si no los necesitás.
- Vaciá y borrá el bucket S3 del workshop (incluidas versiones si activaste versioning).
- Revisá en Cost Explorer que no queden recursos colgando.