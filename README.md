# kireimports-supply-chain-bi
Proyecto integral de BI para Supply Chain: modelado dimensional, Data Warehouse, SQL, ETL, análisis de inventarios, KPIs, forecasting y visualización en Power BI.

---

## 📖 Diccionario de Datos: `DimStockItem`

**Tipo:** Tabla de Dimensión (Conformada)  
**Descripción:** Almacena la información atributiva, descriptiva y de configuración logística de los productos comercializados en el ERP `kireimporter`.  
**Estrategia SCD:** SCD Tipo 2 para auditoría de cambios históricos.

| Campo | Tipo de Dato | Tipo de Llave | Descripción / Regla de Negocio |
| :--- | :--- | :--- | :--- |
| **`StockItemKey`** | `INT` | Primary Key (Surrogate) | Clave subrogada autogenerada para el Data Warehouse. Mantiene la unicidad histórica de los registros. |
| **`StockItemID`** | `INT` | Business Key (NK) | Identificador nativo del producto en el sistema de origen `kireimporter` (OLTP). |
| **`StockItemName`** | `NVARCHAR(100)` | Atributo | Nombre comercial y descripción detallada del artículo. |
| **`SupplierID`** | `INT` | Foreign Key | Identificador del proveedor principal que surte el producto. |
| **`ColorID`** | `INT` | Foreign Key (Nullable) | Identificador de color asociado al artículo en catálogo. |
| **`LeadTimeDays`** | `INT` | Atributo | Tiempo de entrega prometido por el proveedor (en días) para reabastecimiento. |
| **`QuantityPerOuter`** | `INT` | Atributo | Cantidad de piezas individuales contenidas en una caja máster o empaque exterior. |
| **`IsCurrent`** | `BIT` | Control SCD2 | Indicador de vigencia (`1` = Registro activo / precio actual, `0` = Registro histórico). |
| **`ValidFrom`** | `DATE` | Control SCD2 | Fecha en la que entra en vigor esta versión de los datos del producto. |
| **`ValidTo`** | `DATE` | Control SCD2 | Fecha en la que vence esta versión (por defecto `9999-12-31` para registros vigentes). |
