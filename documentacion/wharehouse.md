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

Las dimensiones se almacenarán dentro del esquema `dim` y proporcionarán los atributos descriptivos necesarios para analizar los diferentes procesos de negocio de inventarios, demanda de venta, ventas facturadas y abastecimiento.

La implementación contempla la definición de claves sustitutas (Surrogate Keys), claves de negocio (Business Keys), tipos de datos, restricciones de integridad y atributos para la gestión de cambios históricos mediante Slowly Changing Dimensions Tipo 2 (SCD Type 2), en las dimensiones correspondientes.

Las dimensiones conformadas se implementarán como estructuras únicas y compartidas entre las diferentes tablas de hechos, evitando la duplicación de información y manteniendo la consistencia del modelo dimensional.

## 3.2. Implementación SQL

Se crearán las tablas dimensionales mediante instrucciones SQL CREATE TABLE, respetando los atributos, tipos de datos, claves primarias y restricciones establecidos durante el diseño dimensional.

Las tablas dimensionales que se implementarán son:

- DimDate: dimensión temporal que permitirá analizar la información mediante diferentes niveles de granularidad, incluyendo días, semanas, meses, trimestres y años.
- DimItem: dimensión de productos que almacenará los atributos descriptivos y comerciales de los artículos, incorporando gestión histórica mediante SCD Tipo 2.
- DimSupplier: dimensión de proveedores que contendrá información descriptiva, geográfica y de clasificación de los proveedores, incorporando gestión histórica mediante SCD Tipo 2.
- DimCustomer: dimensión de clientes que almacenará información descriptiva, geográfica y de clasificación de los clientes, incorporando gestión histórica mediante SCD Tipo 2.

