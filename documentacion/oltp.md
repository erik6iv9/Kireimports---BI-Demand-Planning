<h1>Identificación de la base operacional (OLTP)</h1>

<h3>1. Identificación y Origen de la Fuente</h3>
El entorno operacional del proyecto <ins>KireImports</ins> se fundamenta en Wide World Importers, la base de datos transaccional de referencia provista por Microsoft para SQL Server. Su arquitectura modela con alto nivel de detalle las operaciones comerciales cotidianas de una organización global dedicada a la comercialización y distribución mayorista de mercancías.

<h3>2. Contexto de Negocio y Operaciones</h3>
El modelo operacional almacena y gestiona de manera estructurada las actividades cotidianas del negocio, abarcando áreas críticas como:

-Ventas y Pedidos: Registro de transacciones con clientes mayoristas y minoristas.

-Gestión de Compras y Proveedores: Control de adquisición de mercancías.

-Gestión de Inventarios y Existencias: Niveles de stock, movimientos de almacén y existencias físicas.

-Estructura Organizacional: Información de empleados, sucursales y canales de distribución.

<p align="center">
  <img src="../img/wwi.png" width="800" alt="WideWorldImporters sql server esquema">
</p>

<h3>3. Características Técnicas del Entorno Origen</h3>

Plataforma de Base de Datos: Microsoft SQL Server.

Arquitectura: OLTP (Online Transaction Processing), optimizada para operaciones de inserción, actualización y consulta transaccional en tiempo real (normalizada a través de tablas transaccionales).

Volumen y Complejidad: Cuenta con esquemas relacionales robustos (principalmente bajo el esquema Sales, Purchasing, Warehouse, etc.), lo que la convierte en una fuente ideal para diseñar procesos de extracción, transformación y carga (ETL/ELT) hacia un entorno analítico (Data Warehouse).
