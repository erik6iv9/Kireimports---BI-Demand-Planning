# Implementación física y construcción del Data Warehouse

Esta sección documenta el proceso de implementación física del Data Warehouse de KireImports, tomando como referencia el diseño dimensional y la arquitectura de tipo constelación definidos en los apartados anteriores. En esta etapa, los modelos dimensionales previamente diseñados se materializan mediante la creación de las estructuras físicas de almacenamiento en Microsoft SQL Server, utilizando SQL (Structured Query Language) como lenguaje para la definición y construcción de la base de datos.

La implementación contempla la creación de las tablas de dimensiones y hechos, estableciendo sus respectivos tipos de datos, claves primarias, claves foráneas, restricciones de integridad y relaciones, con el propósito de garantizar la consistencia estructural y referencial del modelo dimensional.

Asimismo, se definen los elementos necesarios para reproducir la arquitectura dimensional diseñada, considerando la implementación de las dimensiones compartidas, la estructura de las tablas de hechos y los mecanismos de identificación y relación entre sus registros.

Finalmente, se obtiene una base de datos estructurada en Microsoft SQL Server, conforme al modelo dimensional de KireImports y preparada para recibir, almacenar y organizar la información proveniente de los procesos de extracción, transformación y carga (ETL/ELT), que serán desarrollados en la siguiente unidad del proyecto.

# 1. Creación del Data Warehouse

## 1.1. Descripción

Se creó la base de datos kireimportsDW en SQL Server,
la cual servirá como estructura principal de almacenamiento
para implementar el modelo dimensional de KireImports.

## 1.2. Implementación SQL

```sql
CREATE DATABASE kireimportsDW;
GO
```

![Creación de la base de datos](../img/implementaciones/creaciondw.png)

## 1.3. Verificación de la implementación

```sql
SELECT
    name AS DatabaseName,
    database_id AS DatabaseID,
    create_date AS CreationDate,
    state_desc AS DatabaseStatus
FROM sys.databases
WHERE name = 'kireimportsDW';
GO
```
![Verificación de la base de datos](../img/implementaciones/verificaciondw.png)

# 2. Creación de esquemas de organización

## 2.1. Descripción

Se establecieron dos esquemas dentro de la base de datos KireImportsDW para organizar físicamente las estructuras del Data Warehouse.

El esquema `dim` almacenará las tablas dimensionales, incluyendo las dimensiones conformadas compartidas entre los diferentes procesos de negocio.

El esquema `fact` almacenará las tablas de hechos correspondientes a los procesos de negocio de KireImports, permitiendo mantener una separación lógica entre las dimensiones y los datos transaccionales y analíticos.

## 2.2. Implementación SQL

```sql
-- Seleccionar la base de datos del Data Warehouse

USE [kireimportsDW];
GO

-- Crear esquema dimensional

CREATE SCHEMA dim AUTHORIZATION dbo;
GO

-- Crear esquema de hechos

CREATE SCHEMA fact AUTHORIZATION dbo;
GO
```

![Creación de los esquemas](../img/implementaciones/esquemasdw.png)

## 2.3. Verificación de la implementación

```sql
SELECT
    name AS SchemaName,
    schema_id AS SchemaID,
    USER_NAME(principal_id) AS SchemaOwner
FROM sys.schemas
WHERE name IN ('dim', 'fact')
ORDER BY name;
GO
```
![Verificación de los esquemas](../img/implementaciones/esquemaverifidw.png)

# 3. Implementación de las tablas de dimensiones

## 3.1. Descripción

Se implementarán físicamente las cuatro tablas dimensionales del Data Warehouse de KireImports en SQL Server, tomando como referencia el modelo dimensional integrado (esquema constelación) definido durante la etapa de diseño.

Las dimensiones se almacenarán dentro del esquema dim y proporcionarán los atributos descriptivos necesarios para analizar los diferentes procesos de negocio de inventarios, demanda de venta, ventas facturadas y abastecimiento.

La implementación contempla la definición de claves sustitutas (Surrogate Keys), claves de negocio (Business Keys), tipos de datos, restricciones de integridad y atributos para la gestión de cambios históricos mediante Slowly Changing Dimensions Tipo 2 (SCD Type 2), en las dimensiones correspondientes.

Las dimensiones conformadas se implementarán como estructuras únicas y compartidas entre las diferentes tablas de hechos, evitando la duplicación de información y manteniendo la consistencia del modelo dimensional.

Las tablas dimensionales que se implementarán son:
- DimDate: dimensión temporal.
- DimItem: dimensión de productos.
- DimSupplier: dimensión de proveedores.
- DimCustomer: dimensión de clientes.

## 3.2. Implementación SQL

La implementación se realizará mediante instrucciones CREATE TABLE dentro del esquema dim, respetando las estructuras definidas en el modelo dimensional.

## 3.2.1. Creación de DimDate
Dimensión temporal que permitirá analizar la información mediante diferentes niveles de granularidad: días, semanas, meses, trimestres y años.

```sql
-- KireImports Data Warehouse
-- Dimension: DimDate
USE [kireimportsDW];
GO

CREATE TABLE dim.DimDate (
    DateKey INT NOT NULL,
    FullDate DATE NOT NULL,
    DayNumberOfWeek TINYINT NOT NULL,
    DayName VARCHAR(15) NOT NULL,
    DayNumberOfMonth TINYINT NOT NULL,
    DayNumberOfYear SMALLINT NOT NULL,
    ISOWeekNumber TINYINT NOT NULL,
    ISOYear SMALLINT NOT NULL,
    ISOYearWeek CHAR(8) NOT NULL,
    WeekStartDate DATE NOT NULL,
    WeekEndDate DATE NOT NULL,
    MonthNumber TINYINT NOT NULL,
    MonthName VARCHAR(15) NOT NULL,
    MonthYear CHAR(7) NOT NULL,
    MonthStartDate DATE NOT NULL,
    MonthEndDate DATE NOT NULL,
    Quarter TINYINT NOT NULL,
    QuarterName CHAR(7) NOT NULL,
    Year SMALLINT NOT NULL,
    IsWeekend BIT NOT NULL,
    IsWorkingDay BIT NOT NULL,
    IsHoliday BIT NOT NULL,
    HolidayName VARCHAR(100) NULL,
    IsFirstDayOfMonth BIT NOT NULL,
    IsLastDayOfMonth BIT NOT NULL,
    IsFirstDayOfQuarter BIT NOT NULL,
    IsLastDayOfQuarter BIT NOT NULL,

    CONSTRAINT PK_DimDate
        PRIMARY KEY (DateKey),

    CONSTRAINT UQ_DimDate_FullDate
        UNIQUE (FullDate)
);
GO
```
![Dimension Date](../img/implementaciones/dimdatedw.png)

## 3.2.2. Creación de DimItem
Dimensión de productos que almacenará los atributos descriptivos y comerciales de los artículos.

Se incorporan los campos ValidFrom, ValidTo e IsCurrent para gestionar el historial de cambios mediante SCD Tipo 2.

```sql
-- =============================================
-- KireImports Data Warehouse
-- Dimension: DimItem
-- =============================================

CREATE TABLE dim.DimItem (
    ItemKey INT IDENTITY(1,1) NOT NULL,
    StockItemID INT NOT NULL,
    StockItemName NVARCHAR(100) NOT NULL,
    UnitPrice DECIMAL(18,2) NOT NULL,
    RecommendedRetailPrice DECIMAL(18,2) NOT NULL,
    UnitPackage NVARCHAR(50) NOT NULL,
    OuterPackage NVARCHAR(50) NOT NULL,
    QuantityPerOuter INT NOT NULL,
    StockGroupName NVARCHAR(50) NOT NULL,
    ValidFrom DATETIME2 NOT NULL,
    ValidTo DATETIME2 NOT NULL,
    IsCurrent BIT NOT NULL,

    CONSTRAINT PK_DimItem
        PRIMARY KEY (ItemKey),

    CONSTRAINT CK_DimItem_ValidityPeriod
        CHECK (ValidFrom < ValidTo)
);
GO

-- Garantiza una única versión actual por ítem

CREATE UNIQUE INDEX UQ_DimItem_Current
ON dim.DimItem (StockItemID)
WHERE IsCurrent = 1;
GO
```
![Dimension Item](../img/implementaciones/dimitemdw.png)

## 3.2.3. Creación de DimSupplier
Dimensión de proveedores que almacenará información descriptiva, de clasificación y ubicación geográfica.

Se incorporan los atributos de control histórico para conservar las diferentes versiones de cada proveedor.

```sql
-- =============================================
-- KireImports Data Warehouse
-- Dimension: DimSupplier
-- =============================================

CREATE TABLE dim.DimSupplier (
    SupplierKey INT IDENTITY(1,1) NOT NULL,
    SupplierID INT NOT NULL,
    SupplierName NVARCHAR(100) NOT NULL,
    SupplierCategoryName NVARCHAR(50) NOT NULL,
    DeliveryCityName NVARCHAR(50) NOT NULL,
    StateProvinceName NVARCHAR(50) NOT NULL,
    CountryName NVARCHAR(50) NOT NULL,
    ValidFrom DATETIME2 NOT NULL,
    ValidTo DATETIME2 NOT NULL,
    IsCurrent BIT NOT NULL,

    CONSTRAINT PK_DimSupplier
        PRIMARY KEY (SupplierKey),

    CONSTRAINT CK_DimSupplier_ValidityPeriod
        CHECK (ValidFrom < ValidTo)
);
GO

-- Garantiza una única versión actual por proveedor

CREATE UNIQUE INDEX UQ_DimSupplier_Current
ON dim.DimSupplier (SupplierID)
WHERE IsCurrent = 1;
GO
```
![Dimension Supplier](../img/implementaciones/dimsupplierdw.png)

## 3.2.4. Creación de DimCustomer
Dimensión de clientes que almacenará información descriptiva, geográfica y de clasificación de los clientes.

Al igual que las dimensiones de productos y proveedores, implementará el seguimiento histórico mediante SCD Tipo 2.

```sql
-- =============================================
-- KireImports Data Warehouse
-- Dimension: DimCustomer
-- =============================================

CREATE TABLE dim.DimCustomer (
    CustomerKey INT IDENTITY(1,1) NOT NULL,
    CustomerID INT NOT NULL,
    CustomerName NVARCHAR(100) NOT NULL,
    CustomerCategory NVARCHAR(50) NOT NULL,
    Country NVARCHAR(60) NOT NULL,
    State NVARCHAR(50) NOT NULL,
    City NVARCHAR(50) NOT NULL,
    ValidFrom DATETIME2 NOT NULL,
    ValidTo DATETIME2 NOT NULL,
    IsCurrent BIT NOT NULL,

    CONSTRAINT PK_DimCustomer
        PRIMARY KEY (CustomerKey),

    CONSTRAINT CK_DimCustomer_ValidityPeriod
        CHECK (ValidFrom < ValidTo)
);
GO

-- Garantiza una única versión actual por cliente

CREATE UNIQUE INDEX UQ_DimCustomer_Current
ON dim.DimCustomer (CustomerID)
WHERE IsCurrent = 1;
GO
```

![Dimension Customer](../img/implementaciones/dimcustomerdw.png)

## 3.3. Verificación de la implementación
Una vez creadas las cuatro dimensiones, ejecutaremos consultas para comprobar que las estructuras físicas se hayan implementado correctamente.
## 3.3.1. Verificación de existencia de las dimensiones
```sql
-- =============================================
-- Verificación de tablas de dimensiones
-- =============================================

SELECT
    SCHEMA_NAME(t.schema_id) AS SchemaName,
    t.name AS TableName,
    t.create_date AS CreationDate
FROM sys.tables t
WHERE t.schema_id = SCHEMA_ID('dim')
ORDER BY t.name;
GO
```
![Verifi Dimensiones](../img/implementaciones/dimverifidw.png)

## 3.3.2. Verificación de columnas y tipos de datos

```sql

-- =============================================
-- Verificación de columnas de dimensión
-- =============================================

SELECT
    t.name AS TableName,
    c.column_id AS ColumnID,
    c.name AS ColumnName,
    TYPE_NAME(c.user_type_id) AS DataType,
    c.max_length AS MaxLength,
    c.is_nullable AS IsNullable,
    c.is_identity AS IsIdentity
FROM sys.tables t
INNER JOIN sys.columns c
    ON t.object_id = c.object_id
WHERE t.schema_id = SCHEMA_ID('dim')
ORDER BY
    t.name,
    c.column_id;
GO

```
![Verifi Columnas Dimensiones](../img/implementaciones/dimverificolumdw.png)


