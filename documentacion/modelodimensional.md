<h1>  Tablas de hechos y dimensiones  </h1>

Con el análisis de la base de datos operacional (OLTP) concluido, el desarrollo avanza hacia el diseño del modelo dimensional. Este modelo estrella (*Star Schema*) traduce las necesidades de negocio y las preguntas analíticas detectadas previamente en una estructura orientada a la consulta.   

Mientras que el OLTP prioriza la integridad transaccional, el modelo dimensional busca simplificar y acelerar el análisis de las ventas, la demanda, el inventario y el abastecimiento. Tomando como base las entidades del entorno operacional, se determinaron los procesos de negocio representados, la granularidad, las métricas y las dimensiones clave.   

Esta fase da soporte al flujo general del proyecto:

**OLTP → ETL → Data Warehouse → Power BI**

De este modo, el diseño dimensional establece las bases lógicas sobre las cuales se organizarán los datos que posteriormente poblarán el **Data Warehouse** y alimentarán los tableros de **Power BI**.

<h1>  Dimensiones  </h1>

A continuación, detallo las tablas de dimensiones que diseñé para enriquecer el modelo, definiendo los atributos clave que permitirán contextualizar el análisis sobre clientes, productos, proveedores y periodos de tiempo.

### DimItem (Dimensión de Productos)

<table>
  <tr>
    <td width="35%" align="center">
      <img src="../img/DimItem.png" alt="DimItem" width="100%">
    </td>
    <td width="65%" valign="top">

#### Propósito y Análisis Técnico

Diseñé **DimItem** para centralizar el catálogo de productos y analizar las ventas y la demanda por categoría, empaque, precio y proveedor.

* **Claves y Relaciones:** Utilizo `ItemKey` como clave sustituta (Surrogate Key) para el Data Warehouse, `StockItemID` como clave de negocio con el OLTP y `SupplierKey` para vincular directamente con la dimensión de proveedores.
* **Atributos y Jerarquías:** Incluye `StockItemName` y `StockGroupName` para agrupaciones en Power BI, además de precios (`UnitPrice`, `RecommendedRetailPrice`) y especificaciones de empaque (`UnitPackage`, `OuterPackage`, `QuantityPerOuter`).
* **Historial (SCD Tipo 2):** Incorporé `ValidFrom`, `ValidTo` e `IsCurrent` para registrar cambios en los precios o categorías del producto sin alterar el historial analítico.

    </td>
  </tr>
</table>

### DimCustomer (Dimensión de Clientes)

<table>
  <tr>
    <td width="35%" align="center">
      <img src="../img/DimCustomer.png" alt="DimCustomer" width="100%">
    </td>
    <td width="65%" valign="top">

#### Propósito y Análisis Técnico

Diseñé **DimCustomer** para consolidar el perfil de los compradores y permitir el análisis del comportamiento de la demanda por tipo de cliente y zona geográfica.

* **Claves:** Utilizo `CustomerKey` como clave sustituta (Surrogate Key) para el Data Warehouse y `CustomerID` como clave de negocio para vincular los datos con el OLTP.
* **Segmentación y Geografía:** Incluye `CustomerName` y `CustomerCategory` para categorizar la cartera de clientes, además de `Country`, `State` y `City` para análisis de distribución territorial.
* **Historial (SCD Tipo 2):** Incorporé `ValidFrom`, `ValidTo` e `IsCurrent` para rastrear cambios en la ubicación o categoría del cliente en el tiempo sin distorsionar el historial de ventas.

    </td>
  </tr>
</table>

### DimSupplier (Dimensión de Proveedores)

<table>
  <tr>
    <td width="35%" align="center">
      <img src="../img/DimSupplier.png" alt="DimSupplier Schema" width="100%">
    </td>
    <td width="65%" valign="top">

#### Propósito y Análisis Técnico

Diseñé **DimSupplier** para gestionar la información de los proveedores y facilitar el análisis de la cadena de suministro, tiempos de entrega y procedencia de las compras de importación.

* **Claves:** Utilizo `SupplierKey` como clave sustituta (Surrogate Key) para el Data Warehouse y `SupplierID` como clave de negocio para mantener la trazabilidad con el sistema OLTP.
* **Atributos y Geografía:** Incluye el nombre del proveedor (`SupplierName`), su categoría (`SupplierCategory`), método de entrega (`DeliveryMethod`) y ubicación (`StateProvince`, `Country`) para evaluar el rendimiento logístico por región.
* **Historial (SCD Tipo 2):** Incorporé `ValidFrom`, `ValidTo` e `IsCurrent` para registrar cambios en los datos del proveedor a lo largo del tiempo sin distorsionar el historial de compras y abastecimiento.

    </td>
  </tr>
</table>
