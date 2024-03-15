# Vermont Parcel Viewer 4.x Release Notes

**Vermont Parcel Viewer | v. 4.0 Beta** | [Application Link](https://experience.arcgis.com/experience/b5a5cc7663c84761a305f70b913e1a60/) (Sign-in Required)   
[Vermont Center for Geographic Information](https://vcgi.vermont.gov/data-and-programs/parcel-program)  
March 15, 2024 

VCGI is pleased to announce the beta release of the Vermont Parcel Viewer v. 4.0. Since 2020, and reflecting the work of the [parcel program](https://vcgi.vermont.gov/data-and-programs/parcel-program), the Parcel Viewer has been one of the State's most-used map applications, enabling easy access to one of its most-used spatial datasets. This update offers enhanced capabilities with quick access to information found in the previous parcel viewer, the [Town Mapping Status application](https://maps.vcgi.vermont.gov/parcelstatus/), the [Vermont Land Survey Library](https://landsurvey.vermont.gov/), and the [Vermont Property Transfer spatial dataset](https://geodata.vermont.gov/datasets/VCGI::vt-property-transfers/about). The Viewer displays the best available statewide parcel data as provided by municipalities and mapping vendors. For a full description of the uses, applications, and sources of parcel data please visit the [Parcel Program webpage](https://vcgi.vermont.gov/data-and-programs/parcel-program) and [FAQs](https://vcgi.vermont.gov/resources/frequently-asked-questions/parcel-program-faqs), including specifics about [what parcel data are](https://vcgi.vermont.gov/resources/frequently-asked-questions/parcel-program-faqs#1) and [are not](https://vcgi.vermont.gov/data-and-programs/parcel-program#parceldataarenot).

## Changelog
*Version 4.0 | Spring 2024*
* **New feature:** inclusion of Vermont Property Transfers spatial layer. Reflects transfers between January 2019-present with fields including closing date, new ownership, transfer value, buyer/seller use of property, etc. Some fields integrated into parcel popups; can also view layer independently to see all fields.
* **New feature:** inclusion of Town Parcel Data Status layer. Reflects town’s update method, recency, RPC, download link in popup; labels towns with date of last geometry update when at relevant zoom level.
* **New feature:** inclusion of Surveys from Vermont Land Survey Library. Shows approximate extent of surveys uploaded to Land Survey Library. Some fields and a link to pdf survey are integrated into parcel popups; can also view layer independently to see all fields and links to pdfs. 
* **New feature:** application is responsively designed for desktop and mobile applications, automatically detecting native screen resolution, and serving layout accordingly.
* **New feature:** pop up window can be relocated to side of screen, enhancing visibility of selected parcel(s)
* **New functionality:** dynamic labels that display Grand List match status, SPAN and X info for each parcel, that can be toggled on or off. 
* **New functionality:** Grand List table: additional functionality allows calculation of statistics (sum, min, max, average, etc.) for numerical fields. Can apply to all, selected, or filtered records. 
* **New functionality:** FAQ widget added with more details on functionality and Parcel Program, including links to additional resources
* **New functionality:** URL parameterization: when using the Search function, the resulting map extent (zoom level and center) are incorporated into the URL to facilitate sharing of specific map views/results. URL is also dynamically parameterized for current map center, scale, and layer state visibility, allowing others to see the same view.
* **New functionality:** Filter records by Town, SPAN, and/or E911 address without opening the grand list table
* **Changed functionality:** Convert coordinates from one system to another based on clicked-point or address input. Decimal degree (DD) input can be converted to DDM, DMS, Long-Lat, MGRS, USNG, and UTM.
* **New field:** GIS/calculated acreage, GL acreage, and percent difference between the two (absolute) calculated in parcel popups.
* **Updated field:** Town resident code is defined within pop up

**Continued functionality:**

* Toggle layers on/off, adjust transparency, link to source layer/metadata
* Toggle widgets on/off; move and/or resize widget popup
* Print capabilities
* Draw capabilities
* Basemap selection
* Measure distances and areas in unit of choice
* Select one or more features by point, rectangle, circle, lasso, or line; control over layer(s) to select from. Calculate statistics, export, zoom to, or view results in table.
* When searching by address, the result will zoom to the location but will not select the corresponding parcel/display the popup. This is because it is using a geolocator to find the location. When searching by town, SPAN, or Parcel ID the result will zoom to, select, and display the popup for the parcel since it is derived from the parcel layer. This is consistent with the existing Parcel Viewer.

## Known Issues - Beta Release
* [A](https://community.esri.com/t5/arcgis-experience-builder-questions/exb-search-widget-not-behaving/td-p/1370067) [bug](https://community.esri.com/t5/arcgis-experience-builder-questions/full-text-search-index-on-hosted-feature-layer/m-p/1346085/highlight/true#M9200) [exists](https://community.esri.com/t5/arcgis-experience-builder-questions/multiple-things-stopped-working-in-the-search/m-p/1349288#M9354) when searching by SPAN. Entering a valid SPAN in the format ###-###-##### may not produce any results in the dropdown, but hitting Enter should still return the correct parcel and zoom to the location on the map.
* Searches do not select the queried parcel - only zoom and recenter to them. This is being investigated.
* The Frequently Linked Questions hyperlink in the side panel to open the FAQ widget appears broken due to URL parameterization. This is being investigated.

## Data Sources and Dependencies
| Layer                           | Dependency | Item Name                                                                 | Spatial Ref | Publisher      | URL                                                                                                                                           | Hosting | Standalone Open Data | Life Cycle Supported |
|---------------------------------|------------|---------------------------------------------------------------------------|-------------|----------------|-----------------------------------------------------------------------------------------------------------------------------------------------|---------|----------------------|----------------------|
| Town Parcel Data Status         | Indirect   | FS_VCGI_OPENDATA_Cadastral_VTPARCELS_poly_DataStatus_SP_v1                | SP          | Services_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_OPENDATA_Cadastral_VTPARCELS_poly_DataStatus_SP_v1/FeatureServer/0 | AGO     | Yes                  | No                   |
| Town Parcel Data Status (ETL'd) | Direct     | FS_VCGI_Parcel_Program_Town_Mapping_Status_PRD_FME_v2                     | WM          | Publisher_VCGI | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/ArcGIS/rest/services/FS_VCGI_Parcel_Program_Town_Mapping_Status_FME_PRD_v2/FeatureServer        | AGO     | No                   | No                   |
| Property Transfers              | Direct     | VT Property Transfers                                                     | WM          | Services_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_OPENDATA_Cadastral_PTTR_point_WM_v1_view/FeatureServer             | AGO     | Yes                  | No                   |
| Parcels                         | Direct     | FS_VCGI_VTPARCELS_WM_NOCACHE_v2                                           | WM          | Services_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_VTPARCELS_WM_NOCACHE_v2/FeatureServer                              | AGO     | Yes                  | Yes                  |
| Address Points                  | Direct     | FS_VCGI_OPENDATA_Emergency_ESITE_point_SP_v1                              | SP          | Services_VCGI  | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/ArcGIS/rest/services/FS_VCGI_OPENDATA_Emergency_ESITE_point_SP_v1/FeatureServer/0               | AGO     | Yes                  | No                   |
| Survey Footprints               | Direct     | VT Data - Locations of Surveys Accessible via Vermont Land Survey Library | WM          | Publisher_VCGI | https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_Land_Survey_Library_reviewed_v2_1/FeatureServer                    | AGO     | Yes                  | No                   |
| Best Of Imagery                 | Direct     | VT Service - Best of Color Imagery                                        | WM          | Services_VCGI  | https://maps.vcgi.vermont.gov/arcgis/rest/services/EGC_services/IMG_VCGI_CLR_WM_CACHE/ImageServer                                             | AGS     | Yes                  | Yes                  |
| Basemap                         | Direct     | Human Geography Detail, Labels                                            | WM          | ESRI           | https://basemaps.arcgis.com/arcgis/rest/services/World_Basemap_v2/VectorTileServer                                                            | AGO     | Yes                  | N/A                  |