# Vermont Parcel Viewer 4.x Release Notes

**Vermont Parcel Viewer | v. 4.x** | [Application Link](https://experience.arcgis.com/experience/b5a5cc7663c84761a305f70b913e1a60/) | [Application Permalink](https://maps.vcgi.vermont.gov/parcelviewer/)  
[Vermont Center for Geographic Information](https://vcgi.vermont.gov/data-and-programs/parcel-program)  
April 24, 2024 

VCGI is pleased to announce the release of the Vermont Parcel Viewer v. 4. Since 2019, and reflecting the work of the [parcel program](https://vcgi.vermont.gov/data-and-programs/parcel-program), the Parcel Viewer has been one of the State's most-used map applications, enabling easy access to one of its most-used spatial datasets. This update offers enhanced capabilities with quick access to information found in the previous parcel viewer, the [Town Mapping Status application](https://maps.vcgi.vermont.gov/parcelstatus/), the [Vermont Land Survey Library](https://landsurvey.vermont.gov/), and the [Vermont Property Transfer spatial dataset](https://geodata.vermont.gov/datasets/VCGI::vt-property-transfers/about). The Viewer displays the best available statewide parcel data as provided by municipalities and mapping vendors. For a full description of the uses, applications, and sources of parcel data please visit the [Parcel Program webpage](https://vcgi.vermont.gov/data-and-programs/parcel-program) and [FAQs](https://vcgi.vermont.gov/resources/frequently-asked-questions/parcel-program-faqs), including specifics about [what parcel data are](https://vcgi.vermont.gov/resources/frequently-asked-questions/parcel-program-faqs#1) and [are not](https://vcgi.vermont.gov/data-and-programs/parcel-program#parceldataarenot).

## Changelog
*Version 4.0.2 | May 8, 2025*
* **New functionality:** Added ACCD GIS-maintained TIF District Boundary layer ([Service](https://anrmaps.vermont.gov/arcgis/rest/services/map_services/ACCD_OpenData/MapServer/7) | [Item](https://geodata.vermont.gov/datasets/accd::vt-tax-increment-financing-tif-districts/about)) to underlying web map, per request from Tax Department. No change to version number.

*Version 4.0.2 | November 19, 2024*
* **New functionality:** Simple text annotation added to the draw widget.

*Version 4.0.1 | April 24, 2024*
* **New feature:** inclusion of [Vermont Property Transfers](https://geodata.vermont.gov/datasets/VCGI::vt-property-transfers/explore) spatial layer. Reflects transfers between January 2019-present with fields including closing date, new ownership, transfer value, buyer/seller use of property, etc. Some fields are integrated into parcel popups; users can also view the layer independently to see all fields.
* **New feature:** inclusion of [Town Parcel Data Status](https://experience.arcgis.com/experience/d88b19e908a1460da8bcb7326f7c2ec6) layer. Reflects town’s update method, recency, RPC, download link in popup; labels towns with date of last geometry update when at relevant zoom level.
* **New feature:** inclusion of Surveys from [Vermont Land Survey Library](https://maps.vcgi.vermont.gov/landsurveylibrary/). Shows approximate extent of surveys uploaded to Land Survey Library. Some fields and a link to pdf survey are integrated into parcel popups; users can also view the layer independently to see all fields and links to pdfs. 
* **New functionality:** application is responsively designed for desktop and mobile applications, automatically detecting native screen resolution and serving layout accordingly.
* **New functionality:** popup window can be relocated to side of screen, enhancing visibility of selected or filtered parcel(s).
* **New functionality:** dynamic labels that display Grand List match status, SPAN, and acreage for each parcel. Labels can be toggled on or off. 
* **New functionality:** Grand List table has additional functionality that allows the calculation of statistics (sum, min, max, average, etc.) for numerical fields. Can apply to all, selected, or filtered records. 
* **New functionality:** FAQ widget added with more details on functionality and the Parcel Program, including links to additional resources.
* **New functionality:** URL parameterization: when using the Search function, the resulting map extent (map center, scale, and layer state visibility) is dynamically incorporated into the URL. Sharing the updated URL allows others to quickly access and see the same specific map view.
* **New functionality:** Filter records for Active Parcels by Town, Property Type, and/or MATCHSTAT without opening the Grand List table. Filter Property Transfers by Town and/or a closing date range. Filter results can be viewed on the map, used to calculate statistics, and exported to other formats.
* **Changed functionality:** Convert coordinates from one system to another based on a clicked point on the map or an address input. Decimal degree (DD) input can be converted to DDM, DMS, Long-Lat, MGRS, USNG, and UTM.
* **Changed functionality:** Most draw capabilities maintained (points, lines areas, with measurements in varying units.) Some functionality no longer available (specifically: snap to vertex, text annotation) due to no longer being available upon [move from from Web AppBuilder to Experience Builder](https://doc.arcgis.com/en/web-appbuilder/latest/create-apps/wab-exb-widget-functionality-matrix.htm).
* **New field:** GIS/calculated acreage, Grand List acreage, and percent difference between the two (absolute) is now dynamically calculated in parcel popups.
* **Updated field:** Town resident code is defined within popup.

**Continued functionality:**

* Toggle layers on/off, adjust transparency, link to source layer/metadata
* Toggle widgets on/off, move and/or resize widget popup
* Print capabilities
* Basemap selection
* Measure distances and areas in unit of choice
* Select one or more features by point, rectangle, circle, lasso, or line; control over layer(s) to select from. Calculate statistics, export, zoom to, or view results in table.
* When searching by address, the result will zoom to the location but will not select the corresponding parcel or display the popup. This is because the address search uses a geolocator to find the location. When searching by town, SPAN, or Parcel ID the result will zoom to, select, and display the popup for the parcel since it is searching on the parcel layer itself. This is consistent with the existing Parcel Viewer.

## Minimum Requirements
The Vermont Parcel Viewer 4.x is built using the ArcGIS Online and ArcGIS Experience Builder platforms. [Click here](https://doc.arcgis.com/en/arcgis-online/reference/browsers.htm) for a list of supported web browsers. VCGI does not control these underlying requirements.

For best performance and full functionality, use the latest version of a browser linked above. Not all functionality is available for mobile devices.

## Known Issues - v.4.0 Release
* [A](https://community.esri.com/t5/arcgis-experience-builder-questions/exb-search-widget-not-behaving/td-p/1370067) [bug](https://community.esri.com/t5/arcgis-experience-builder-questions/full-text-search-index-on-hosted-feature-layer/m-p/1346085/highlight/true#M9200) [exists](https://community.esri.com/t5/arcgis-experience-builder-questions/multiple-things-stopped-working-in-the-search/m-p/1349288#M9354) when searching by SPAN. Entering a valid SPAN in the format ###-###-##### may not produce any results in the dropdown, but hitting Enter should still return the correct parcel and zoom to the location on the map.
* Searches by address do not select the queried parcel - only zoom to and recenter to them. This is related to how layers are queried; see last bullet of section above.

## Data Sources and Dependencies
| Layer                            | Dependency  | Item Name                                                                  | Spatial Ref  | Publisher       | URL                                                                                                                                            | Hosting  | Standalone Open Data  | Life Cycle Supported |   |
|----------------------------------|-------------|----------------------------------------------------------------------------|--------------|-----------------|------------------------------------------------------------------------------------------------------------------------------------------------|----------|-----------------------|----------------------|---|
| Town Parcel Data Status          | Indirect    | FS_VCGI_OPENDATA_Cadastral_VTPARCELS_poly_DataStatus_SP_v1                 | SP           | Services_VCGI   | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_OPENDATA_Cadastral_VTPARCELS_poly_DataStatus_SP_v1/FeatureServer/0  | AGO      | Yes                   | No                   |   |
| Town Parcel Data Status (ETL'd)  | Direct      | FS_VCGI_Parcel_Program_Town_Mapping_Status_PRD_FME_v2                      | WM           | Publisher_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/ArcGIS/rest/services/FS_VCGI_Parcel_Program_Town_Mapping_Status_FME_PRD_v2/FeatureServer         | AGO      | No                    | No                   |   |
| Property Transfers               | Direct      | VT Property Transfers                                                      | WM           | Services_VCGI   | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_OPENDATA_Cadastral_PTTR_point_WM_v1_view/FeatureServer              | AGO      | Yes                   | No                   |   |
| Parcels                          | Direct      | FS_VCGI_VTPARCELS_WM_NOCACHE_v2                                            | WM           | Services_VCGI   | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_VTPARCELS_WM_NOCACHE_v2/FeatureServer                               | AGO      | Yes                   | Yes                  |   |
| Address Points                   | Direct      | FS_VCGI_OPENDATA_Emergency_ESITE_point_SP_v1                               | SP           | Services_VCGI   | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/ArcGIS/rest/services/FS_VCGI_OPENDATA_Emergency_ESITE_point_SP_v1/FeatureServer/0                | AGO      | Yes                   | No                   |   |
| Survey Footprints                | Direct      | VT Data - Locations of Surveys Accessible via Vermont Land Survey Library  | WM           | Publisher_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_Land_Survey_Library_reviewed_v2_1/FeatureServer                     | AGO      | Yes                   | No                   |   |
| Best Of Imagery                  | Direct      | VT Service - Best of Color Imagery                                         | WM           | Services_VCGI   | https://maps.vcgi.vermont.gov/arcgis/rest/services/EGC_services/IMG_VCGI_CLR_WM_CACHE/ImageServer                                              | AGS      | Yes                   | Yes                  |   |
| Basemap                          | Direct      | Human Geography Detail, Labels                                             | WM           | ESRI            | https://basemaps.arcgis.com/arcgis/rest/services/World_Basemap_v2/VectorTileServer                                                             | AGO      | Yes                   | N/A                  |   |
| TIF District Boundaries          | Direct      | TIF District Boundary (ID: 7)                                              | WM           | ACCD_OpenData   | https://anrmaps.vermont.gov/arcgis/rest/services/map_services/ACCD_OpenData/MapServer/7                                                        | AGS      | Yes                   | No                   |   |
