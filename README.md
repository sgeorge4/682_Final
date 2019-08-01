Identifying Need: Gun Crimes and Shot Decection Within DC Election Wards

Stephanie George

August 4, 2019


INTRODUCTION

Time and budget permitting, many Police Departments must consider the best method to expand gunshot detection networks within their jurisdictions. Sensor networks currently in use, such as the Shot Spotter, are allocated to the areas that see the highest crime rates in an area, historically. 

The Washington DC Metropolitan Police Department intends on expanding a Shot Spotter detection network into areas that are not currently covered, but still see significant gun crimes. By utilizing Open Data DC, information showing the locations of Shot Spotter Gun Shots, a polygon layer representing boundaries of DC's 2012 election wards, and a layer of the 2017 DC Crime points can be used to analyze areas with the most need of the Shot Spotter gunshot detection network in DC. 


ANALYSIS

Gathered from Open Data DC, a website housing hundreds of datasets provided by the District of Columbia government, three valuable datasets were used to conduct this analysis. First, the Shot Spotter Gun Shots (https://opendata.dc.gov/datasets/shot-spotter-gun-shots)
dataset provides coverage for most of DC. The Shot Spotter gunshot detection system is described as an "acoustic surveillance technology that uses sensors to detect, locate and alert law enforcement agencies of potential gunfire incidents in real time" (https://opendata.dc.gov/datasets/shot-spotter-gun-shots). The next dataset used is the Ward from 2012 (https://opendata.dc.gov/datasets/ward-from-2012), a polygon layer representing the boundaries of DC's eight election wards, created as part of the DC Geographic Information System (DC GIS). Lastly, the Crime Incidents in 2017 (https://opendata.dc.gov/datasets/crime-incidents-in-2017) dataset contains a subset of locations and attributes of crime incidents reported in the ASAP (Analytical Services Application) crime report database by the DC Metropolitan Police Department. 
Two choropleth maps were made using the above three datasets. The first thematic map, shown below, renders the Gun Crimes committed per 10,000 people across DC’s eight election wards. Combined 2011 and 2012 population data found in the attribute table of the Ward from 2012 dataset were used to determine Gun Crimes per 10,000 people.  

![](GunCrimes_Map.jpg)

| Wards         | Crimes        |
| :-----------: |:-------------:| 
| 1             | 14            | 
| 2             | 7             |   
| 3             | 3             |   
| 4             | 15            | 
| 5             | 33            |   
| 6             | 19            |  
| 7             | 57            |   
| 8             | 52            |  

Before attempting to create the map, extraneous gun crime points outside the DC polygon were clipped, ensuring that all points would be located within the eight election wards.

To create this map, three new fields were added to the Wards from 2012 attribute table, and the Field Calculator was used for basic calculations. The first field contained population per 10,000 people. This was calculated by dividing each ward's population by 10,000. The second field showed total gun crimes per election ward. This was found as follows: 1) filtering the Crime Incidents in 2017 dataset to select only gun crimes and creating a new scratch layer, 2) creating a new scratch layer from the Wards from 2012 dataset to display each separately ward , and 3) using the Show the Feature Count tool to determine how many features were located in each ward. Though several extra steps were taken to find the number of gun crimes per ward, it served as a useful tool to clearly visualize election wards and the corresponding gun crime point layer. The last field combined the data found within the first two fields. The total ward population divided by 10000 was then divided by the total gun crimes to determine how many gun crimes occurred per ward per 10000 people.

The second map shown below, displaying Shooting Incidents per 10,000 People, was found similarly to the Gun Crimes but with the number of total shooting incidents detected by the Shot Spotter. The Jenks Natural Breaks classification method was used for both maps due to the large differences in shooting incidents per 10,000 people within election wards.


![](Shots_Map.jpg)

| Wards         | Shots         |
| :-----------: |:-------------:| 
| 1             | 189           | 
| 2             | 7             |   
| 3             | <1            |   
| 4             | 333           | 
| 5             | 472           |   
| 6             | 270           |  
| 7             | 1323          |   
| 8             | 1610          |   


AUTOMATION

In addition to manually analyzing ShotSpotter data using QGIS, python was also utilized to automatically import the datasets and calculate crimes and shooting incidents per 10,000 people. In order to import all three datasets, the following code was used:

                      import processing
                      crime = "E:/682Final/Crime_Incidents_in_2017.shp"
                      iface.addVectorLayer(crime, "Crime", "ogr")
                      
The first line of coding imports QGIS processing tools so that a shapefile may be saved as a new variable in the second line. The iface.addVectorLayer function adds the newly saved shapefile to the map. The second and third lines were repeated for the other two datasets, election wards and shots detected. All three datasets were saved and displayed on QGIS.


To automatically calculate gun crimes per 10,000, the previous code was recycled to save and display the dataset with three new fields added to the attribute table. 

                      iface.showAttributeTable(vlayer)
                      for field in vlayer.fields(): print(field.name())
                      
By using the iface.showAttributeTable function, the layer’s attribute table is able to be opened automatically. To get a list of the attribute names printed to the console, we can go through the layer’s fields provided by the feature() function. The second line of code can be read as: For each field in the vector layer’s fields, print the field name. Similarly, the attribute value of the layer’s features may be printed using the getFeatures() function:

                      for feature in vlayer.getFeatures(): 
                          print(feature["POPULATION"])
                          
Finally, the two lines of code above can be tweaked to divide two fields in the attribute table together as such:

                      for feature in vlayer.getFeatures():
                          print(feature["Total_GC"] / feature["POPULATION"])

The results of this calculation will be printed to the console. 


RESULTS

While Ward 7 and Ward 8 should continue to be covered by an expanded gunshot detection network based on the historically high gun crime rates, Ward 5 and Ward 6 should also be covered due to the high shooting detections and relatively high gun crime. In addition, Ward 1 should be included too. Not only does Ward 1 neighbor two of the lowest gun crime and shooting detection wards, Ward 2 and Ward 3, but the gun crime and shooting incident detection is unusually high for its location. 

There are several limitations to this analysis.
1. Classifications are assigned by ShotSpotter and represent their assessment of what kind of impulse noise occurred. There were several instances of single gunshot AND/OR firecracker detection in the Shot Spotter Gun Shots dataset. While firecrackers are illegal in DC, it can hardly be compared to an actual gunshot. By mistakenly detecting firecrackers for gunshots, the dataset will be skewed and misleading.
2. ShotSpotter does not provide coverage for the entire District of Columbia. While the pattern of gun crime and shot detect is apparant, there is a chance that it will not take the needs of other wards into account.
3. Block locations for some crime points were not automatically assigned resulting in 0,0 for x,y coordinates which results in inaccurate and incomplete data.
4. Data provided by ShotSpotter  may include duplicate incidents or unverified shots that may have occurred outside of official coverage areas. Again, this leads the dataset to be misleading and inaccurate.



