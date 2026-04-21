# Handling Parcel Geometry With Many Associated Tabular Records
## Stacked Polygons

The statewide parcel dataset overseen by the [Vermont Parcel Program](https://vcgi.vermont.gov/data-and-programs/parcel-program) is comprised of **parcel geometry**, the approximate parcel boundary lines drawn as closed multi-sided shapes (polygons), and **parcel attribution** from the annual Grand List. These two components are joined together by a matching School Property Account Number (SPAN) in the attribute table of the parcel polygons layer and in the Grand List. 

In most cases each parcel polygon is joined to one Grand List record, but it is not uncommon for one parcel polygon to be assoicated with more than one Grand List record. This is often the case with parcel polygons that repersent condominium developments as they are typically described by their percentage of undivided interest in the common area and facilities rather than discrete boundaries that can be easily represented by polygons. 

The **stacked polygons method** is the current recommendation for representing unlanded structures and common interest parcels such as condominiums, per the Vermont GIS Parcel Data Standard. A stacked polygon is a group of identical parcel polygons stacked on top of each other with a different Grand List record assigned to each. The stacked polygons can be “flattened” to remove all but one polygon for each parcel for analytical purposes.

## Using SQL Server to Create Stacked Polygons

The SQL script below is a stored procedure, **JoinGL2Parcels**, that joins the statewide Grand List table to parcel polygons via a matching SPAN. The Intersection Table facilitates this join by relating more than one Grand List record to the same parcel polygon, resulting in stacked polygons. A **stored procedure** is a collection of SQL statements saved in a database along with the data needed to excute the prodecure. Stored procedures are often used for tasks that need to be executed repeatedly. 

## How the SQL Script Works

### Data Inputs, Outputs, and Aliases

**Data Inputs:**
- **PARCEL_Admin.GRANDLIST**: Database table of the statewide Grand List.
- **PARCEL_Admin.TABLE_VTPARCELS_intersection_evw**: An enterprise geodatabase versioned view (EGDB view) of the Intersection Table.  
- **PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw**: A EGDB view of the parcel polygons feature class.

**Data Outputs:**
- **PARCEL_Admin.D_GL2INT**: An intermediate table that holds a successful join between the Grand List and the Intersection Table (parcel polygons not yet attached).
- **PARCEL_Admin.D_GL2PARCELS**: The final production layer.

Throughout the stored procedure, table aliases are used as a nickname for the full table names for readability and efficiency. 

|Alias   |Table Name                                                    |
|--------|--------------------------------------------------------------|
|i       |PARCEL_Admin.TABLE_VTPARCELS_intersection_evw                 |
|g       |PARCEL_Admin.GRANDLIST                                        |
|p       |PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw|

### Step 1: Setting Up the Workspace

The first part of the script establishes the workspace. The USE command directs to the **PARCELGDB** database where the stored procedure, JoinGL2Parcels, will be saved as well as where the data exists needed to run to the stored procedure.

```sql
-- Set the working directory to the PARCELGDB database which contains the GIS data needed to run the procedure.

USE PARCELGDB;
GO

-- Define the stored procedure JoinGL2Parcels.

CREATE PROCEDURE JoinGL2Parcels As
```

### Step 2: Define Intermediate Table

The next part of the script creates an intermediate table called **D_GL2INT** which joins **PARCEL_Admin.GRANDLIST** (the Grand List) and **PARCEL_Admin.TABLE_VTPARCELS_intersection_evw** (the Intersection Table). An versioned view (denoted by the _evw suffix) of the Intersection Table is used as mutiple users can make changes to the Intersection Table at the same time and versioning reconciles those differences. A versioned view of **PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw** (the parcel polygons) is also used for the same reason.

Each time the stored procedure is run, the intermediate table is truncated and then re-populated. A **LEFT OUTER JOIN** is used to merge the Intersection Table and the Grand List. This type of join merges two tables while maintaining every record in the table mentioned first (the Left table). In this case, every record in the Intersection Table is maintained. If there is a matching SPAN between the Intersection Table (GLIST_SPAN field) and the Grand List (SPAN field), the Grand List attribution is added to D_GL2INT.

![IMAGE 1](https://github.com/VCGI/documentation/blob/main/parceldata/IMAGE1_StackedPolygons.png?raw=true)

```sql
-- Remove pre-existing data from D_GL2INT table which contains derived data and is not resgistered with the EGDB.

TRUNCATE TABLE PARCEL_Admin.D_GL2INT;

-- Insert new data into D_GL2INT table by joining GRANDLIST table to TABLE_VTPARCELS_intersection_evw which is an EGDB view that presents default version of TABLE_VTPARCELS_intersection table in the EGDB.

-- NOTE: The i.YEAR column is named INTRS_YEAR to differeniate it with YEAR field of the other tables. The i.TOWN column is named INTRS_TOWN to differentiate it with the TOWN field of other tables. The join is based on SPAN field contents excluding dashes (-) and records with NULLs in the GIS_SPAN field of TABLE_VTPARCELS_intersection_evw are excluded from the result.

INSERT INTO PARCEL_Admin.D_GL2INT 
SELECT 
i.GIS_SPAN, i.GLIST_SPAN, i.YEAR AS INTRS_YEAR, i.TOWN AS INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.TABLE_VTPARCELS_intersection_evw as i 
LEFT OUTER JOIN PARCEL_Admin.GRANDLIST AS g 
ON (REPLACE(i.GLIST_SPAN,'-','') = REPLACE(g.span,'-','')) WHERE i.GIS_SPAN IS NOT NULL;
```

### Step 3: Define Final Production Table

The last part of this script creates the final production table **D_GL2PARCELS** using two joins. After truncating, a **RIGHT OUTER JOIN** merges D_GL2INT to the parcel polygons with a SPAN match. All the records of the table listed second (the Right table) are maintainted. In this case, the records for D_GL2INT records are maintained; if there is a SPAN match between D_GL2INT (GIS_SPAN field) and the parcel polygons (SPAN field), the geometry is added to D_GL2PARCELS. The WHERE p.SHAPE IS NOT NULL command ensure only records with actual geometry are included.

*Two seperate joins are necceary to account for the one-to-many relationship of stacked polygons. The RIGHT OUTER JOIN ensure that the duplications in the GIS_SPAN field of D_GL2INT create duplicate geometry as each record joins to the same parcel polygon.*

```sql
-- Remove pre-existing data from D_GL2PARCELS table which contains derived data and is not registered with the EGDB.

TRUNCATE TABLE PARCEL_Admin.D_GL2PARCELS;

-- Insert new data into D_GL2PARCELS by joining D_GL2PARCELS table to Cadastral_VTPARCELS_poly_standardized_parcels_evw feature class.

-- Joins are based on the SPAN field excluding dashes.

-- D_GL2PARCELS has an identity field named UNIQUE_ID which gives D_GL2PARCELS a field that can function as a surrogate OBJECTID field, allowing D_GL2PARCELS to function as a query layer. 

INSERT INTO PARCEL_Admin.D_GL2PARCELS 

SELECT 
p.SPAN, p.MAPID, p.PROPTYPE, p.YEAR, p.TOWN, p.SOURCENAME, p.SOURCETYPE, p.SOURCEDATE, p.EDITMETHOD, p.EDITOR, p.EDITDATE, p.MATCHSTAT, p.EDITNOTE, p.SHAPE, p.GDB_GEOMATTR_DATA, 
g.GLIST_SPAN, g.INTRS_YEAR, g.INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw AS p 
RIGHT OUTER JOIN PARCEL_Admin.D_GL2INT AS g 
ON (REPLACE(p.SPAN,'-','') = REPLACE(g.GIS_SPAN,'-','')) WHERE p.SHAPE IS NOT NULL
ORDER BY REPLACE(g.GLIST_SPAN,'-','');
```

Because the RIGHT OUTER JOIN only adds the geometry for the parcel polygons with a SPAN match, a second join is neccesary to add the "unmatched" parcels (parcels polygons with no match in the D_GL2INT). A LEFT OUTER JOIN is used, which maintains all the parcel polygons records. The WHERE g.GIS_SPAN IS NULL command then filters for the unmatched parcels and adds those features to D_GL2PARCELS.

```sql
INSERT INTO PARCEL_Admin.D_GL2PARCELS 

SELECT 
p.SPAN, p.MAPID, p.PROPTYPE, p.YEAR, p.TOWN, p.SOURCENAME, p.SOURCETYPE, p.SOURCEDATE, p.EDITMETHOD, p.EDITOR, p.EDITDATE, p.MATCHSTAT, p.EDITNOTE, p.SHAPE, p.GDB_GEOMATTR_DATA, 
g.GLIST_SPAN, g.INTRS_YEAR, g.INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw AS p 
LEFT OUTER JOIN PARCEL_Admin.D_GL2INT AS g 
ON (REPLACE(p.SPAN,'-','') = REPLACE(g.GIS_SPAN,'-','')) WHERE g.GIS_SPAN IS NULL
ORDER BY TOWN;

GO
```

![IMAGE 2](https://github.com/VCGI/documentation/blob/main/parceldata/IMAGE2_StackedPolygons.png?raw=true)

## Full SQL Script

*Additional annoations added for clarity.*

```sql
-- Set the working directory to the PARCELGDB database which contains the GIS data needed to run the procedure.

USE PARCELGDB;
GO

-- Define the stored procedure JoinGL2Parcels.

CREATE PROCEDURE JoinGL2Parcels As

-- Remove pre-existing data from D_GL2INT table which contains derived data and is not resgistered with the EGDB.

TRUNCATE TABLE PARCEL_Admin.D_GL2INT;

-- Insert new data into D_GL2INT table by joining GRANDLIST table to TABLE_VTPARCELS_intersection_evw which is an EGDB view that presents default version of TABLE_VTPARCELS_intersection table in the EGDB.

-- NOTE: The i.YEAR column is named INTRS_YEAR to differeniate it with YEAR field of the other tables. The i.TOWN column is named INTRS_TOWN to differentiate it with the TOWN field of other tables. The join is based on SPAN field contents excluding dashes (-) and records with NULLs in the GIS_SPAN field of TABLE_VTPARCELS_intersection_evw are excluded from the result.

INSERT INTO PARCEL_Admin.D_GL2INT 

SELECT 
i.GIS_SPAN, i.GLIST_SPAN, i.YEAR AS INTRS_YEAR, i.TOWN AS INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.TABLE_VTPARCELS_intersection_evw as i 
LEFT OUTER JOIN PARCEL_Admin.GRANDLIST AS g 
ON (REPLACE(i.GLIST_SPAN,'-','') = REPLACE(g.span,'-','')) WHERE i.GIS_SPAN IS NOT NULL;

-- Remove pre-existing data from D_GL2PARCELS table which contains derived data and is not registered with the EGDB.

TRUNCATE TABLE PARCEL_Admin.D_GL2PARCELS;

-- Insert new data into D_GL2PARCELS by joining D_GL2PARCELS table to Cadastral_VTPARCELS_poly_standardized_parcels_evw feature class.
--NOTE: This is done by doing 2 joins. 
    -- The first join handles parcels that correspond to records in the Intersection Table via a match between the SPAN fields. 
    -- The second join handles parcels that don't correspond to records in the Intersection Table (no match between SPAN fields as in the case of a road right-of-way, for example).

-- The first join is a RIGHT OUTER JOIN. All D_GL2INT records that join are in the result, this causes parcel polygons that have more than one corresponding record in the Intersection Table to be repersented as stacked polygons (duplicate geometry with unique attribution). An "order by" is used to stack polygons in SPAN-based order. In the seond join, an "order by" is used to sort results by TOWN field. 

-- Joins are based on the SPAN field excluding dashes.

-- D_GL2PARCELS has an identity field named UNIQUE_ID which gives D_GL2PARCELS a field that can function as a surrogate OBJECTID field, allowing D_GL2PARCELS to function as a query layer. 

INSERT INTO PARCEL_Admin.D_GL2PARCELS 

SELECT 
p.SPAN, p.MAPID, p.PROPTYPE, p.YEAR, p.TOWN, p.SOURCENAME, p.SOURCETYPE, p.SOURCEDATE, p.EDITMETHOD, p.EDITOR, p.EDITDATE, p.MATCHSTAT, p.EDITNOTE, p.SHAPE, p.GDB_GEOMATTR_DATA, 
g.GLIST_SPAN, g.INTRS_YEAR, g.INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw AS p 
RIGHT OUTER JOIN PARCEL_Admin.D_GL2INT AS g 
ON (REPLACE(p.SPAN,'-','') = REPLACE(g.GIS_SPAN,'-','')) WHERE p.SHAPE IS NOT NULL
ORDER BY REPLACE(g.GLIST_SPAN,'-','');

INSERT INTO PARCEL_Admin.D_GL2PARCELS 

SELECT 
p.SPAN, p.MAPID, p.PROPTYPE, p.YEAR, p.TOWN, p.SOURCENAME, p.SOURCETYPE, p.SOURCEDATE, p.EDITMETHOD, p.EDITOR, p.EDITDATE, p.MATCHSTAT, p.EDITNOTE, p.SHAPE, p.GDB_GEOMATTR_DATA, 
g.GLIST_SPAN, g.INTRS_YEAR, g.INTRS_TOWN, 
g.glyear, g.tname, g.parcid, g.owner1, g.owner2, g.addrgl1, g.addrgl2, g.citygl, g.stgl, g.zipgl, g.descprop, g.locaprop, g.cat, g.rescode, g.acresgl, g.real_flv, g.hsted_flv, g.nres_flv, g.land_lv, g.imprv_lv, g.equipval, g.equipcode, g.invenval, g.hsdecl, g.hsiteval, g.vetexamt, g.expdesc, g.enddate, g.statute, g.examt_hs, g.examt_nr, g.uvreduc_hs, g.uvreduc_nr, g.glval_hs, g.glval_nr, g.crhouspct, g.mungl1pct, g.aoegl_hs, g.aoegl_nr, g.e911addr 
FROM PARCEL_Admin.Cadastral_VTPARCELS_poly_standardized_parcels_evw AS p 
LEFT OUTER JOIN PARCEL_Admin.D_GL2INT AS g 
ON (REPLACE(p.SPAN,'-','') = REPLACE(g.GIS_SPAN,'-','')) WHERE g.GIS_SPAN IS NULL
ORDER BY TOWN;

GO
```





