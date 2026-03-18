# Vermont Parcel Viewer 4.x Arcade Code Documentation

This page documents [arcade code](https://developers.arcgis.com/arcade/) in use and related aspects as built in the Vermont Parcel Viewer v.4.x maintained by the [Vermont Center for Geographic Information (VCGI)](https://vcgi.vermont.gov). **Certain app functionality depends on this code, and is only found in the application and/or its referenced map products (not in the data sources themselves).** Advanced users looking to recreate this functionality may refer to this documentation.

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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_active_labels2.jpg)

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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_inactivelabels.jpg)

## Town Parcel Data Status Labels - GIS Date
This script is entered in the "Labels Features" function of the Town Parcel Data Status layer.
```javascript
$feature.Town+ " GIS Date: " +TextFormatting.NewLine+ $feature["GISDate"]
```
It returns a label that appears like this:

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_townstatuslabel.jpg)


## Address Points - E911 Labels - Primary Address + Town Name
This script is entered in the "Labels Features" function of the Address Points - E911 layer.

```javascript
$feature["PRIMARYADDRESS"]+TextFormatting.NewLine+$feature["TOWNNAME"]
```
It returns a label that appears like this:

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_e911labels.jpg)


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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_townparcelstatus_symbology.jpg)


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


## Parcel Summary with PTTRs and Current Use

This script summarizes ownership and status (latest geometry update) for a parcel at the top of the pop-up. It also includes conditional statements related to the presence of a property transfer and whether the parcel is enrolled in Current Use. If a property transfer has taken place since the latest Grand List, there is a specification that the ownership information may have changed. If enrolled in Current Use, the summary describes which type (agriculture, forest, or both). It is ```{expression/expr10}```.


```javascript
var parcelFeature = $feature;

//References PTTR point layer within the map
var transferLayer = FeatureSetByName($map, "Vermont Property Transfers");

//References Current Use table within the map - including tax year and agriculture and forest acreage fields
var cuTable = FeatureSetByName($map, "VT Data - Current Use Program Properties", ["SPAN", "TAX_YEAR", "TOT_AG_ACR", "TOT_FOR_AC"], false);

// Fetch the tax year from the dataset regardless of the specific parcel match
var cuGlobal = First(cuTable);
var cuDatasetYear = DefaultValue(cuGlobal.TAX_YEAR, "Unknown Year"); //if year is not found

// Safe Variable Definitions (DefaultValue prevents the script from returning null if data is missing)
var propSt = DefaultValue($feature.E911ADDR, "");
var propCity = DefaultValue($feature.TOWN, "");
var GLowner1 = DefaultValue($feature.OWNER1, "");
var GLowner2 = DefaultValue($feature.OWNER2, "");
var GLyear = DefaultValue($feature.GLYEAR, "");
var GISYear = DefaultValue($feature.YEAR, "");

//Annual Grand List date; ***update year with new GLs as available*** (*purposefully set to March, seems to exclude April from below if set to 4/1*)
var GLDate = Date(2024, 3, 1)

// --- 1. OWNERSHIP & GIS SECTION ---
var ownerTxt = "";
if (IsEmpty(GLowner2)) {
    ownerTxt = GLowner1;
} else {
    ownerTxt = GLowner1 + " and " + GLowner2;
}

var result = "The property at " + propSt + " in " + propCity + " is owned by " + ownerTxt + ". Parcel geometry was last updated in " + GISYear + ".";

// --- 2. PROPERTY TRANSFER SECTION ---
var transferNote = "";

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
        if (firstSpan == null) {
          firstSpan = transfer.SPAN;
        } else if (firstSpan != transfer.SPAN) {
          //Flag to indicate there are multiple PTTR SPANs within a single parcel (indicates multi-SPAN)
          uniqueSpan = false;
        }
        
        recordsAfterDate = "A property transfer has occured for this parcel since the current statewide Grand List (" + GLyear + ") and ownership may have changed. See property transfer details below.";
      }
    }

    //If there are multiple PTTR SPANs within a single parcel (i.e., it is multi-SPAN)
    if (!uniqueSpan) {
      transferNote = "This is a multi-SPAN parcel. Multiple properties, owners, and transfers may exist within this parcel.";
    } else if (recordsAfterDate != "") {
      transferNote = recordsAfterDate;
    } else {
      transferNote = "There is no record of a property transfer for this parcel since the current statewide Grand List (" + GLyear + ").";
    }

  } else {
    //No transfer of this parcel at all (since 2019)
    transferNote = "There is no record of a property transfer for this parcel since the current statewide Grand List (" + GLyear + ").";
  }
} else {
  //Parcel is not a PROPTYPE = PARCEL feature
  transferNote = "This feature is categorized as " + DefaultValue(parcelFeature.PROPTYPE, "Unknown") + ".";
}

// Add Transfer note with a carriage return
result += TextFormatting.NewLine + TextFormatting.NewLine + transferNote;

// --- 3. CURRENT USE SECTION ---
// Using Grand List SPAN from the parcel feature to match against SPAN in the table
var currentSpan = $feature.GLIST_SPAN;

var cuNote = "";
// Only attempt filter if currentSpan is not null to avoid errors
if (!IsEmpty(currentSpan)) {
    var cuMatch = Filter(cuTable, "SPAN = @currentSpan");

    if (Count(cuMatch) > 0) {
        var cuRecord = First(cuMatch);
        var taxYr = cuRecord.TAX_YEAR;
        var agAcres = cuRecord.TOT_AG_ACR;
        var forAcres = cuRecord.TOT_FOR_AC;
        
        // Determine Land Type logic
        var landType = "enrolled"; 
        if (agAcres > 0 && forAcres > 0) {
            landType = "enrolled for Agriculture and Forest";
        } else if (agAcres > 0) {
            landType = "enrolled for Agriculture";
        } else if (forAcres > 0) {
            landType = "enrolled for Forest";
        }

        cuNote = "As of " + taxYr + " this parcel is " + landType + " in the Current Use program. See additional details below.";
    } else {
        // Append the sentence for NOT ENROLLED properties using the TAX_YEAR from the Current Use dataset
        cuNote = "As of " + cuDatasetYear + " this parcel is not enrolled in the Current Use program.";
    }
} else {
    // Handling cases where GLIST_SPAN is missing from the parcel record
    cuNote = "As of " + cuDatasetYear + " this parcel is not enrolled in the Current Use program.";
}

// Add Current Use note with a carriage return
result += TextFormatting.NewLine + TextFormatting.NewLine + cuNote;

return result;
```

The following text block is an example of a popup for a parcel that has had a property transfer since the current Grand List and is enrolled in the Current Use program:

>The property at 1950 BRAZIER RD in EAST MONTPELIER is owned by BRAZIER THOMAS H and BRAZIER ANN M. Parcel geometry was last updated in 2022.
>
>A property transfer has occurred for this parcel since the current statewide Grand List (2024) and ownership may have changed. See property transfer details below.
>
>As of 2025 this parcel is enrolled for Agriculture in the Current Use program. See additional details below. 



## Ownership (Annual Grand List)
This script does organizes and presents ownership fields of the grand list into one pop up field.
It is ```{expression/expr0}```.

```javascript
Concatenate([$feature.OWNER1,$feature.OWNER2], ', ') +TextFormatting.NewLine
+$feature.ADDRGL1 +TextFormatting.NewLine 
+Concatenate([$feature.CITYGL,$feature.STGL,$feature.ZIPGL], ', ')
```

It returns the following info in a pop-up:

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_ownership_annualGL.jpg)


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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_rescode.jpg)


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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_proptransferpopupup.jpg)


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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_ownershipsinceGL.jpg)


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

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_surveyavailability.jpg)


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
It returns the following info in a pop-up with the on-the-fly calculated percent difference between listed (GL) and drawn (GIS) acres for the selected parcel:

![](https://files.vcgi.vermont.gov/logo/documentation-public/parcelviewer4/arcade_parcelviewer_percentdifference.jpg)


## Current Use Enrollment
This script indicates whether a parcel is enrolled in the Current Use Program by matching SPANs in the Current Use table (for the year specified) with Grand List SPANs in the parcel dataset. It also returns the enrollment type (agriculture, forest, or both), the total enrolled acreage, and total parcel acreage, and the percent of the parcel enrolled. If the Grand List SPAN is not found in the Current Use dataset the status is noted as not enrolled. It is ```{expression/expr8}```.

```javascript
// 1. Added 'TAX_YEAR', 'TOT_AG_ACR', and 'TOT_FOR_AC' to the request
var cuTable = FeatureSetByName($map, "VT Data - Current Use Program Properties", ['SPAN', 'ENROLL_YR', 'TOT_ENR_AC', 'TOT_ACRES', 'TAX_YEAR', 'TOT_AG_ACR', 'TOT_FOR_AC']);

// 2. Filter the table to find a matching SPAN
var parcelSpan = $feature.SPAN;
var filterExpr = "SPAN = '" + parcelSpan + "'";
var match = First(Filter(cuTable, filterExpr));

// 3. Logic if a match is found
if (!IsEmpty(match)) {
    var enrolledAcres = match.TOT_ENR_AC;
    var totalAcres = match.TOT_ACRES;
    var agAcres = match.TOT_AG_ACR;
    var forAcres = match.TOT_FOR_AC;
    
    // Check if Enrollment Year is null
    var enrollYrDisplay = match.ENROLL_YR;
    if (IsEmpty(enrollYrDisplay)) {
        enrollYrDisplay = "Not available";
    }
    
    // Determine Land Type (Ag, Forest, or Both)
    var landType = "None Listed";
    if (agAcres > 0 && forAcres > 0) {
        landType = "Agriculture and Forest";
    } else if (agAcres > 0) {
        landType = "Agriculture";
    } else if (forAcres > 0) {
        landType = "Forest";
    }

    // Calculate percentage safety check
    var percentText = "Calculation unavailable";
    if (totalAcres > 0 && !IsEmpty(totalAcres)) {
        var pct = (enrolledAcres / totalAcres) * 100;
        percentText = Round(pct, 2) + "%";
    }

    // 4. Construct return string with land type and hyperlink
    return "Status: Enrolled (" + match.TAX_YEAR + ")" + TextFormatting.NewLine +
           "Land Type: " + landType + TextFormatting.NewLine +
           "Enrollment Year: " + enrollYrDisplay + TextFormatting.NewLine + 
           "Enrolled Acres: " + enrolledAcres + TextFormatting.NewLine +
           "Total Property Acres: " + totalAcres + TextFormatting.NewLine +
           "Percent Enrolled: " + percentText + TextFormatting.NewLine ;
} else {
    return "Status: Not Enrolled";
}
```

## Link to Current Use data
This script displays the link to the full Current Use data table. The link is only displayed if the selected parcel is enrolled. It is ```{expression/expr9}```. 

```javascript
// 1. Access the Current Use table (we only need the SPAN for the check)
var cuTable = FeatureSetByName($map, "VT Data - Current Use Program Properties", ['SPAN']);

// 2. Filter the table to find a matching SPAN
var parcelSpan = $feature.SPAN;
var filterExpr = "SPAN = '" + parcelSpan + "'";
var match = First(Filter(cuTable, filterExpr));

// 3. If a match is found, return the URL; otherwise, return nothing
if (!IsEmpty(match)) {
    return "https://geodata.vermont.gov/datasets/bc419e4f43504a398e13ec3586c0a7de_0/explore";
} else {
    return "";
}
```



***
*End of document.*
***
