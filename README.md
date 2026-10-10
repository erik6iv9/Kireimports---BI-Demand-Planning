<p align="center">
  <img src="img/logo.png" width="350" alt="Logo de Kireimports">
</p>

<br>
<h1>Descripción del Proyecto</h1>

KireImports es un proyecto de <ins>Business Intelligence & Demand Planning</ins> que nace a partir de problemáticas operativas comunes en las empresas comercializadoras. Su objetivo principal es demostrar cómo la transformación de datos operativos en información de valor puede facilitar el análisis, mejorar la visibilidad de los procesos y apoyar la toma de decisiones estratégicas.

El proyecto simula un entorno empresarial en el que los datos provenientes de diferentes procesos operativos son integrados, transformados y analizados para generar información útil sobre inventarios, ventas, compras y planeación de la demanda.

<h1>Problemática</h1>
<table>
  <tr>
    <td width="55%">
      <p>
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

De esta manera, la solución busca integrar capacidades propias de <ins>Business Intelligence, análisis de datos, gestión de inventarios, Supply Chain & Demand Planning</ins>, convirtiendo los datos generados por la operación en información útil para la planeación y la toma de decisiones.
      </p>
    </td>
    <td width="40%" align="center">
      <img src="img/kire.png" width="100%">
      <br>
      <sub>Base de datos operacional (OLTP)</sub>
    </td>
  </tr>
</table>

<h1>Stack tecnológico y conocimientos aplicados</h1>

Para llevar a cabo este proyecto, se integraron distintas herramientas y conocimientos técnicos, analíticos y de negocio de la siguiente manera:

<ins>SQL / SQL Server</ins>: Utilizado para gestionar y analizar la base de datos transaccional (OLTP), identificar entidades y relaciones entre las tablas operacionales, realizar consultas y transformaciones de datos, y <ins>diseñar el Data Warehouse mediante un modelo dimensional basado en Star Schema</ins>.

<ins>Python</ins>: Empleado para automatizar procesos <ins>ETL (Extracción, Transformación y Carga)</ins>, realizar la preparación y transformación de datos, trabajar con <ins>series temporales</ins>, analizar el comportamiento histórico de la demanda y desarrollar modelos de <ins>Forecasting</ins>, incluyendo la evaluación de los pronósticos.

<ins>Power BI</ins>: Conectado al Data Warehouse para realizar el modelado analítico y desarrollar <ins>dashboards</ins> orientados a la toma de decisiones, con énfasis en el análisis de inventarios, rotación, cobertura, clasificación ABC y <ins>abastecimiento/resurtido</ins>.

<ins>Excel</ins>: Utilizado como herramienta complementaria para la exploración inicial de los datos, validación de información y análisis preliminar.

<h1>Fases y desarrollo del proyecto</h1>

El desarrollo se estructuró de manera secuencial, siguiendo el flujo de transformación de datos operativos → información analítica → conocimiento para la toma de decisiones.

Definición del problema y objetivos del negocio

- Identificación de la problemática de negocio → [Ver documentación]
- Definición de objetivos y preguntas de negocio → [Ver documentación]
- Establecimiento de indicadores clave de desempeño (KPIs) → [Ver documentación]

Base de datos y fuentes de información

- Identificación de la base operacional (OLTP) → [Ver documentación](documentacion/oltp.md)
- Análisis de los esquemas y tablas operacionales → [Ver documentación](documentacion/esquemas-operacionales.md)
- Identificación de entidades, relaciones y datos disponibles → [Ver documentación](documentacion/datadiscovery.md)

Business Intelligence y modelado

- Diseño del Data Warehouse y modelo dimensional / Star Schema → [Ver documentación]
- Diseño y desarrollo del proceso ETL → [Ver documentación]
- Modelado de datos y desarrollo en Power BI → [Ver documentación]

Análisis de inventarios y Supply Chain

- Análisis de inventarios: rotación y cobertura → [Ver documentación]
- Clasificación ABC → [Ver documentación]
- Análisis de abastecimiento y resurtido → [Ver documentación]

Demand Planning

- Análisis histórico de la demanda → [Ver documentación]
- Preparación de series temporales → [Ver documentación]
- Modelado de Forecasting y evaluación de pronósticos → [Ver documentación]
