# Vermont Parcel Viewer 4.x Arcade Code Documentation

This page documents [arcade code](https://developers.arcgis.com/arcade/) in use and related aspects as built in the Vermont Parcel Viewer v.4.x maintained by the [Vermont Center for Geographic Information (VCGI)](https://vcgi.vermont.gov). Certain app functionality depends on this code, and is only found in the application and/or its referenced map products (not in the data sources themselves).

# Labels
## Active Parcels Contingent Label
This script is entered in the "Labels Features" function of the Labels - Active Parcels layer.
```javascript
If (IsEmpty($feature.SPAN)) {
    return 'UNMATCHED'
}
else if ($feature.SPAN == $feature.GLIST_SPAN) {
return $feature.SPAN+TextFormatting.NewLine+$feature["DESCPROP"]
} 
else if ($feature.SPAN != $feature.GLIST_SPAN) {
return 'GIS SPAN: '+$feature.SPAN+TextFormatting.NewLine+
'GL SPAN: '+$feature["GLIST_SPAN"]+TextFormatting.NewLine+$feature["DESCPROP"]
}
```
It returns a label that appears like this:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_active_labels2.jpg)

## Inactive Parcels Contingent Label
This script is entered in the "Labels Features" function of the Labels - Inactive Parcels layer.
```javascript
If (IsEmpty($feature.PARENTSPAN)) {
    return (('INACTIVE')+TextFormatting.NewLine+('PARENT SPAN: BLANK'))
}
else if ($feature.PARENTSPAN == $feature.SPAN) {
return $feature.PARENTSPAN
} 
else if ($feature.PARENTSPAN != $feature.SPAN) {
return 'INACTIVE'+TextFormatting.NewLine+'PARENT SPAN: '+$feature.PARENTSPAN
}
```
It returns a label that appears like this:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_inactivelabels.jpg)

## Town Parcel Data Status Labels - GIS Date
This script is entered in the "Labels Features" function of the Town Parcel Data Status layer.
```javascript
$feature.Town+ " GIS Date: " +TextFormatting.NewLine+ $feature["GISDate"]
```
It returns a label that appears like this:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_townstatuslabel.jpg)

## Address Points - E911 Labels - Primary Address + Town Name
This script is entered in the "Labels Features" function of the Address Points - E911 layer.

```javascript
$feature["PRIMARYADDRESS"]+TextFormatting.NewLine+$feature["TOWNNAME"]
```
It returns a label that appears like this:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_e911labels.jpg)

# Symbology
## Town Data Status Layer - GIS Recency by Town
This script is entered in the Styles function of the Town Data Status Layer. It calculates an expression called "GIS Recency by Town" that is then symbolized to depict GIS Recency.
```javascript
//areas which are updated 6 months ago or less belong in the first group
if (DateDiff(Now(), Date($feature.GISDate), 'days') <= 182) {
     return "Geometry updated within the last 6 months" }
//areas which are updated more than 6 months but less than 12 months ago belong in the second group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 182 &&
DateDiff(Now(), Date($feature.GISDate), 'days') <= 365) {
    return "Geometry updated between 6 and 12 months ago" }
//areas which are updated more than 1 year but less than 2 years ago belong in the third group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 365 &&
DateDiff(Now(), Date($feature.GISDate), 'days') <= 730) {
    return "Geometry updated between 1 and 2 years ago" }
//areas which are updated more than 2 years but less than 3 years ago belong in the fourth group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') > 730 &&
DateDiff(Now(), Date($feature.GISDate), 'days') < 1095) {
    return "Geometry updated between 2 and 3 years ago" }
//areas which are updated 3 years ago or more belong in the fifth group
else if (DateDiff(Now(), Date($feature.GISDate), 'days') >= 1095) {
     return "Geometry updated more than 3 years ago" }
//areas which do not satisfy any of the given conditions belong in the fourth group
else {
     return "None of these conditions" }
```

It returns symbology that can be styled to appear like this:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_townparcelstatus_symbology.jpg)

Hex values for the Town Data Status symbology are as follows:

```css
#718E81 /*Geometry updated within the last 6 months*/
#a7c8b8 /*Geometry updated between 6 and 12 months ago*/
#D4E7CD /*Geometry updated between 1 and 2 years ago*/
#DFC27D /*Geometry updated between 2 and 3 years ago*/
#A67039 /*Geometry updated more than 3 years ago*/
```

# Pop-Ups
These functions are executed via *Attribute Expressions* on the Parcels - Active layer, may reference other layers in the same map, and are displayed within the pop up environment only (not within attributes).

## Ownership (Annual Grand List)
This script does organizes and presents ownership fields of the grand list into one pop up field.
It is ```{expression/expr0}```.

```javascript
Concatenate([$feature.OWNER1,$feature.OWNER2], ', ') +TextFormatting.NewLine
+$feature.ADDRGL1 +TextFormatting.NewLine 
+Concatenate([$feature.CITYGL,$feature.STGL,$feature.ZIPGL], ', ')
```

It returns the following info in a pop-up:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_ownership_annualGL.jpg)

## Resident Ownership Code (Keyed)
This script spells out what the Resident Ownership Code (RESCODE) field value means.
It is ```{expression/expr1}```.

```javascript
If($feature.RESCODE == 'T') { 
   return 'T (Grand List owner is a Town Resident)'
} 
else if($feature.RESCODE == 'S') {
    return 'S (Grand List owner lives in state, but not in town)'
} 
else if($feature.RESCODE == 'NS') {
    return 'NS (Non-state; Grand List owner resides out of state)'
} 
else if($feature.RESCODE == 'C') {
    return 'C (Grand List owner is a Corporation)'
}
```

It returns the following info in a pop-up:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_rescode.jpg)

## Property Transfers Since 2019
This script shows **whether or not there is a property transfer occuring since 2019 on record for the selected parcel**, pulled on demand from the property transfers layer in the same map.
It is ```{expression/expr2}```.

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;

//Reference PTTR layer
var transferLayer = FeatureSetByName($map, "Vermont Property Transfers");

var pttrStart = Date(2018, 12, 31);

//Check for parcel PROPTYPE only (no ROWs, water, etc.)
if (parcelFeature.PROPTYPE == "PARCEL") {
  //Look for PTTR points that intersect parcel features
  var transferFeatures = Intersects(transferLayer, parcelFeature); 

  //If a PTTR point intersects a parcel
  if (Count(transferFeatures) > 0) {
    //Checks below for multi=SPAN parcel by looking for unqiue SPANs
    var uniqueSpan = true;
    var firstSpan = null;
    var allRecords = "";
    for (var transfer in transferFeatures) {

      if (transfer.postedDate>pttrStart){
        if (firstSpan == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {

          uniqueSPAN = false;
        }
        
        allRecords += "Closing Date: " + Text(transfer.closeDate, 'YYYY-MM-DD') + "\n";
        allRecords += "Seller: " + transfer.sellEntNam +" "+transfer.sellFstNam+" "+transfer.sellLstNam+ "\n";
        allRecords += "Buyer: " + transfer.buyEntNam +" "+transfer.buyFstNam+" "+transfer.buyLstNam+ "\n\n";
      }
    }
    if (!uniqueSpan) {
      return "NOTE: This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel.\n\n "+allRecords;
    } else if (allRecords != ""){
      return allRecords;
    }
  } else {
  //No record of transfer between 2019-present
    return "There is no record of a property transfer for this parcel between 2019 and present."; 
  }
} else {
//Note for non-parcel features
return "This feature is categorized as "+parcelFeature.PROPTYPE+". Transfer data not applicable."; 
}
```

It returns the following info in a pop-up:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_proptransferpopupup.jpg)

## Property Transfers Since Annual Grand List
This script shows **whether or not there is a property transfer occuring since the most recently joined annual grand list for the selected parcel**, pulled on demand from the property transfers layer in the same map.
It is ```{expression/expr3}```.

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;

//References PTTR point layer within the map
var transferLayer = FeatureSetByName($map, "Vermont Property Transfers");

// Adjust the date as needed per latest Grand List
var GLDate = Date(2022, 4, 1);

//Isolate only PARCEL features from parcel layer (no ROWs, water, etc.)
if (parcelFeature.PROPTYPE == "PARCEL") {
  //Look for any PTTR points that intersect (lie within) with the parcel
  var transferFeatures = Intersects(transferLayer, parcelFeature);

  //If more than 0 PTTR points are within the parcel
  if (Count(transferFeatures) > 0) {
    //Checks below for multi-SPAN parcels by looking for unique SPANs
    var uniqueSpan = true;
    var firstSpan = null;
    var recordsAfterDate = "";
    for (var transfer in transferFeatures) {
      //If the closing date is after the current GL (i.e., has been transferred since annual GL was published)
      if (transfer.closeDate > GLDate) {
        if (firstSPAN == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {
          //Flag to indicate there are multiple PTTR SPANs within a single parcel (indicates multi-SPAN)
          uniqueSpan = false;
        }
        //Report on survey info for popup
        recordsAfterDate += "Closing Date: " + Text(transfer.closeDate, 'YYYY-MM-DD') + "\n"; 
        recordsAfterDate += "Seller: " + transfer.sellEntNam +" "+transfer.sellFstNam+" "+transfer.sellLstNam+ "\n";
        recordsAfterDate += "Buyer: " + transfer.buyEntNam +" "+transfer.buyFstNam+" "+transfer.buyLstNam+ "\n\n";
      }
    }

    //If there are multiple PTTR SPANs within a single parcel (i.e., it is multi-SPAN)
    if (!uniqueSPAN) {
      return "NOTE: This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel.\n\n" + recordsAfterDate;
      //If there is only one SPAN for the parcel, find all those between the current Grand List and present
    } else if (recordsAfterDate != "") {
      return recordsAfterDate;
      //No transfers between current GL and present
    } else {
      //UPDATE YEAR following annual GL join
      return "There is no record of a property transfer for this parcel since the current statewide Grand List (2022).";
    }
  } else {
    //No transfer of this parcel at all (since 2019)
    return "There is no record of a property transfer for this parcel since the current statewide Grand List (2022).";
  }
} else {
  //Parcel is not a PROPTYPE = PARCEL feature
  return "This feature is categorized as "+parcelFeature.PROPTYPE+". Transfer data not applicable.";
}
```

It returns the following info in a pop-up, and in this case, showing ownership that isn't yet reflected in the current joined annual grand list:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_ownershipsinceGL.jpg)

## Property Transfer Return Overview
This script shows an overview of relevant property transfer information for a selected parcel for display as a text paragraph atop the popup window. It dynamically displays first and second buyers and sellers, where applicable, as well as handles Multi-SPAN parcels. It is ```{expression/expr7}```.

```javascript
var parcelFeature = $feature;

//References PTTR point layer within the map
var transferLayer = FeatureSetByName($map, "Vermont Property Transfers");

var propSt = $feature.E911ADDR
var propCity = $feature.TOWN
var GLowner1 = $feature.OWNER1
var GLowner2 = $feature.OWNER2
var GLyear = $feature.GLYEAR
var RealListVal = $feature.REAL_FLV
var GISYear = $feature.YEAR

//Annual Grand List date; ***update year with new GLs as available***
var GLDate = Date(2022, 4, 1)

if (parcelFeature.PROPTYPE == "PARCEL") {
  //Look for any PTTR points that intersect (lie within) with the parcel
  var transferFeatures = Intersects(transferLayer, parcelFeature);

  //If more than 0 PTTR points are within the parcel
  if (Count(transferFeatures) > 0) {
    //Checks below for multi-SPAN parcels by looking for unique SPANs
    var uniqueSpan = true;
    var firstSpan = null;
    var recordsAfterDate = "";
    for (var transfer in transferFeatures) {
      //If the closing date is after the current GL (i.e., has been transferred since annual GL was published)
      if (transfer.closeDate > GLDate) {
        if (firstSPAN == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {
          //Flag to indicate there are multiple PTTR SPANs within a single parcel (indicates multi-SPAN)
          uniqueSpan = false;
        }
        if (GLowner2 =='') {
        recordsAfterDate = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+". A property transfer has occured for this parcel since the current statewide Grand List ("+GLYEAR+") and ownership may have changed. See property transfer details below. Parcel geometry was last updated in "+GISYear+"."
        } else {
        recordsAfterDate = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+" and "+GLowner2+". A property transfer has occured for this parcel since the current statewide Grand List ("+GLYEAR+") and ownership may have changed. See property transfer details below. Parcel geometry was last updated in "+GISYear+"."
        }
      }
    }

    //If there are multiple PTTR SPANs within a single parcel (i.e., it is multi-SPAN)
    if (!uniqueSPAN) {
      if (GLowner2 =='') {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+". This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel. Parcel geometry was last updated in "+GISYear+"."
      } else {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+" and "+GLowner2+". This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel. Parcel geometry was last updated in "+GISYear+"."
      }  
    
      //var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+". This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel. Parcel geometry was last updated in "+GISYear+".";
      //If there is only one SPAN for the parcel, find all those between the current Grand List and present
    } else if (recordsAfterDate != "") {
      var result = recordsAfterDate;
      //No transfers between current GL and present
    } else {
      //UPDATE YEAR following annual GL join
      if (GLowner2 =='') {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+". There is no record of a property transfer for this parcel since the current statewide Grand List ("+GLYEAR+"). Parcel geometry was last updated in "+GISYear+"."
      } else {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+" and "+GLowner2+". There is no record of a property transfer for this parcel since the current statewide Grand List ("+GLYEAR+"). Parcel geometry was last updated in "+GISYear+"."
      }  
    }

  } else {
    //No transfer of this parcel at all (since 2019)
          if (GLowner2 =='') {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+". There is no record of a property transfer for this parcel since the current statewide Grand List ("+GLYEAR+"). Parcel geometry was last updated in "+GISYear+"."
      } else {
      var result = "The property at "+propSt+" in "+propCity+" is owned by "+GLowner1+" and "+GLowner2+". There is no record of a property transfer for this parcel since the current statewide Grand List ("+GLYEAR+"). Parcel geometry was last updated in "+GISYear+"."
      }
  }
} else {
  //Parcel is not a PROPTYPE = PARCEL feature
  var result = "This feature is categorized as "+parcelFeature.PROPTYPE+". Feature geometry was last updated in "+GISYear+".";
}

return result

```

It returns the following in a text block in a popup:

```The property at 60 OLD BROOK ROAD Middlesex was transferred by KIMBERLY CROWELL to KATHLEEN CLEMENTS and LAWRENCE M CLEMENTS on January 24, 2024. The property is 1 acres and transferred for $900000. The value of the property in the 2023 town Grand List was $445800. The SPAN is 39012110847 and the parcel ID is 00045-003.000. The seller use of the property was Secondary Residence; the buyer use is Domicile/Primary Residence.```

MULTI-SPANS: Note the number of stacked polygons associated with this SPAN in the top right, and the multi-SPAN flag included in the popup:

![Popup showing a multi-SPAN parcel and list of all transfers since 2019](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_popup_featureset_multipart_01.JPG)

## Survey Information (if Available)
This script shows **whether or not there is a submittal to the [Vermont Land Survey Library](https://landsurvey.vermont.gov/) for the selected parcel**, pulling from the Land Survey Library view layer referenced in the same map. It is ```{expression/expr4}```

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;

//Reference survey layer
var surveysLayer = FeatureSetByName($map, "Surveys - Vermont Land Survey Library"); 

// Intersect the parcel with the polygons of the surveys layer
var surveysIntersect = Intersects(parcelFeature, surveysLayer);

//Count number of attachments for a survey record
var cnt = 0 
for (var att in surveysIntersect) {
  cnt += Count(Attachments(att)) 
}

if (Count(surveysIntersect) > 0) {
  var surveyInfo = "";
  for (var survey in surveysIntersect) {
    // Check if the centroid of the survey polygon falls within the clicked parcel
    var surveyCentroid = Centroid(Geometry(survey));
    if (Contains(parcelFeature, surveyCentroid)) { //Report on survey info
      surveyInfo += "Survey Type: " + survey.survey_type + "\n";
      surveyInfo += "Survey Date: " + Text(survey.survey_date, 'YYYY-MM-DD') + "\n";
      surveyInfo += "Surveyor: " + Text(survey.surveyor_name) + "\n\n";
    }
  }
  if (surveyInfo != "") {
      //If there is more than one attachment for a survey:
      if (cnt >1){
      var surveysInfo2 = surveyInfo + "See link below. Toggle on the Surveys layer to access links to additional survey(s)."
      return surveysInfo2
    }
    else {
      return surveyInfo + "See link below. Toggle on the Surveys layer to verify whether additional surveys are available.";
    }
  } else {
    return "Unable to find survey(s) associated with this parcel. Toggle on the Surveys layer to verify whether any surveys are available.";
  }
} else {
  return "Unable to find survey(s) associated with this parcel. Toggle on the Surveys layer to verify whether any surveys are available.";
}
```

It returns the following info in a pop-up, and in this case, shows one survey for the selected parcel within the Land Survey Library:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_surveyavailability.jpg)


## Link to Survey (if Available)
This script displays the link to the **first submitted survey** for the selected parcel, if available. It does not show links to second or more surveys, if available.  It is ```{expression/expr5}```. See the above image for survey information for an example of what is returned in the popup.

```javascript
// Get the clicked parcel feature
var parcelFeature = $feature;

// Retrieve the feature layer containing the attachments
var attachmentLayer = FeatureSetByName($map, "Surveys - Vermont Land Survey Library");

// Find intersecting features from the attachment layer
var intersectingAtts = Intersects(attachmentLayer, parcelFeature);

// If there are attachments
if (Count(intersectingAtts) >0) {
  for (var survey in intersectingAtts) {
    var surveyCentroid = Centroid(Geometry(survey));
    // If survey polygon centroid falls within parcel, create link for first available attachment
    if (Contains(parcelFeature, surveyCentroid)) {
        var ObjectID = survey.OBJECTID //ID for record
        var AttachID = First(Attachments(survey)).ID //ID for first attachment associated with record
        var Part1 = "https://services1.arcgis.com/BkFxaEFNwHqX3tAw/arcgis/rest/services/FS_VCGI_Land_Survey_Library_reviewed_v2_1/FeatureServer/0/"
        var Part2 = "/attachments/"
        var link = Part1 + ObjectID + Part2 + AttachID
     }
  }
}

return link
```

## Total Acreage
This script **calculates GIS acreage for each parcel, compares it with listed acreage for said parcel in the Grand List, and displays the percent difference between the two as an absolute value**. It is ```{expression/expr6}```.

```javascript
var GLACRES = $feature.ACRESGL;
var GLACRESdec = Text($feature.ACRESGL, '#.00');
var GISACRES = AreaGeodetic($feature,'acres');
var AC_Diff_PCT = ((Abs(GLACRES-GISACRES))/((GLACRES+GISACRES)/2))*100;
Round(AC_Diff_PCT,2)

return ('Annual Grand List Acres: '+GLACRESdec+TextFormatting.NewLine+'GIS Acres: '+Round(GISACRES,2)+TextFormatting.NewLine+Round(AC_Diff_PCT,1)+'% Difference');
```
It returns the following info in a pop-up, and in this case, shows one survey for the selected parcel within the Land Survey Library:

![](https://vcgi.nyc3.cdn.digitaloceanspaces.com/documentation-assets/images/arcade_parcelviewer_percentdifference.jpg)

***
*End of document.*
***