# 🏛️ Reto 2 — Data warehouse con dbt

**Repositorio:** `sideproject-data-warehouse` · **Nivel:** intermedio · **Entorno:** PostgreSQL + dbt · **Método y evaluación:** [`README.md`](docs/METODO.md)

## El caso

La dirección de un banco pide cada lunes los mismos cinco números y cada lunes salen distintos, porque cada analista los calcula a su manera desde tablas operativas. Quieren **una sola versión de la verdad**: un data warehouse donde cada indicador esté definido una vez, probado y documentado.

## Lo que debe existir al terminar

- **Carga en estilo ELT:** los datos llegan crudos a un esquema `raw` y todas las transformaciones son SQL dentro de la base, gestionadas con dbt.
- **Un modelo dimensional:** al menos una tabla de hechos y tres dimensiones, con la granularidad escrita y justificada.
- **Historial de cambios** en una dimensión (un cliente que cambia de segmento no pierde su pasado).
- **Pruebas de datos** que fallen cuando deben: claves únicas, referencias válidas, valores permitidos y al menos una regla de negocio propia.
- **Documentación generada** con el linaje de cada tabla.
- **Un cuadro de mando en D3.js**, servido por una API, con los cinco indicadores de la dirección.

## Datos

`datos/`: 500 clientes y 800 cuentas en CSV, 60,000 movimientos de enero de 2024 a septiembre de 2026 en **Parquet** (`movimientos.parquet`) y `cambios_segmento.csv`, con el historial de 140 clientes que cambiaron de segmento. Ojo: `clientes.csv` solo guarda el segmento **vigente**; el pasado hay que reconstruirlo. Son datos sintéticos.

## Por qué PostgreSQL y no Redshift

El «Redshift» del emulador es PostgreSQL por dentro y no carga desde S3. Usar PostgreSQL directamente es más honesto, y lo que aprendes de modelado y de dbt sirve igual en Redshift, Snowflake o BigQuery.

## Restricciones

- Ninguna transformación en Python: si no es SQL en dbt, no cuenta.
- `dbt build` desde cero deja el warehouse completo y con todas las pruebas en verde.
- Cada indicador del cuadro de mando sale de **una** tabla del warehouse, sin cálculos en la API ni en el navegador.
- Relanzar la carga de un día no duplica hechos.

## Preguntas que tu diseño debe responder

- ¿Qué representa exactamente una fila de tu tabla de hechos?
- ¿Estrella o copo de nieve, y por qué?
- Una compra de 2025 de un cliente que hoy es «premium» y entonces era «básico»: ¿a qué segmento se suma?
- ¿Qué modelos son vistas, cuáles tablas y cuáles incrementales? ¿Por qué?

## Defensa: lo que te van a preguntar

- Rompe un dato en `raw` delante de nosotros. ¿Qué prueba lo detecta?
- Define «cliente activo». ¿Dónde vive esa definición y cuántas veces está escrita?
- ¿Qué harías distinto con mil millones de filas?

## Súbelo de nivel

- Ejecuta `dbt build` en cada pull request con GitHub Actions.
- Repite el modelo sobre el data lake del reto 1 (las tablas de negocio leídas con Athena).
- Conecta además una herramienta de BI de la maestría (Power BI o Tableau) y compara con tu tablero en D3.js: ¿qué gana y qué pierde cada una?
