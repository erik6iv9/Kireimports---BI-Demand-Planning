<h1> Diseño del modelo estrella </h1>

Una vez realizado el análisis y descubrimiento de la base de datos operacional (OLTP), continúo con el diseño del modelo dimensional que utilizaré como base para la solución de **Business Intelligence y Demand Planning**.

El objetivo de esta etapa es diseñar un **modelo estrella (Star Schema)** que me permita organizar la información operacional de acuerdo con las necesidades de análisis identificadas durante la definición de la problemática y las preguntas de negocio. Este diseño servirá como referencia para determinar cómo estructuraré la información que posteriormente será almacenada en el **Data Warehouse**.

A diferencia del modelo operacional, cuyo propósito principal es registrar y gestionar las transacciones del negocio, mi modelo dimensional estará orientado al análisis y consulta de la información. Con este diseño busco facilitar el análisis de las ventas, el comportamiento de la demanda, el desempeño del inventario y los procesos relacionados con el abastecimiento.

Para construir el modelo partiré de las entidades, relaciones y datos que identifiqué durante el análisis del OLTP. A partir de estos elementos determinaré los procesos de negocio que serán representados, la granularidad de cada proceso, las tablas de hechos, las dimensiones y las métricas necesarias para responder a las preguntas de negocio planteadas.

Una vez definido y validado el modelo estrella, utilizaré este diseño como base para la **construcción del Data Warehouse**. Posteriormente, desarrollaré el proceso **ETL**, mediante el cual extraeré la información de las fuentes operacionales, realizaré las transformaciones necesarias y cargaré los datos en las estructuras definidas en el Data Warehouse.

Finalmente, conectaré el **Data Warehouse con Power BI**, donde desarrollaré el modelo de datos, las medidas, indicadores y visualizaciones necesarias para analizar la información y apoyar la toma de decisiones relacionadas con el abastecimiento, inventario y demanda.

De esta manera, la arquitectura que planteo para el proyecto sigue el siguiente flujo:

**OLTP → ETL → Data Warehouse → Power BI**

Dentro de esta arquitectura, el **modelo estrella representa el diseño dimensional** que utilizaré para definir cómo se organizará la información analítica que posteriormente implementaré dentro del Data Warehouse.
