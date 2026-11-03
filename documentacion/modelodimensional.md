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

Diseñé **DimItem** para centralizar el catálogo de productos dentro del Data Warehouse, proporcionando una estructura que permita analizar las ventas, el comportamiento de la demanda y el desempeño del inventario desde diferentes perspectivas, como categoría, empaque, precio y proveedor.

- **Claves y Relaciones:** Utilizo `ItemKey` como clave sustituta (*Surrogate Key*) para identificar cada registro dentro del Data Warehouse, mientras que `StockItemID` conserva la referencia a la clave de negocio del sistema OLTP. Además, incorporo `SupplierKey` para establecer la relación con la dimensión de proveedores (`DimSupplier`).

- **Atributos y Jerarquías:** Incluye atributos descriptivos como `StockItemName` y `StockGroupName`, que permiten identificar, clasificar y agrupar los productos en Power BI. También contempla información comercial, como `UnitPrice` y `RecommendedRetailPrice`, así como especificaciones de empaque mediante `UnitPackage`, `OuterPackage` y `QuantityPerOuter`. Estos atributos permiten enriquecer los análisis de ventas, demanda y abastecimiento.

- **Gestión del Historial (SCD Tipo 2):** Implementé una estrategia de *Slowly Changing Dimension Tipo 2* mediante los campos `ValidFrom`, `ValidTo` e `IsCurrent`. Esto permite conservar las distintas versiones de un producto cuando cambian determinados atributos, como su precio o clasificación, manteniendo la trazabilidad histórica y evitando sobrescribir información necesaria para los análisis temporales.

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

Diseñé **DimCustomer** para consolidar el perfil de los compradores dentro del Data Warehouse, proporcionando una estructura que permita analizar el comportamiento de la demanda desde diferentes perspectivas, como el tipo de cliente y su ubicación geográfica.

- **Claves y Relaciones:** Utilizo `CustomerKey` como clave sustituta (*Surrogate Key*) para identificar cada registro dentro del Data Warehouse, mientras que `CustomerID` conserva la referencia a la clave de negocio del sistema OLTP, manteniendo la trazabilidad entre ambos sistemas.

- **Segmentación y Geografía:** Incluye atributos descriptivos como `CustomerName` y `CustomerCategoryName`, que permiten identificar y segmentar a los clientes según su categoría. Además, incorpora información geográfica mediante `DeliveryCityName`, `StateProvinceName` y `CountryName`, facilitando el análisis de ventas, distribución territorial y comportamiento de la demanda por región.

- **Gestión del Historial (SCD Tipo 2):** Implementé una estrategia de *Slowly Changing Dimension Tipo 2* mediante los campos `ValidFrom`, `ValidTo` e `IsCurrent`. Esto permite conservar las distintas versiones de un cliente cuando cambian determinados atributos, como su categoría o ubicación geográfica, manteniendo la trazabilidad histórica y evitando sobrescribir información necesaria para los análisis temporales.

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

Diseñé **DimSupplier** para centralizar la información de los proveedores dentro del Data Warehouse, proporcionando una estructura que permita analizar la cadena de suministro, los tiempos de entrega y la procedencia geográfica de las compras, facilitando la evaluación y el seguimiento de las relaciones comerciales con los proveedores.

- **Claves y Relaciones:** Utilizo `SupplierKey` como clave sustituta (*Surrogate Key*) para identificar cada registro dentro del Data Warehouse, mientras que `SupplierID` conserva la referencia a la clave de negocio del sistema OLTP, manteniendo la trazabilidad entre ambos sistemas.

- **Atributos y Geografía:** Incluye atributos descriptivos como `SupplierName` y `SupplierCategoryName`, que permiten identificar y agrupar a los proveedores según su categoría. Además, incorpora información geográfica mediante `DeliveryCityName`, `StateProvinceName` y `CountryName`, estableciendo una jerarquía territorial que facilita el análisis de la procedencia de las compras y la distribución logística.

- **Gestión del Historial (SCD Tipo 2):** Implementé una estrategia de *Slowly Changing Dimension Tipo 2* mediante los campos `ValidFrom`, `ValidTo` e `IsCurrent`. Esto permite conservar las distintas versiones de un proveedor cuando cambian determinados atributos, como su categoría o ubicación geográfica, manteniendo la trazabilidad histórica y evitando sobrescribir información necesaria para los análisis temporales.
    </td>
  </tr>
</table>
