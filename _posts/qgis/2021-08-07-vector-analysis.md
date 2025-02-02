---
layout: article
title: Simple vector analysis
modified: 2021-08-07
categories: qgis
image:
  teaser: vector_analysis_teaser.png
---

Spatial queries and spatial joins are one of the basic analysis forms in GIS. In this tutorial, we will find how many UNSESCO WHC sites are every country. In some areas there are a lot of WHC sites and this makes their visualization complicated because points tend to overlap. To overcome this, we will analyze points in a grid or generate a heatmap.

#### The tutorial consists of the following steps:
- [1. Download data](#1-download-data)
- [2. Procedure](#2-procedure)
  * [2.1. Spatial join](#21-spatial-join)
  * [2.2. Spatial query](#22-spatial-query)
  * [2.3. Heatmap](#23-heatmap)

### 1. Download data
This tutorial is a continuation to [<span style="color:#0564A0">"Basic vector syling"</span>](https://kevelyn1.github.io/QGIS-Intro/qgis/vector-styling/). And we will use the same WHC sites data from [<span style="color:#0564A0">UNESCO World Heritage Sites</span>](http://whc.unesco.org/en/syndication) saved as gpkg ([<span style="color:#0564A0">"Basic vector syling"</span>](https://kevelyn1.github.io/QGIS-Intro/qgis/vector-styling/) sections 1. and 2.1).

We will also need country borders. Download the [<span style="color:#0564A0">countries</span>](https://http//www.naturalearthdata.com/download/10m/cultural/ne_10m_admin_0_countries.zip) and extract to your working folder.

**Data Sources:**
World Heritage List from [<span style="color:#0564A0">World Heritage List</span>](http://whc.unesco.org/en/syndication) and country borders from [<span style="color:#0564A0">Natural Earth</span>](https://www.naturalearthdata.com/)

### 2. Procedure
#### 2.1. Spatial join
1. Open QGIS and in the QGIS Browser Panel, locate the directory where you added the data and add files <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021.gpkg</span> and the <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries.shp</span> to QGIS.
2. Change the CRS of the project to Winkel Tripel (ESRI:54042) and save your project.
3. Let's find out which countries have the highest numbers of world heritage sites. We will use spatial join for that purpose. In the Procesing Toolbox, find tool Count points in polygon. Make layer <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> as Polygons and layer  <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span> as Points, save the output as <span style="font-family:Consolas; color:#AF1B03">whc_countries.gpkg</span> and click Run.
![image](../../images/6_points in polygon.png)
4. You will have a new layer of countries <span style="font-family:Consolas; color:#AF1B03">whc_countries.gpkg</span>. Open the attribute table of this new layer and browse horizontally to the end until you find a column named **NUMPOINTS**. This was created as a result of this analysis. Every country has now a count of WHC sites.
![image](../../images/6_numpoint attribute.png)
5. Let's visualize the countries based on the number of WHC sites in the country. Open the Symbology of the layer <span style="font-family:Consolas; color:#AF1B03">whc_countries</span>. Choose legend type as Graduated and NUMPOINTS for Value. Pick suitable Color ramp and click Classify. You may see that the ranges are quite uneven because the Equal Count has divided even number into each class and as there are very few with very high numbers then the highest class has very large range. This means that we won't really see from the map what countries have a lot of WHC sites.
![image](../../images/6_symbology countries.png)
6. Let's try to adjust the classification so that we would see better what countries have only very few WHC sites and which have a lot. Under Symbology, change the classification Mode into Natural Breaks (Jenks)[^1]. You can see that the classes changed into more equal ranges. However, there is no class with 0 WHC sites. We would still want to see also these countries, so well make small adjustments to the classes based on the automatic classification. We also need to adjust the values that are presented in the legend so that they are not overlapping and would represent the actual class values. Adjust the class values to what is shown below and click OK.
![image ](../../images/6_jenks.png)
7. We can now adjust the stroke width of the borders and make the map even nicer with some shading. Under Symbology, select all classes and then click on Symbol. Under Symbol Settings click on Simple Fill. Change the Stroke width to 0.16 mm and switch on Draw Effect. Click on the  Customize Effects button ![image ](../../images/icon_effects.png). Switch in Inner Glow and adjust the Spread and Blur to 0.4 mm, Opacity of 50% and color darker grey. Click OK.
![image ](../../images/6_stroke and inner glow.png)
![image ](../../images/6_countries final.png)
8. It is possible to perform also more complex spatial join. Let's find out when was the first WHC site registered in every country. In the Processing Toolbar, find Join attributes by location (summary). Choose <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> as Input, Join layer <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span>, Geometric predicate: intesects, Fields to summarize: date_inscribed, and Summaries to calculate: min. You can create it as temporary layer.
![image ](../../images/6_join min year inscribed.png)
9. You need to change the Symbology based on the new attribute created by join. Open the attribute table of the <span style="font-family:Consolas; color:#AF1B03">Joined layer</span> (F6). The **date_inscribed_min** has valued generated by join and they show the earliest year a WHC site was inscribed in the countries.
![image ](../../images/6_joined attributes.png)
10. Let's visualize it on a map. Open the Symbology of <span style="font-family:Consolas; color:#AF1B03">Joined layer</span> and change the type to Graduated and Value to date_inscribed_min. Select appropriate color ramp and reduce the class numbers to 4. Adjust the classification as shown below.
![image ](../../images/6_symbology joined.png)
11. As you can see, most countries have their first WHC inscribed rather in the 1980ties. You may notice that some countries are not present on the map. This means that there were no WHC sites in these countries. If you would like to make it as a proper map then you should use <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> under the <span style="font-family:Consolas; color:#AF1B03">Joined layer</span> and make the fill color of the missing countries, for example white and show in the legend that there are no WHC sites.
![image ](../../images/6_joined layer final.png)
12. The number of WHC sites per country might be somewhat misleading because bigger countries by area could have just more WHC sites because they are bigger. This is known as modifiable areal unit problem (MAUP)[^2]. There are several ways how to reduce the problem. One possibility is to normalize the number of WHC sites with the area of the country or population. The other possibility is to normalise the spatial unit (enumeration unit) itselt. We can create a regular grid (fishnet) and count the WHC sites there. Let's generate regular grid of hexagons. Hexagons are nesting together perfectly and they look cool :smirk: To create a hexagonal grid, find Create grid tool from the Processing Toolbox.
Make grid type as Hexagon, fro the grid extent choose layer <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span>, horizontal and vertical spacing 300 km, grid CRS can remain Winkel Tripel and save the file as <span style="font-family:Consolas; color:#AF1B03">whc_hex.gpkg</span>
![image ](../../images/6_generate hex.png)
13. Now we need to perform spatial join. Open Count Points in Polygon tool. Add <span style="font-family:Consolas; color:#AF1B03">whc_hex</span> as Polygons, <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span> as Points and save the file as <span style="font-family:Consolas; color:#AF1B03">whc_hex_count.gpkg</span>.
![image ](../../images/6_spatial join hex.png)
13. We can now visualize the hexagons based on the WHC sites' count. Open the Symbology of the <span style="font-family:Consolas; color:#AF1B03">whc_hex_count</span> layer. Change the type to Graduated, choose appropriate color ramp, switch the classification mode to Natural Breaks to get the base for classes. It is useful to look at the histogram of the data from the Histogram tab next to Classes. Most of the hexagons are without any WHC sites.  Therefore the class with value 0 could be separately and made invisible. It would be also useful to make the hexagons a little bit transparent to be able to see the country borders underneath. Expand the Layer Rendering and reduce make the Opacity to ca 70%.
![image ](../../images/6_symbology hex.png)
14. For the nice map design you might want to add the oceans, so the shape of the globe would become visible. You can find the ocean layer from the  [<span style="color:#0564A0">Making a map</span>](https://kevelyn1.github.io/QGIS-Intro/qgis/making-a-map/) tutorial. You can find the file <span style="font-family:Consolas; color:#AF1B03">ne_10m_ocean.shp</span> in the folder \Natural_Earth_quick_start\packages\Natural_Earth_quick_start\10m_physical\
If you don't have the Natural Earth Quickstart Kit downloaded then you can find the dataset for oceans from [<span style="color:#0564A0">Natural Earth</span>](https://www.naturalearthdata.com/downloads/10m-physical-vectors/) under Ocean.
Add the <span style="font-family:Consolas; color:#AF1B03">ne_10m_ocean.shp</span> to the map view and drag it under the other layers in the Layer panel. Asjust the Symbology of the <span style="font-family:Consolas; color:#AF1B03">ne_10m_ocean.shp</span> light blue.
![image ](../../images/6_add ocean.png)
15. You can now create a new layout and add title, legend and credentials to the map.
>:scroll:**Note**
>
*If you are considering adding north arrow and scalebar to your map then for global maps they are rather unnecessary because most people know how big is Earth and have an understanding of the scale. North arrow is also rather necessary if the north is not oriented up.*

![image ](../../images/whc_hex.png)

#### 2.2. Spatial query
Spatial queries enable to identify spatial overlap/intersection/containment of two spatial layers. For example, you can quite easily identify WHC sites that are located in United States.
16. Select United States from the <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> layer by using either button Select Features by Single Click ![image ](../../images/icon_select.png) or from the attribute table by using Select features by using an expression ![image ](../../images/icon_select features expression.png).
![image ](../../images/6_select usa.png)
17. From the Processing Toolbox, find Extract by Location tool. Make layer <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span> as input for Extract features from, check intersect as geometric predicate, make layer <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> as input for By comparing to the features from and check Select features only. You may save it as temporary layer.
![image ](../../images/6_extract by location.png)
18. As a result you will have WHC sites in US.
![image ](../../images/6_whc in us.png)

#### 2.3. Heatmap
Heatmaps are one possibility to analyze and visualize point patterns to see the clustering and dispersion.
19. QGIS enables to create heatmaps very easily under Symbology. Open the Symbology of <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span> layer and switch the symbology type into Heatmap, change the Color ramp to Magma and Radius into 4 and click OK.
![image ](../../images/6_heatmap symbology.png)
20. The symbology of the <span style="font-family:Consolas; color:#AF1B03">whc_sites_2021</span> layer will be changed into heatmap and you see the hotspots over some areas. To be able to see better where hotspots are, we should add country borders over the heatmap. You may duplicate the <span style="font-family:Consolas; color:#AF1B03">ne_10m_admin_0_countries</span> layer if you don't want to change the existing styling of the layer. Drag the layer on top of the heatmap layer in the Layer panel and open it's Symbology. Make the symbol Fill color transparent, Stroke color white and Stroke width 0.1mm.
![image ](../../images/6_borders for heatmap.png)  
21. You should now have country borders as white thin lines on top of the heatmap that enables to see where there is aggregation of the WHC sites.
![image ](../../images/6_heatmap final.png)

[^1]:You can read more about data classification from this [<span style="color:#0564A0">overview by Axis Maps</span>](https://www.axismaps.com/guide/data-classification).
[^2]:MAUP affects results when point-based measures of spatial phenomena are aggregated into districts, for example, population density or illness rates. The resulting summary values (e.g., totals, rates, proportions, densities) are influenced by both the shape and scale of the aggregation unit. (Wiki) You can watch this nice [<span style="color:#0564A0">Youtube video about MAUP</span>](https://www.youtube.com/watch?v=CISjONu-5Qg) to understand the problem a bit better.
