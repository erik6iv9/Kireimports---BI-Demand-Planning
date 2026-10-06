Descripción del Proyecto

KireImports es un proyecto de Business Intelligence que nace a partir de problemáticas operativas comunes en las empresas comercializadoras. Su objetivo principal es demostrar cómo la transformación de datos operativos en información de valor puede facilitar el análisis, mejorar la visibilidad de los procesos y apoyar la toma de decisiones estratégicas.

El proyecto simula un entorno empresarial en el que los datos provenientes de diferentes procesos operativos son integrados, transformados y analizados para generar información útil sobre inventarios, ventas, compras y planeación de la demanda.

Problemática

KireImports presenta dificultades para llevar una planeación eficiente de sus operaciones, debido a que la información de ventas, compras e inventarios se encuentra concentrada en una misma base de datos operacional.

Aunque esta base permite registrar y gestionar las transacciones diarias del negocio, utilizarla directamente para realizar análisis dificulta la consulta, integración y estandarización de la información necesaria para evaluar el desempeño de las operaciones.

Como consecuencia, obtener una visión integral del comportamiento del inventario, las ventas, las compras y la demanda requiere realizar análisis sobre estructuras diseñadas principalmente para la operación, lo que limita la generación de información consistente y oportuna para la toma de decisiones.

Ante esta problemática, KireImports requiere una solución de Business Intelligence que permita transformar los datos operativos en información estructurada, estandarizada y orientada al análisis, con el objetivo de:

- Analizar y controlar inventarios, identificando niveles de stock, rotación, cobertura y posibles excesos o faltantes.
- Analizar la demanda y desarrollar pronósticos, utilizando el comportamiento histórico de las ventas como apoyo para la planeación.
- Apoyar la planeación de abastecimiento y resurtido, facilitando la identificación de necesidades de compra y reposición.
- Analizar el desempeño de compras y ventas, permitiendo evaluar tendencias y comportamiento de los productos.
- Construir indicadores y dashboards de gestión, que proporcionen información oportuna para la toma de decisiones.
- Detectar desviaciones y oportunidades de mejora, mediante el análisis sistemático de los datos operativos.

De esta manera, la solución busca integrar capacidades propias de Business Intelligence, análisis de datos, gestión de inventarios, Supply Chain y Demand Planning, convirtiendo los datos generados por la operación en información útil para la planeación y la toma de decisiones.



El área de negocio necesita responder preguntas como:

- ¿Qué productos tienen mayor demanda?
- ¿Cómo está evolucionando la demanda?
- ¿Qué productos tienen exceso de inventario?
- ¿Qué productos presentan baja cobertura?
- ¿Qué productos requieren mayor atención de abastecimiento?
- ¿Qué productos podrían presentar riesgo de agotamiento?
- ¿Cómo se comporta la demanda históricamente?
- ¿Es posible estimar la demanda futura de determinados productos?

KireImports busca responder estas preguntas mediante un flujo de análisis estructurado.

---

2. Enfoque del proyecto

El proyecto sigue una metodología basada en:

Problema de negocio
        ↓
Preguntas de negocio
        ↓
KPIs y métricas
        ↓
Identificación de datos necesarios
        ↓
Modelado dimensional
        ↓
Data Warehouse
        ↓
Análisis en Power BI
        ↓
Identificación de patrones y problemas
        ↓
Forecasting de demanda
        ↓
Interpretación
        ↓
Recomendaciones para abastecimiento

De esta manera, cada componente técnico responde a una necesidad concreta del negocio.

---

3. Objetivo

El objetivo de KireImports es desarrollar una solución analítica que permita transformar datos operativos en información útil para:

- Analizar inventarios.
- Analizar ventas y comportamiento de la demanda.
- Analizar compras y proveedores.
- Identificar productos que requieren atención.
- Evaluar el comportamiento histórico de la demanda.
- Aplicar técnicas de forecasting.
- Comparar la demanda proyectada con la situación actual del inventario.
- Apoyar decisiones relacionadas con resurtido y abastecimiento.

---

4. Perspectiva de Business Intelligence

Desde la perspectiva de Business Intelligence, KireImports demuestra el desarrollo de una solución de datos de principio a fin.

El proyecto contempla:

- Análisis de una fuente OLTP.
- Identificación de procesos de negocio.
- Definición del grain de los procesos.
- Diseño de un modelo dimensional.
- Construcción de un Data Warehouse.
- Procesos de transformación y carga mediante SQL.
- Creación de métricas y KPIs.
- Desarrollo de dashboards en Power BI.
- Análisis de información desde diferentes dimensiones.

El objetivo es convertir datos transaccionales en información estructurada para facilitar el análisis y la toma de decisiones.

---

5. Perspectiva de Planeación de la Demanda

KireImports también incorpora un componente de análisis y forecasting de demanda.

A partir del historial de ventas se estudia el comportamiento de determinados productos para identificar:

- Tendencia.
- Variabilidad.
- Estacionalidad.
- Patrones recurrentes.
- Cambios en el comportamiento de la demanda.

Posteriormente se aplican modelos de forecasting y se evalúa su desempeño mediante métricas de error.

El forecast no se considera un resultado aislado.

La intención es utilizarlo como una herramienta adicional para responder una pregunta de negocio:

«¿Cómo podría comportarse la demanda futura y qué implicaciones podría tener para el inventario y el abastecimiento?»

---

6. Resultado esperado

El resultado final es una solución que conecta el análisis histórico con la planeación:

Ventas históricas
       ↓
Análisis de demanda
       ↓
Forecast
       ↓
Demanda futura estimada
       ↓
Comparación con inventario
       ↓
Identificación de posibles riesgos
       ↓
Apoyo a decisiones de abastecimiento

Por lo tanto, KireImports no busca únicamente mostrar qué ocurrió, sino avanzar progresivamente hacia:

«¿Qué está ocurriendo? → ¿Por qué podría estar ocurriendo? → ¿Qué podría ocurrir? → ¿Qué deberíamos considerar hacer?»

---

7. Tecnologías

Área| Tecnologías
Base de datos| SQL Server
Consulta y transformación| SQL
Data Warehouse| Modelado dimensional / Star Schema
Business Intelligence| Power BI / DAX
Análisis y automatización| Python
Forecasting| Python / Statsmodels
Control de versiones| Git / GitHub

---

8. Alcance

KireImports se desarrolla como un proyecto de portafolio con un enfoque práctico y orientado a negocio.

El proyecto busca demostrar la integración de conocimientos de:

BI + Data Warehousing + SQL + análisis de datos + forecasting + planeación de inventarios y abastecimiento.

La solución se desarrolla de manera incremental, permitiendo incorporar posteriormente procesos y capacidades adicionales de ingeniería de datos y analítica avanzada.
