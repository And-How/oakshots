Sometimes you may have data of your own, or from aother source that you don't want to or cannot legally/safely publish with the full location information. Crime are a common example- many police departments just don't feel comfortable publishing the full raw address and coordinates of crime reports even though they are public records. Commonly these get rounded to block levels- such as publishing 1500 blk of MacArthur rather than the real 1501 address. That can be messy and time consuming though.

Say you have such a dataset, like Oakland's ShotSpotter locations, just say. But Oakland PD seems to be more ocmfirtable publishing it at Washington D.C. levels of accuracy , say 100 meters. Even though there is a street address included, but for ShotSpotter we know that is a close match only an not an exact shot location.  There are many way to strip down the accuracy of these data to publish and not upset the agency who gave out the data. This is one way.

Open your data in Excel or similar tools, you have X/Y coordinates already included which is nice, these are State Plane Projected numbers- in US feet as the measurement units. This is because most city data is managed for local use and State Plan is best for that. But the interwebs wants global scale coordinates- Lats and Longs. We'll get there. First we need to fuzz up our data to not piss off anyone seeing the points land on their house too often right.

Excel has a neat fuction called <strong> MROUND </strong>. It lets you round a number to the nearest multiple of something- in our case we're going to round the X/Y columns from single digit accuracy to the nearest 100 feet- bigger than a housing lot and this no longer clearly tied to the individual property the data points too but not so fuzzy that the maps start to show a false reality.  We use the formula of 
  =MROUND(x column,100) 
and we repeat it for the Y field. Now our data is rounded up or down to the 100 foot mark. Ww could do 

So the original coordinate that started like this:
6076697	2101583

Becomes
6076700	2101600

What if we wanted more fuzz? Use 200 or 300 or 1,000 as your rounding value!

Using 200 gives:
6076600	2101600

There is room for slip ups here- this all assumes a nice real, random set of coordinates from the real world. If there was a systematic problem with the raw data it's possible this calculation would not mask as well as it should- we need to visually compare some points in a GIS tool to make sure, or do some difference calculations between the old and new coordinates and statistically evaluate the differences. Let's not do that now.

So we now have X/Y in a rough coordinate that isn't accurate to the household level- good for public use in this case. (not good enough for public health data btw). We want to map it, so we need to reproject into a web format. We could use GDAL command line to do it, but the simple way that helps you see what the point of this is as follows.

Open the csv file in [QGIS](http://qgis.org), using the **Add Text File** tool. It should detect that you have coordinates in the file, and will ask you to confirm the projection next- we pick NAD 83 California State Plane III, just because we now that's right... 
![image](/images/import.png?raw=true)

![image](/images/coords.png


Our map view will show to spots, our city is the top rght cluster, the bottom left is Null Island, I've been there and gotten the t-shirt. Don't go there.

If you open your attributes you'll see your rounded coordinates. If you click on the Globe symbol on bottom right you'll be able to select a new projection and use On-The-Fly projections, pick **WGS84** or Web Mercator Auxilary Sphere, either is fine for this level of accuracy. If you want to know this stuff then be prepared- projections are not for the feint of heart.

Now our map is displaying latitudes and Longitudes, yay. Load a plugin called Table Manager now. From the Plugins menu.

From the Vector menu select geometry **Tools>Export/Add Geometry**

Pick your layer, then select the Project CRS form the dropdown- this is telling the app to use WGS 84, then hit OK or click the box to create a new shapefile from this- if you do that the result will be a shapefile that has the original fuzzed cooridnates in X/Y and your new web ready lat/long values based upon those fuzzed coordinates. Now you can load this into TileMill, CartoDB or wherever internet maps lead you! And you're not dumping all the points in the correct spot. Which is sad, but perhaps wise in this case?

