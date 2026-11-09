<h1>Modelado por Procesos de Negocio e Integración del Modelo Dimensional (Constelación) del Data Warehouse</h1>

Esta sección documenta el diseño del modelo dimensional del Data Warehouse, tomando como referencia los diferentes procesos de negocio. Mediante un enfoque de modelado analítico, se representa cada proceso de manera individual a través de su correspondiente esquema estrella, definiendo su granularidad, tablas de hechos, dimensiones, relaciones y métricas de análisis.

Estas representaciones constituyen la base del diseño dimensional del Data Warehouse, cuya arquitectura estará conformada por un modelo constelación, que integrará los diferentes procesos de negocio mediante dimensiones conformadas. De esta manera, se busca proporcionar una visión analítica unificada de las operaciones, facilitando el análisis, el seguimiento de indicadores clave y la toma de decisiones mediante herramientas de Business Intelligence.

Finalmente, al término de este documento se presenta el diseño dimensional integrado del Data Warehouse, representado mediante el modelo constelación que reúne los diferentes procesos de negocio en una arquitectura analítica unificada.

# Modelo Estrella: Control y Seguimiento de Inventario

<p align="center">
  <img src="../img/StarStockHoldings.png" width="800" alt="startesquemainventario">
</p>

## 1\. Descripción General

Este modelo en estrella (*Star Schema*) está diseñado para el análisis cuantitativo, seguimiento y valoración periódica de los niveles de inventario.

Implementa un patrón de hechos de instantánea periódica (*Periodic Snapshot Fact Table*), que registra diariamente el estado de las existencias por producto. Esto permite conservar un historial de inventario y analizar su evolución a lo largo del tiempo, facilitando el monitoreo de disponibilidad, cumplimiento de niveles objetivo, necesidades de abastecimiento y valoración del inventario.

## 2\. Arquitectura del Modelo

-   **Grano del Modelo:** Un registro representa un producto (`ItemKey`) en una fecha específica de captura diaria (`DateKey`).
-   **Tipo de Esquema:** Modelo estrella (*Star Schema*).
-   **Tabla de Hechos:** `FactStockHoldings`.
-   **Dimensiones:** `DimItem` (Productos) y `DimDate` (Tiempo).
-   **Tipo de Hecho:** Fotografía periódica (*Periodic Snapshot*).

### Integridad y granularidad

-   Se establece una restricción de unicidad compuesta sobre `ItemKey` y `DateKey`, garantizando que cada producto tenga un único registro por fecha de captura.
-   La tabla conserva históricamente las fotografías diarias, sin sobrescribir los registros de períodos anteriores.

## 3\. Métricas y Atributos de Análisis

-   `QuantityOnHand`: Cantidad de unidades disponibles en inventario al momento de la captura.
-   `LastCostPrice`: Último costo unitario registrado para el producto.
-   `LastStocktakeQuantity`: Cantidad registrada en el último conteo físico de inventario.
-   `TargetStockLevel`: Nivel objetivo de existencias establecido para el producto.

## 4\. Casos de Uso del Negocio

1.  **Valoración de Inventario:** Cálculo del valor estimado de las existencias en bodega mediante la multiplicación de QuantityOnHand x LastCostPrice, permitiendo analizar su evolución y valor en diferentes fechas de corte.
2.  **Control de Abastecimiento:** Monitoreo del cumplimiento de los niveles objetivo de inventario QuantityOnHand vs TargetStockLevel, identificando productos con existencias inferiores a los niveles establecidos.
3.  **Auditoría y Control de Inventario:** Comparación entre las existencias registradas en el sistema y las cantidades reportadas en el último conteo físico (`LastStocktakeQuantity`), facilitando la identificación de posibles diferencias.
4.  **Análisis de Tendencias:** Evaluación de la evolución diaria, semanal y mensual de las existencias, identificando patrones de acumulación, reducción y variaciones del inventario.
5.  **Análisis de Cobertura y Disponibilidad:** Seguimiento de los niveles de existencias por producto y período, proporcionando información para evaluar la disponibilidad de inventario y apoyar las decisiones de abastecimiento.

## 5\. Consideraciones Analíticas

Las métricas de inventario requieren un tratamiento específico durante la agregación:

-   `QuantityOnHand` es una métrica **semi-aditiva**: puede agregarse entre productos para una misma fecha, pero no debe sumarse entre diferentes fechas.
-   `LastCostPrice` y `TargetStockLevel` son atributos **no aditivos**.
-   La valoración del inventario debe calcularse considerando las cantidades y los costos correspondientes a cada producto en la fecha analizada.

Estas consideraciones permiten construir indicadores consistentes y evitar errores de agregación en las herramientas de análisis, especialmente en Power BI.



# Modelo Estrella: Transaccion de Inventario

<p align="center">
  <img src="../img/StartStockItemTransactions.png" width="800" alt="startesquematransaccion">
</p>


## 1\. Descripción General

Este modelo en estrella (*Star Schema*) está diseñado para el análisis detallado y transaccional de los movimientos de inventario.

Implementa un patrón de **hechos de transacciones (*****Transaction Fact Table*****)**, el cual registra cada evento o movimiento individual que afecta el inventario (como entradas por compras, salidas por ventas, ajustes o devoluciones). A diferencia de las tablas de instantáneas, este esquema permite analizar la dinámica operativa, el volumen de operaciones y la trazabilidad completa de los productos a medida que interactúan con clientes y proveedores a lo largo del tiempo.

## 2\. Arquitectura del Modelo

-   **Grano del Modelo:** Un registro representa una transacción individual de inventario (`StockItemTransactionKey`) asociada a un producto específico, un momento en el tiempo, y opcionalmente a un cliente o proveedor.
-   **Tipo de Esquema:** Modelo estrella (*Star Schema*).
-   **Tabla de Hechos:** `FactStockItemTransactions`.
-   **Dimensiones:**
-   -   `DimItem` (Productos)
    -   `DimSupplier` (Proveedores)
    -   `DimCustomer` (Clientes)
    -   `DimDate` (Tiempo)
-   **Tipo de Hecho:** Transaccional (*Transactional Fact Table*).

### Integridad y granularidad

-   Cada fila corresponde a un movimiento o evento discreto en el sistema transaccional.
-   Las dimensiones integran control de cambios históricos mediante **SCD Tipo 2** (`ValidFrom`, `ValidTo`, `IsCurrent`), asegurando que las transacciones históricas conserven los atributos vigentes del producto, cliente o proveedor en el momento en que ocurrió el evento.

## 3\. Métricas y Atributos de Análisis

-   **Métrica Cuantitativa:**
-   -   `Quantity`: Cantidad de unidades involucradas en la transacción (positiva para entradas/ingresos, negativa o dirigida según el tipo de movimiento).
-   **Atributos Descriptivos y de Control:**
-   -   `TransactionType`: Tipo de movimiento (por ejemplo, venta, compra, ajuste de inventario).
    -   `PurchaseOrder`: Número de orden de compra asociado (si aplica).
    -   `Invoice`: Número de factura comercial asociada (si aplica).
    -   `TransactionOccurredWhen`: Marca de tiempo exacta en que ocurrió la transacción en el sistema fuente.

## 4\. Casos de Uso del Negocio

1.  **Análisis de Flujo de Inventario:** Monitoreo detallado de entradas y salidas de mercancía para entender la velocidad de rotación y el comportamiento operativo del almacén.
2.  **Trazabilidad de Ventas y Compras:** Cruce directo entre el movimiento de stock y los actores involucrados (`DimCustomer` y `DimSupplier`), permitiendo auditar qué cliente adquirió qué producto o qué proveedor lo suministró.
3.  **Auditoría de Documentos Comerciales:** Seguimiento de operaciones respaldadas por órdenes de compra e facturas (`PurchaseOrder`, `Invoice`) para conciliar inventarios físicos con registros administrativos.
4.  **Análisis de Patrones Operativos por Período:** Evaluación de volúmenes de transacciones agrupados por días hábiles, fines de semana o cierres mensuales utilizando la robusta estructura de `DimDate`.

## 5\. Consideraciones Analíticas

-   `Quantity` es una métrica **completamente aditiva**: puede sumarse a través de cualquier dimensión (por producto, tipo de transacción, cliente, proveedor o período de tiempo) sin restricciones.
-   Permite complementar los análisis de saldo estático (*Stock Holdings*) con la dinámica real de flujo de mercancías.



# Modelo Estrella: Demanda de Venta

<p align="center">
  <img src="../img/StartFactSalesOrderLines.png" width="800" alt="startesquemaorders">
</p>


### ​1. Descripción General

​Este modelo en estrella (*Star Schema*) está diseñado específicamente para capturar y analizar la **demanda de los clientes a través del proceso de órdenes de venta**, constituyéndose como la fuente analítica fundamental para la **planeación de la demanda** (*Demand Planning*).

​Implementa un patrón de **tabla de hechos transaccional por línea de orden** (*Transactional Fact Table*), registrando cada unidad de demanda individual solicitada. Esta alta granularidad permite analizar los volúmenes reales de pedidos, detectar patrones de estacionalidad, evaluar la variabilidad del consumo y proyectar requerimientos futuros con precisión.

### ​2. Arquitectura del Modelo

-   ​**Grano del Modelo:** Cada registro representa una línea individual de producto demandada (ItemKey) dentro de una orden de venta específica (OrderID), vinculada a un cliente (CustomerKey) y a la fecha exacta en que se generó la necesidad (DateKey).
-   ​**Tipo de Esquema:** Modelo estrella (*Star Schema*).
-   ​**Tabla de Hechos:** FactSalesOrderLines (Hechos de líneas de órdenes de venta como señal de demanda).
-   ​**Dimensiones Clave:**
-   -   ​DimCustomer (Clientes)
    -   ​DimItem (Catálogo de productos)
    -   ​DimDate (Dimensión de tiempo para análisis de estacionalidad y horizonte temporal)
-   ​**Integridad y Trazabilidad (SCD Tipo 2):** Las dimensiones principales implementan **Slowly Changing Dimensions Tipo 2** (ValidFrom, ValidTo, IsCurrent). Esto es crítico para la planeación de la demanda, ya que garantiza que el análisis histórico refleje las características del cliente o del producto exactamente como estaban vigentes en el momento en que se originó la orden, evitando distorsiones en las métricas de consumo histórico.

### ​3. Métricas y Atributos de Soporte a la Demanda

-   ​**Métricas Cuantitativas (Drivers de Demanda):**
-   -   ​Quantity: Métrica primaria de volumen. Representa la cantidad exacta de unidades demandadas por línea de orden, siendo el insumo base para calcular la demanda histórica y construir pronósticos.
    -   ​UnitPrice: Precio unitario asociado a la transacción (útil para ponderar la criticidad o valor monetario de la demanda).
-   ​**Atributos de Control y Contexto:**
-   -   ​OrderLineKey: Identificador único de la línea de detalle (Clave primaria).
    -   ​OrderID: Identificador del pedido global, fundamental para analizar el tamaño medio de los lotes de pedido (*Order Batching*).

### ​4. Casos de Uso en Planeación de la Demanda

-   ​**Línea Base para Pronósticos (*****Demand Forecasting*****):** Proporciona la historia de transacciones limpia y estructurada necesaria para alimentar modelos de pronóstico estadístico (como suavizamiento exponencial, modelos ARIMA o análisis de tendencias) a nivel de SKU y cliente.
-   ​**Análisis de Estacionalidad y Patrones Temporales:** Permite identificar ciclos de demanda diarios, semanales, mensuales o estacionales mediante la integración con DimDate, facilitando la anticipación de picos de consumo (*Demand Spikes*).
-   ​**Segmentación de Clientes por Comportamiento de Compra:** Utiliza los atributos de DimCustomer para clasificar la demanda por zonas geográficas, canales o tipología de cliente, permitiendo ajustar los modelos de pronóstico a perfiles de consumo específicos.
-   ​**Rotación y Comportamiento del Producto:** A través de DimItem, ayuda a identificar productos de alta rotación frente a artículos de demanda errática o intermitente, optimizando las políticas de inventario de seguridad.

## 5\. Consideraciones Analíticas

-   Las métricas cuantitativas como `Quantity` son **completamente aditivas** a través de cualquier dimensión (producto, cliente, categoría temporal o geográfica).
-   El cálculo de ingresos totales o valor de ventas se realiza de manera dinámica multiplicando cantidad por precio unitario en cada línea, asegurando precisión analítica en herramientas de Business Intelligence como Power BI

# Modelo Estrella: Venta Facturada

<p align="center">
  <img src="../img/StartFactSalesLine.png" width="800" alt="startesquemasales">
</p>

## 1\. Descripción General

Este modelo en estrella (*Star Schema*) está diseñado para el análisis financiero, de ingresos reales y de comportamiento comercial de ventas facturadas.

Implementa un patrón de **hechos de transacciones (*****Transactional Fact Table*****)**, el cual registra de forma detallada cada línea de producto dentro de una factura comercial emitida a los clientes. A diferencia de las órdenes de venta proyectadas, este modelo refleja las transacciones completadas y facturadas, permitiendo auditar con precisión la facturación, el rendimiento por cliente y la comercialización de productos a lo largo del tiempo.

## 2\. Arquitectura del Modelo

-   **Grano del Modelo:** Un registro representa una línea individual de producto (`ItemKey`) dentro de una factura específica (`InvoiceID`), asociada a un cliente (`CustomerKey`) y una fecha de emisión de factura (`DateKey`).
-   **Tipo de Esquema:** Modelo estrella (*Star Schema*).
-   **Tabla de Hechos:** `FactSalesLines`.
-   **Dimensiones:**
-   -   `DimItem` (Productos)
    -   `DimCustomer` (Clientes)
    -   `DimDate` (Tiempo)
-   **Tipo de Hecho:** Transaccional (*Transactional Fact Table*).

### Integridad y granularidad

-   Cada fila corresponde a una línea de detalle comercial facturada, conectando las dimensiones clave de negocio mediante claves foráneas.
-   Las dimensiones incorporan control de cambios históricos mediante **SCD Tipo 2** (`ValidFrom`, `ValidTo`, `IsCurrent`), asegurando que las facturas históricas mantengan los atributos vigentes del cliente o producto en el momento en que se realizó la transacción comercial.

## 3\. Métricas y Atributos de Análisis

-   **Métricas Cuantitativas:**
-   -   `Quantity`: Cantidad de unidades del producto facturadas en la línea de venta.
    -   `UnitPrice`: Precio unitario aplicado y facturado al cliente en la transacción.
-   **Atributos de Control y Descriptivos:**
-   -   `InvoiceLineKey`: Identificador único de la línea de factura (Clave primaria de la tabla de hechos).
    -   `InvoiceID`: Identificador de la factura comercial general a la que pertenece la línea.

## 4\. Casos de Uso del Negocio

1.  **Análisis de Ingresos y Facturación Real:** Cálculo preciso de los ingresos totales generados mediante la multiplicación de Quantity × UnitPrice a nivel de línea, factura, cliente o categoría de producto.
2.  **Segmentación y Comportamiento de Clientes:** Evaluación del volumen de compras, categorías preferidas (`CustomerCategory`) y distribución geográfica (`Country`, `State`, `City`) de los clientes activos utilizando `DimCustomer`.
3.  **Rendimiento de Líneas de Productos:** Identificación de los artículos más rentables y vendidos, clasificados por sus grupos de stock (`StockGroupName`) a través de `DimItem`.
4.  **Análisis Financiero y Temporal:** Monitoreo de tendencias de facturación diarias, semanales, mensuales o por periodos de cierre contable (`IsLastDayOfMonth`, `IsWorkingDay`) gracias a la integración con `DimDate`.

## 5\. Consideraciones Analíticas

-   Las métricas cuantitativas como `Quantity` y los valores calculados de ingresos son **completamente aditivos** a través de cualquier dimensión del modelo.
-   Permite cerrar el ciclo comercial del Data Warehouse al contrastar los pedidos planeados (`FactSalesOrderLines`) frente a las transacciones de ingresos reales consolidadas (`FactSalesLines`).

-   ​**Aditividad de la Demanda:** La métrica Quantity es completamente aditiva a lo largo de cualquier eje dimensional (se puede sumar por categoría de producto, región de cliente, semana o año comercial), lo que permite evaluar el volumen de demanda en el nivel de agregación requerido para la toma de decisiones operativas o tácticas.


# Modelo Estrella: Órden de Compra

<p align="center">
  <img src="../img/StartFactPurchaseLines.png" width="800" alt="startesquemapurchase">
</p>

## 1\. Descripción General

Este modelo en estrella (*Star Schema*) está diseñado para el análisis del proceso de compras, abastecimiento y la relación comercial con los proveedores.

Implementa un patrón de **hechos de transacciones por línea de orden de compra (*****Transactional Fact Table*****)**, el cual registra de manera detallada cada línea de artículo solicitada a los proveedores. Permite auditar el volumen de mercancía pedida frente a la recibida, los costos esperados, el control de entregas y el estado de finalización de las órdenes de aprovisionamiento a lo largo del tiempo.

## 2\. Arquitectura del Modelo

-   **Grano del Modelo:** Un registro representa una línea individual de producto (`ItemKey`) dentro de una orden de compra específica (`PurchaseOrderID`), asociada a un proveedor (`SupplierKey`) y una fecha de emisión o transacción (`DateKey`).
-   **Tipo de Esquema:** Modelo estrella (*Star Schema*).
-   **Tabla de Hechos:** `FactPurchaseLines`.
-   **Dimensiones:**
-   -   `DimItem` (Productos)
    -   `DimSupplier` (Proveedores)
    -   `DimDate` (Tiempo)
-   **Tipo de Hecho:** Transaccional por línea de detalle (*Transactional Fact Table*).

### Integridad y granularidad

-   Cada fila corresponde a una línea de detalle dentro de un pedido de compra, vinculando las dimensiones clave del negocio mediante claves foráneas.
-   Las dimensiones incorporan control de cambios históricos mediante **SCD Tipo 2** (`ValidFrom`, `ValidTo`, `IsCurrent`), asegurando que las compras históricas conserven los atributos vigentes del producto o proveedor en el momento en que se generó la orden.

## 3\. Métricas y Atributos de Análisis

-   **Métricas Cuantitativas y Operativas:**
-   -   `OrderedOuters`: Cantidad de contenedores/bultos exteriores pedidos al proveedor.
    -   `ReceivedOuters`: Cantidad de contenedores/bultos exteriores efectivamente recibidos en almacén.
    -   `ExpectedUnitPricePerOuter`: Precio unitario esperado por contenedor exterior.
-   **Atributos de Control y Descriptivos:**
-   -   `PurchaseLineKey`: Identificador único de la línea de compra (Clave primaria de la tabla de hechos).
    -   `PurchaseOrderID`: Identificador de la orden de compra general.
    -   `LastReceiptDateKey`: Clave de la fecha correspondiente a la última recepción de mercancía para esa línea.
    -   `IsOrderLineFinished`: Bandera lógica (`BIT`) que indica si la línea de la orden de compra ha sido completada o finalizada.

## 4\. Casos de Uso del Negocio

1.  **Control de Abastecimiento y Cumplimiento:** Evaluación de la eficiencia de los proveedores comparando el volumen pedido frente al recibido (`OrderedOuters` vs. `ReceivedOuters`).
2.  **Auditoría de Costos de Adquisición:** Seguimiento de los precios pactados o esperados (`ExpectedUnitPricePerOuter`) para optimizar presupuestos y negociaciones de compras.
3.  **Gestión de Entregas y Tiempos:** Monitoreo del estado de las órdenes (`IsOrderLineFinished`) y la fecha de última recepción (`LastReceiptDateKey`) para medir los plazos de entrega (*Lead Times*).
4.  **Evaluación de Proveedores:** Análisis del rendimiento de los proveedores (`DimSupplier`) agrupados por ubicación geográfica y categorías de suministro.

## 5\. Consideraciones Analíticas

-   Las métricas cuantitativas de volumen (`OrderedOuters`, `ReceivedOuters`) son **completamente aditivas** a través de las dimensiones de tiempo, producto y proveedor.
-   Facilita métricas clave de cadena de suministro como el nivel de servicio del proveedor (*fill rate*) al contrastar las cantidades pedidas con las recibidas.
-   Permite cerrar el ciclo de inventarios y ventas conectando las entradas de mercancía con los saldos de stock y las salidas comerciales del Data Warehouse.


# Arquitectura del Data Warehouse: Esquema de Constelación (Star Schema Constellation)

<p align="center">
  <img src="../img/Datawharehouse.png" width="800" alt="Datawharehouse">
</p>

## Enfoque Estratégico: Análisis de Inventarios, Planificación de la Demanda, Abastecimiento y Business Intelligence

## 1\. Descripción General y Visión Estratégica

La arquitectura del Data Warehouse de **KireImports** está diseñada para integrar y consolidar la información de los principales procesos comerciales y operativos de la organización, proporcionando una infraestructura analítica orientada al **análisis de inventarios, planificación de la demanda (Demand Planning), abastecimiento (Procurement) y Business Intelligence (BI)**.

El modelo implementa una arquitectura de **Constelación de Estrellas (Star Schema Constellation)** basada en los principios de la metodología de Ralph Kimball y su arquitectura de bus de datos (*Data Warehouse Bus Architecture*). Esta estructura integra múltiples procesos de negocio mediante el uso de **dimensiones conformadas (Conformed Dimensions)**, permitiendo analizar de manera consistente las relaciones entre ventas, compras, proveedores y movimientos de inventario.

El propósito principal es proporcionar una visión integral de la cadena de suministro, conectando el comportamiento de la demanda con las operaciones de abastecimiento y la disponibilidad de inventario.

A partir de esta infraestructura, se busca desarrollar capacidades analíticas que permitan:

-   Analizar el comportamiento histórico de la demanda y las ventas.
-   Evaluar el desempeño y la disponibilidad del inventario.
-   Identificar patrones de consumo, tendencias y comportamientos estacionales.
-   Analizar el desempeño de proveedores y las operaciones de abastecimiento.
-   Desarrollar análisis de series temporales y modelos de pronóstico de demanda (*Demand Forecasting*).
-   Generar información estratégica para respaldar la planificación de inventarios y la toma de decisiones.

La información consolidada estará disponible para su explotación mediante herramientas de Business Intelligence, principalmente Power BI, y podrá servir posteriormente como fuente de datos para modelos estadísticos y soluciones analíticas más avanzadas.

## 2\. Componentes del Modelo Global

### A. Dimensiones Compartidas (Conformed Dimensions)

Las dimensiones conformadas constituyen la estructura común de segmentación y análisis de los diferentes procesos de negocio. Su implementación permite mantener criterios consistentes al relacionar información de ventas, inventarios y abastecimiento.

-   **`**DimItem**` (Productos):** Representa el catálogo de productos comercializados por la organización. Permite analizar el comportamiento de la demanda, las ventas y el inventario según las características de cada artículo, sus grupos de stock, precios y presentaciones de empaque. Implementa **SCD Tipo 2** para conservar el historial de cambios relevantes en los atributos de los productos y permitir análisis históricos consistentes.
-   **`**DimCustomer**` (Clientes):** Permite segmentar y analizar el comportamiento comercial de los clientes mediante sus características y ubicación geográfica. Facilita la identificación de patrones de compra, concentración de demanda y comportamiento de consumo por regiones, ciudades y categorías de clientes. Contempla **SCD Tipo 2** para conservar los cambios históricos de los atributos definidos para seguimiento.
-   **`**DimSupplier**` (Proveedores):** Constituye un componente fundamental para el análisis de abastecimiento. Permite evaluar a los proveedores según su categoría, ubicación geográfica y participación en las operaciones de compra. Facilita el análisis histórico de las relaciones comerciales y los cambios en sus características mediante **SCD Tipo 2**.
-   **`**DimDate**` (Tiempo):** Dimensión temporal creada específicamente para el Data Warehouse, sin depender de una tabla de origen del sistema OLTP. Proporciona atributos calendarios, jerarquías temporales y clasificaciones operativas como `IsWorkingDay`, `IsHoliday`, `IsFirstDayOfMonth` e `IsLastDayOfMonth`. Es fundamental para analizar tendencias, estacionalidad, comportamiento semanal y mensual, series temporales y planificación de la demanda.

### B. Tablas de Hechos Integradas para el Análisis de la Cadena de Suministro

El modelo integra cinco tablas de hechos especializadas, cada una diseñada para representar un proceso de negocio y una granularidad analítica específica.

1.  **`**FactSalesOrderLines**` (Líneas de Órdenes de Venta):** Registra los productos solicitados por los clientes mediante sus órdenes de venta. Constituye una fuente fundamental para estudiar el comportamiento histórico de los pedidos y la demanda comercial, identificar patrones de solicitud y analizar la evolución de los requerimientos de los clientes. Su información puede utilizarse como insumo para los análisis de planificación de la demanda.
2.  **`**FactSalesLines**` (Líneas de Facturación):** Registra las operaciones de venta facturadas y permite analizar la demanda comercial materializada, los volúmenes vendidos y los ingresos generados. Facilita el estudio de tendencias de ventas, comportamiento de consumo y evolución del desempeño comercial.
3.  **`**FactPurchaseLines**` (Líneas de Órdenes de Compra):** Registra los productos solicitados a los proveedores mediante órdenes de compra. Permite analizar las cantidades adquiridas, los costos de adquisición y el comportamiento de las operaciones de abastecimiento. Constituye una fuente para evaluar el desempeño de los proveedores y estudiar la relación entre las necesidades de inventario y las decisiones de compra.
4.  **`**FactStockItemTransactions**` (Transacciones de Inventario):** Registra los movimientos de inventario asociados a las entradas y salidas de mercancía. Permite analizar la dinámica del stock, identificar patrones de movimiento, estudiar la rotación de productos y comprender el comportamiento de las existencias a través del tiempo.
5.  **`**FactStockHoldings**` (Existencias de Inventario):** Representa las existencias de inventario mediante una estructura de tipo *Periodic Snapshot*. Permite analizar los niveles de stock disponibles en determinados momentos de referencia, identificar acumulaciones, posibles excesos y situaciones de escasez, además de estudiar la evolución de las existencias por producto y período.

## 3\. Integración Estratégica para el Negocio

La arquitectura permite integrar diferentes procesos de negocio para construir una visión analítica transversal de la operación.

-   **Integración de la Demanda y las Ventas:** Relaciona los pedidos realizados por los clientes (`FactSalesOrderLines`) con las ventas facturadas (`FactSalesLines`), permitiendo estudiar la evolución de la demanda comercial, comparar pedidos con ventas materializadas y analizar patrones históricos de consumo.
-   **Integración del Abastecimiento y los Proveedores:** Conecta las operaciones de compra (`FactPurchaseLines`) con la dimensión de proveedores (`DimSupplier`), facilitando el análisis de costos de adquisición, volúmenes solicitados y comportamiento de las operaciones de abastecimiento.
-   **Integración del Flujo y Disponibilidad de Inventario:** Relaciona las transacciones de inventario (`FactStockItemTransactions`) con las existencias periódicas (`FactStockHoldings`), proporcionando información para estudiar los movimientos de mercancía y la evolución de los niveles de inventario.
-   **Soporte para la Planificación de la Demanda (Demand Planning):** La disponibilidad de información histórica sobre pedidos, ventas y existencias, vinculada mediante dimensiones conformadas y una dimensión temporal robusta, proporciona una base analítica para desarrollar estudios de demanda, identificar tendencias, evaluar estacionalidad y construir modelos de pronóstico.
-   **Business Intelligence y Gobierno de Datos:** La integración mediante dimensiones conformadas permite desarrollar modelos de análisis en Power BI, manteniendo criterios uniformes de segmentación y facilitando la comparación de indicadores entre los diferentes procesos de negocio.

## 4\. Casos de Uso Analíticos y Aplicaciones de Negocio

La arquitectura del Data Warehouse está diseñada para respaldar los siguientes escenarios analíticos:

### 1\. Análisis y Control de Inventarios

-   Evaluación de los niveles de existencias por producto y grupo de stock.
-   Identificación de productos con acumulación o escasez de inventario.
-   Análisis de entradas y salidas de mercancía.
-   Estudio de la rotación y comportamiento histórico del inventario.
-   Evaluación de la evolución de las existencias a través del tiempo.

### 2\. Planificación de la Demanda (Demand Planning)

-   Análisis del comportamiento histórico de los pedidos y las ventas.
-   Identificación de patrones de consumo y tendencias de demanda.
-   Análisis de estacionalidad por semana, mes, trimestre y año.
-   Identificación de variaciones en los requerimientos comerciales.
-   Generación de información histórica para respaldar la planificación de necesidades futuras.

### 3\. Pronóstico de Demanda (Demand Forecasting)

-   Preparación de conjuntos de datos históricos para modelos de pronóstico.
-   Análisis de series temporales para identificar tendencias y componentes estacionales.
-   Evaluación del comportamiento de la demanda por producto.
-   Desarrollo y evaluación de modelos de forecasting.
-   Utilización de los resultados de pronóstico como insumo para la planificación de inventarios y abastecimiento.

### 4\. Análisis de Abastecimiento y Desempeño de Proveedores

-   Evaluación de los volúmenes de compra por proveedor y producto.
-   Análisis de costos de adquisición.
-   Seguimiento de las cantidades solicitadas mediante órdenes de compra.
-   Estudio del comportamiento histórico de las operaciones de abastecimiento.
-   Identificación de patrones de compra y oportunidades de optimización.

### 5\. Business Intelligence y Análisis Ejecutivo

-   Construcción de dashboards en Power BI para el seguimiento de indicadores comerciales y operativos.
-   Integración de indicadores de ventas, compras e inventarios.
-   Análisis multidimensional mediante jerarquías y operaciones de *Drill-Down*.
-   Comparación de resultados entre diferentes períodos.
-   Seguimiento de indicadores clave de desempeño (*KPIs*) para respaldar la toma de decisiones.

## 5\. Visión de Evolución de la Arquitectura

El Data Warehouse de KireImports constituye la base de una infraestructura analítica que podrá evolucionar progresivamente hacia capacidades más avanzadas de ingeniería y análisis de datos.

Una vez consolidada la arquitectura de integración, transformación y almacenamiento, la información podrá aprovecharse para implementar procesos analíticos adicionales, modelos de predicción y soluciones de automatización.

Entre las posibles líneas de evolución se encuentran:

-   Implementación de modelos estadísticos y algoritmos de Machine Learning para el pronóstico de demanda.
-   Automatización de procesos de planificación de inventarios.
-   Incorporación de mecanismos de Data Quality y Data Observability para garantizar la integridad, consistencia y confiabilidad de los datos.
-   Implementación de controles de trazabilidad y validación a lo largo del proceso ETL.
-   Integración futura de agentes de Inteligencia Artificial para facilitar la consulta, interpretación y aprovechamiento de la información almacenada.

**En conjunto, KireImports busca consolidar una arquitectura de datos orientada al negocio, capaz de transformar los registros operativos en información analítica confiable y convertir esa información en conocimiento útil para la gestión de inventarios, la planificación de la demanda, el abastecimiento y la toma de decisiones estratégicas.**



\*Nota de herramientas:\* Todos los diagramas de modelos dimensionales incluidos en este documento fueron diseñados utilizando \[DrawDB\](https://www.drawdb.app/).
