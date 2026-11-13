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

