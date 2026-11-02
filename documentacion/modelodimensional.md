<h1>  Tablas de hechos y dimensiones  </h1>

Con el análisis de la base de datos operacional (OLTP) concluido, el desarrollo avanza hacia el diseño del modelo dimensional. Este modelo estrella (*Star Schema*) traduce las necesidades de negocio y las preguntas analíticas detectadas previamente en una estructura orientada a la consulta.   

Mientras que el OLTP prioriza la integridad transaccional, el modelo dimensional busca simplificar y acelerar el análisis de las ventas, la demanda, el inventario y el abastecimiento. Tomando como base las entidades del entorno operacional, se determinaron los procesos de negocio representados, la granularidad, las métricas y las dimensiones clave.   

Esta fase da soporte al flujo general del proyecto:

**OLTP → ETL → Data Warehouse → Power BI**

De este modo, el diseño dimensional establece las bases lógicas sobre las cuales se organizarán los datos que posteriormente poblarán el **Data Warehouse** y alimentarán los tableros de **Power BI**.

<h1>  Dimensiones  </h1>

A continuación, detallo las tablas de dimensiones que diseñé para enriquecer el modelo, definiendo los atributos clave que permitirán contextualizar el análisis sobre clientes, productos, proveedores y periodos de tiempo.

<table>
  <tr>
    <td width="35%" align="center">
      <img src="ruta/a/tu/imagen/DimItem.png" alt="DimItem Schema" width="100%">
    </td>
    <td width="65%" valign="top">

#### Propósito y Análisis Técnico

Diseñé **DimItem** para centralizar la información descriptiva de los productos y analizar ventas y demanda por categoría, empaque y proveedor.

* **Claves:** `ItemKey` actúa como clave sustituta (Surrogate Key) para el modelo analítico y `StockItemID` como clave de negocio para mantener trazabilidad con el OLTP.
* **Atributos y Jerarquías:** Incluye `StockItemName` y `StockGroupName` para agrupaciones en Power BI, junto con métricas de precios (`UnitPrice`) y empaques (`UnitPackage`, `QuantityPerOuter`).
* **Historial (SCD Tipo 2):** Incorporé `ValidFrom`, `ValidTo` e `IsCurrent` para registrar cambios en los atributos del producto a lo largo del tiempo sin alterar el contexto histórico de las ventas.

    </td>
  </tr>
</table>
