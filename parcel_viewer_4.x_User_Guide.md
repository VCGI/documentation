# Vermont Parcel Viewer 4.x User Guide
## How do I use the Vermont Parcel Viewer?
The Parcel Viewer includes a variety of tools to query, format, and explore Vermont parcel data. See each section below to learn about specific features and widgets. Each widget can be displayed or hidden by clicking the respective green button along the bottom of the page.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_overview.jpg)

## Search
Use the search bar in the upper right to find a parcel using the address, SPAN, or parcel ID. **The search tool will automatically query multiple layers; use the dropdown to select only certain layers/geocoders if desired.** The map will zoom to the parcel once a search result is selected. Users can also search by municipality to retrieve information about the respective parcel maintenance practices (mapping vendor, date of last update, associated regional planning commission, etc.). NOTE: a bug exists  when searching by SPAN. Entering a valid SPAN in the format ###-###-##### may not produce any results in the dropdown, but hitting Enter should still return the correct parcel and zoom to the location on the map.

## Legend
The legend widget is displayed automatically upon opening the Viewer in the lower left sidebar. **It includes any layers that are toggled on (see below) and visible at the current zoom scale.** When the Viewer is first opened, the legend shows how recently the GIS data have been updated for each municipality. As one zooms in to the map, this layer turns off and the parcel polygons are displayed. As other layers turn on they will also appear in the legend.

## Layers
Several layers in the Viewer are formatted to automatically become hidden or visible depending on the map scale, but users can also control layer visibility as desired at any scale through the Layers widget. Expand a layer by clicking […] to control layer transparency and access a link to the layer source/metadata. Some layers (Property Transfers, E911 Address Points, Inactive Parcels and labels, Surveys) will remain hidden at all scales unless specifically toggled on by the user.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_layers.jpg)

## Grand List Table
Use the Grand List Table widget to query, filter, zoom to, and/or export Grand List records. The table includes all fields from the annual Grand List joined to the parcel data, as well as fields related to parcel geometry. Select records by clicking parcels in the map, or use the ‘Set Filter’ button under ‘More Actions’ to write a query and select multiple records. For example, to filter the table to show only records in the town of Barnet:

(Image forthcoming)

Records can be exported to another format for use outside of the Viewer. Under ‘More Actions’ select Export and the format desired for all, selected, or filtered records. The ‘More Actions’ menu also includes a Statistics function, which can be applied to the entire dataset or filtered records for numeric fields (e.g., calculating statistics for the Listed Real Value for all parcels in Barnet will return the record count, minimum, maximum, sum, average, and standard deviation).

## Convert Coordinates
Use this widget to click a location on the map and obtain the coordinates in a variety of formats. Users can also enter the coordinates in the ‘Input’ bar to zoom to the location. The default Input units are decimal degrees, but this can be adjusted through the settings in the upper right.

(Image forthcoming)

## Filter Records
The Filter Records widget offers an alternative to filtering and exporting records similar to the Grand List table. Choose to filter based on Town, SPAN, and/or E911 address. Results can be viewed on the map, in a table, and/or exported to another format for use outside of the Viewer. Statistics can also be calculated on the filtered dataset. 

## Select
The Select widget allows users to select one or more parcels using various extents (rectangle, circle, lasso), lines, or point locations. Results can be refined to specific layers and selected records can be viewed in a table, viewed on the map, exported to another format, or used to calculate statistics.

## FAQs
For quick reference to some commonly asked questions, refer to the FAQ widget. Many questions include links to additional resources and documentation regarding parcel data. Contact information for the Parcel Program is also provided.

## Print
Use the Print widget to create simple .png maps using layers in the Parcel Viewer. Toggle layers on/off as desired, specify the paper size, and select the current extent, current scale, or a customized scale for the map. Enter additional details as needed and select Print. Under the ‘Print result’ tab a link will appear for your customized .png output, which can be saved for use outside the Viewer. Note: The Parcel Viewer is not intended to fulfill large-format printing needs.

## Draw
The Draw widget allows users to add shapes and markers to the Viewer. Items drawn in the Viewer cannot be saved and will disappear once the application is refreshed, but can be preserved for reference by printing the map to a .png file (see Print widget above). 

## Basemap Gallery
The Basemap Gallery widget allows a user to select ESRI basemaps for different mapping needs. Be sure to check different basemaps with different levels of transparency on select layers, such as the Imagery - VCGI Color layer.

# Other Features
## What information is in the popup when I click on a parcel?
* The popup contains several fields of interest from the Grand List, including the SPAN, parcel ID, property description, property type, listed real value, and ownership. 
* 'GIS Year’ indicates the year of the most recent parcel geometry update for the town. ‘Grand List Year’ is the annual Grand List the parcel data are currently joined to and is consistent for all parcels statewide.
* The ‘Total Acreage’ field shows the acreage of the parcel as listed in the Grand List (i.e., deeded acreage as listed in the town records), the acreage of the parcel as calculated with GIS based on the drawn extent, and the percent difference between the two values. It is common for discrepancies to exist between the Grand List and GIS acreage, however, substantial differences may warrant further review or revision of the deed description and/or the parcel geometry. In the Lister and Assessor Handbook (2021), the Vermont Department of Taxes specifies that “a survey done by a Vermont registered land surveyor is entitled to the greatest evidentiary weight, followed by a tax map, and, finally, by a recorded deed” (see page 33). When evaluating parcel data it is important to remember that parcel extents shown in the Viewer are representative, generalized, and not reflective of official survey boundaries. See more details here.
* All property (parcels) transferred by deed since January 2019 will include the closing date, seller, and buyer information in the popup. Transfers occurring since the annual Grand List currently joined to the parcel geometry, if any, are also specified. This information indicates changes in ownership that are not yet included in the annual Grand List. 
* Any surveys from the Vermont Land Survey Library associated with the parcel will be included with the survey type, date, and surveyor. A link to a pdf of the survey will also be provided. Occasionally, particularly in the case of subdivisions, a related survey will not be listed in the popup. To verify whether one or more surveys exist for a parcel, toggle on the ‘Surveys – Vermont Land Survey Library’ layer to see approximate survey footprints. When this layer is toggled on, the popup will include additional pages linking directly to any available pdfs.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_surveys.png)

## How do I measure distances/areas in the Viewer?
The Viewer includes a measurement tool for both linear distance and area. Simply select the ruler icon in the upper left and click on the map to specify a distance or polygon. Both options allow the user to select from various units.
## Can I zoom to my location?
Yes. Use the ‘Find My Location’ icon in the lower right to quickly zoom to your location. Note: your browser must have permission to know your location for this function to work. 
