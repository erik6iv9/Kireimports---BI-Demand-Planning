<h1>Análisis de los esquemas operacionales KireImports (WideWorldImporters)</h1>
La base de datos operacional KireImports organiza sus objetos mediante esquemas funcionales que separan las distintas áreas de negocio de la empresa mayorista. 

<p align="center">
  <img src="img/logo.png" width="350" alt="Logo de Kireimports">
</p>

<h3>Para el diseño del Data Warehouse, los esquemas de mayor relevancia son:</h3>

<h2>1. Esquema Sales (Ventas)</h2>
Contiene toda la actividad comercial con clientes, sirviendo como fuente principal para las tablas de hechos de ventas y el análisis de la demanda.

-Orders y OrderLines (Órdenes de Venta): Representan la demanda inicial de los clientes por los productos. Permiten analizar el volumen de solicitudes de compra, las cantidades requeridas y el comportamiento de la preventa antes de llegar a la facturación.

-Invoices (Facturas): Almacena el encabezado de las facturas de venta emitidas a los clientes. Es la tabla central para medir el volumen de ventas concretadas y los ingresos reales.

-InvoiceLines (Líneas de Factura): Detalla los productos, cantidades, precios unitarios y montos monetarios de cada partida dentro de una factura.

-Customers (Clientes): Contiene la información de los compradores, sus categorías y los grupos de compra a los que pertenecen, lo cual resulta fundamental para estructurar las dimensiones de análisis de la clientela.

<h2>2. Esquema Purchasing (Compras)</h2>
Administra las adquisiciones de mercancía a proveedores, sirviendo de base para los procesos de <ins>aprovisionamiento.</ins>

PurchaseOrders y PurchaseOrderLines: Gestionan las órdenes de compra emitidas a los proveedores.

Suppliers (Proveedores): Información detallada sobre los proveedores y las categorías de productos que suministran.

<h2>3. Esquema Warehouse (Inventario y Almacén)</h2>
Fundamental para el seguimiento de existencias, movimientos de stock y catálogo de productos.

StockItems (Artículos de Inventario): El catálogo maestro de productos, que incluye precios recomendados, pesos, tamaños y proveedores predeterminados (base para la dimensión de productos).

StockItemHoldings (Existencias Actuales): Niveles de inventario actuales y cantidades en stock por artículo.

StockItemTransactions (Transacciones de Inventario): El registro histórico de cada entrada, salida o ajuste de inventario (clave para calcular movimientos de stock y rotación).
