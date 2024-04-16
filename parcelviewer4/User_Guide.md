# Vermont Parcel Viewer 4.x User Guide
## How do I use the Vermont Parcel Viewer?
The Parcel Viewer includes a variety of tools to query, format, and explore Vermont parcel data. See each section below to learn about specific features and widgets. Each widget can be displayed or hidden by clicking the respective green button in the tray along the bottom of the page.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_overview_v2.jpg)

## Search
Use the search bar in the upper left to find a parcel using the address, SPAN, or parcel ID. **The search tool will automatically query multiple layers; use the dropdown to select only certain layers/geocoders if desired.** The map will zoom to the parcel once a search result is selected. Users can also search by municipality to retrieve information about the respective parcel maintenance practices, including mapping vendor, date of last update, and associated regional planning commission. NOTE: a bug exists  when searching by SPAN. Entering a valid SPAN in the format ###-###-##### may not produce any results in the dropdown, but hitting Enter should still return the correct parcel and zoom to the location on the map.

## Legend
The legend widget is displayed automatically upon opening the Viewer in the lower left sidebar. **It includes any layers that are toggled on (see below) and visible at the current zoom scale.** When the Viewer is first opened, the legend shows how recently the GIS data have been updated for each municipality. As one zooms in to the map, this layer turns off and the parcel polygons are displayed. As other layers turn on they will also appear in the legend.

## Pop Up Window
Click on a feature in the map window to see a pop up with associated information. One can move the location of the pop up window by either clicking in a different location, or by docking the pop up window to the right of the screen with the dock button.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_movepopupv2.jpg)

## Layers
The Layers widget is the left-most button in the bottom widget tray. Several layers in the Viewer are formatted to automatically become hidden or visible depending on the map scale. Users can control the visibility of most layers as desired through the Layers widget, though some layers may be unavailable at certain scales. Some layers (Property Transfers, E911 Address Points, Inactive Parcels and labels, Surveys) will remain hidden at all scales unless specifically toggled on by the user. Expand a layer by clicking […] to control layer transparency and access a link to the layer source/metadata.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_layers.jpg)

## Grand List Table
Use the Grand List Table widget to query, filter, zoom to, and/or export Grand List records. The table includes all fields from the annual Grand List joined to the parcel data, as well as fields related to parcel geometry. Select records by clicking parcels in the map, or use the ‘Set Filter’ button under ‘More Actions’ to write a query and select multiple records. For example, one could set a filter to show only parcels in the town of Athens in the table and on the map, and then export only that subset of records to a .csv file for use outside of the Parcel Viewer:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer_GrandListFilterExport.gif)

The ‘More Actions’ menu also includes a Statistics function, which can be applied to the entire dataset or filtered records for numeric fields. For example, calculating statistics for the Listed Real Value for all parcels in Athens will return the record count, minimum, maximum, sum, average, and standard deviation.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer_GrandListFilterStatistics.gif)

## Convert Coordinates
Use this widget to click a location on the map and obtain the coordinates in a variety of formats. Users can also enter the coordinates in the ‘Input’ bar to zoom to the location. The default Input units are decimal degrees, but this can be adjusted through the settings in the upper right.

## Filter Records
The Filter Records widget offers an alternative to filtering and exporting records, similar to the Grand List table, for both the Active Parcels layer and the Property Transfers layer. For Active Parcels, choose to filter based on Town, Property Type, and/or MATCHSTAT ('Unmatched' parcels are those without a corresponding SPAN in the Grand List; 'Exempt' features include roads, railroads, and water bodies). For Property Transfers, choose to filter by town and/or a closing date range. Results can be viewed on the map, in a table, and/or exported to another format for use outside of the Viewer. Statistics can also be calculated on the filtered dataset. 

The example below shows filtering the Property Transfer layer to isolate transfers that took place in Bakersfield between January 1, 2024 and March 31, 2024, as well as calculating statistics on the value paid/transferred and exporting the filtered records to a .csv:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer_FilterRecordsPTTs_1144px.gif)

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
* The ‘Total Acreage’ field shows the acreage of the parcel as listed in the Grand List (i.e., deeded acreage as listed in the town records), the acreage of the parcel as calculated with GIS based on the drawn extent, and the percent difference between the two values. It is common for discrepancies to exist between the Grand List and GIS acreage, however, substantial differences may warrant further review or revision of the deed description and/or the parcel geometry. In the [Lister and Assessor Handbook (2021)](https://tax.vermont.gov/sites/tax/files/documents/GB-1143.pdf), the Vermont Department of Taxes specifies that “a survey done by a Vermont registered land surveyor is entitled to the greatest evidentiary weight, followed by a tax map, and, finally, by a recorded deed” (see page 33). When evaluating parcel data it is important to remember that parcel extents shown in the Viewer are representative, generalized, and not reflective of official survey boundaries. See more details [here](https://vcgi.vermont.gov/resources/frequently-asked-questions/parcel-program-faqs#9).
* All property (parcels) transferred by deed since January 2019 will include the closing date, seller, and buyer information in the popup. Transfers occurring since the annual Grand List currently joined to the parcel geometry, if any, are also specified. This information indicates changes in ownership that are not yet reflected in the annual Grand List. 
* Any surveys from the Vermont Land Survey Library associated with the parcel will be included with the survey type, date, and surveyor. A link to a pdf of the survey will also be provided. Occasionally, particularly in the case of subdivisions, a related survey will not be listed in the popup. To verify whether one or more surveys exist for a parcel, toggle on the ‘Surveys – Vermont Land Survey Library’ layer to see approximate survey footprints. When this layer is toggled on, the popup will include additional pages linking directly to any available pdfs.
The example below shows clicking a parcel in Mount Holly to see that there are multiple related surveys. The survey layer is toggled on, and the link provided in the popup is used to access a pdf of one of the surveys. To see pdfs for the other surveys, flip through the popup pages using the arrows at the top of the popup; each page will list an individual survey with related information and a link to the pdf.

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer_ViewSurveys_1144px.gif)

## How do I measure distances/areas in the Viewer?
The Viewer includes a measurement tool for both linear distance and area. Simply select the ruler icon in the upper left next to the Search bar and click on the map to specify a distance or polygon. Both options allow the user to select from various units.

## Can I zoom to my location?
Yes. Use the ‘Find My Location’ icon in the lower right above the "+" button to quickly zoom to your location. Note: your browser / device must have permission to know your location for this function to work. 

## Can I zoom to a clicked feature on the map instead of using the Search bar?
Yes. In the popup for a clicked town or parcel select the "Zoom to" option to maximize the full extent of the feature. 

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer_ZoomtoClicked.gif)

## How do I view unmatched parcels?
There is a selectable layer titled "Parcels - Unmatched" in the layer list. Toggle it on and it will display those parcels without a SPAN match to the current Grand List, thus not enabling a join with its related grand list information. For example, on a mobile device:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_mobile_unmatchedparcels.png)

Users may also use the Filter Records tool to select the VT Active Parcels layer, filter a town by its unmatched records, and then zoom to / highlight them on the map for review:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/userguide_parcelviewer4x_filterunmatched.gif)
