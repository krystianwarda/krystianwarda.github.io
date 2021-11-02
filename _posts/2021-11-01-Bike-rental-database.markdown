---
layout: post
title: Exploratory Data Analysis of bike rentals database with Pyspark
date: 2021-11-01 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: rower.jpg # Add image post (optional)
tags: [pyspark, python] # add tag
---

Maintenance-free bike rentals database

<br>
<br>

Code can be viewed at following github page:
<br>
<a href="
https://github.com/krystianwarda/bike_rental_pyspark">
https://github.com/krystianwarda/bike_rental_pyspark</a>
<br>



### 1. Context
This data set is taken from https://www.fordgobike.com/system-data and represents trips taken by members of the service for month of February of 2019.

### 2. Content
Data consists of info about trips taken by service's members, their types, their age, their gender, stations of starting and ending trips, duration of trips etc.

### 3. Acknowledgements
Thanks to ford gobike service for providing data online.


### 4. Data Analysis
Database on which the analysis was performed consist of following features: <br>
Trip Duration (seconds),<br>
Start Time and Date, <br>
End Time and Date, <br>
Start Station ID, <br>
Start Station Name, <br>
Start Station Latitude, <br>
Start Station Longitude, <br>
End Station ID, <br>
End Station Name,<br>
End Station Latitude,<br>
End Station Longitude,<br>
Bike ID,<br>
User Type,<br>
Member Year of Birth,<br>
Member Gender.<br>


### Distribution of the variable "member_gender"

```
df.groupBy('member_gender').agg(
    F.expr("count(member_gender)").alias("No of records")).show()
```

<br>
 <table style="width:300px">
  <tr>
    <th>member_gender</th>
    <th>No of records</th>
  </tr>
  <tr>
    <td>null</td>
    <td>0</td>
  </tr>
  <tr>
    <td>Female</td>
    <td>98621</td>
  </tr>
  <tr>
    <td>Other</td>
    <td>6299</td>
  </tr>
  <tr>
    <td>Male</td>
    <td>348318</td>
  </tr>
</table> 

<br>

### The minimum, maximum and average age of bicycle rentals

```
df = df.withColumn("Age", 2017 - df.member_birth_year).alias("Age")
df.groupBy('member_gender').agg(
    F.expr("avg(Age)").alias("AVG Age"),
    F.expr("min(Age)").alias("MIN Age"),
    F.expr("max(Age)").alias("MAX Age")
).withColumn('AVG Age', F.round(F.col('AVG Age'), 0).cast('integer')).show()
```
<br>

 <table style="width:500px">
  <tr>
    <th>member_gender</th>
    <th>AVG Age</th>
    <th>MIN Age</th>
    <th>MAX Age</th>
  </tr>
  <tr>
    <td>null</td>
    <td>null</td>
    <td>null</td>
    <td>null</td>
  </tr>
  <tr>
    <td>Female</td>
    <td>35</td>
    <td>18</td>
    <td>117</td>
  </tr>
  <tr>
    <td>Other</td>
    <td>37</td>
    <td>18</td>
    <td>117</td>
  </tr>
  <tr>
    <td>Male</td>
    <td>37</td>
    <td>18</td>
    <td>131</td>
  </tr>
</table> 

<br>

### The number of unique bikes
<br>
```
df.select("bike_id").distinct().count()
```
<br>
Result: 3673
<br>
### Number of unique bike stations

```
df.dropDuplicates(['start_station_name','end_station_name']).select('start_station_name').distinct().count()
```
Result: 272

### Which bike was rented the longest and which was the shortest during the analyzed period (and for how long)

#### MAX RENT TIME

```
aggData = df.groupBy('bike_id').agg(
    F.expr("count(bike_id)").alias("Total No of rents"),
    F.expr("avg(duration_sec)").alias("AVG Rent Time"),
    F.expr("min(duration_sec)").alias("MIN Rent Time"),
    F.expr("max(duration_sec)").alias("MAX Rent Time")
).orderBy("MAX Rent Time",ascending=False)
aggData.show(1)
```

<br>

 <table style="width:700px">
  <tr>
    <th>bike_id</th>
    <th>Total No of rents</th>
    <th>AVG Rent Time</th>
    <th>MIN Rent Time</th>
    <th>MAX Rent Time</th>
  </tr>
  <tr>
    <td>2231</td>
    <td>95</td>
    <td>2939</td>
    <td>126</td>
    <td>86369</td>
  </tr>
</table> 

<br>

#### MAX NUMBER OF RENTALS

```
aggData.orderBy("Total No of rents",ascending=False).show(1)
```

<br>

 <table style="width:700px">
  <tr>
    <th>bike_id</th>
    <th>Total No of rents</th>
    <th>AVG Rent Time</th>
    <th>MIN Rent Time</th>
    <th>MAX Rent Time</th>
  </tr>
  <tr>
    <td>68</td>
    <td>457</td>
    <td>903</td>
    <td>85</td>
    <td>22318</td>
  </tr>
</table> 

<br>

#### MIN RENT TIME

```
aggData.orderBy("MIN Rent Time",ascending=True).show(1)
```

<br>

 <table style="width:700px">
  <tr>
    <th>bike_id</th>
    <th>Total No of rents</th>
    <th>AVG Rent Time</th>
    <th>MIN Rent Time</th>
    <th>MAX Rent Time</th>
  </tr>
  <tr>
    <td>1238</td>
    <td>110</td>
    <td>1041</td>
    <td>61</td>
    <td>16288</td>
  </tr>
</table> 

<br>

#### HIGHEST AVG RENT TIME

```
aggData.orderBy("AVG Rent Time",ascending=False).show(1)
```
<br>

 <table style="width:700px">
  <tr>
    <th>bike_id</th>
    <th>Total No of rents</th>
    <th>AVG Rent Time</th>
    <th>MIN Rent Time</th>
    <th>MAX Rent Time</th>
  </tr>
  <tr>
    <td>3730</td>
    <td>8</td>
    <td>11461</td>
    <td>598</td>
    <td>79841</td>
  </tr>
</table> 

<br>

### Average duration of a single loan

```
df.select(mean('duration_sec').alias("AVG rent time")).show()
```
Result: 1099


### Between which stations there was the greatest traffic
```
df.groupBy('start_station_name', 'end_station_name').agg(
    F.expr("count(bike_id)").alias("Total No of connections"))\
.orderBy("Total No of connections",ascending=False).show(1)
```
<br>

 <table style="width:700px">
  <tr>
    <th>start_station_name</th>
    <th>end_station_name</th>
    <th>Total No of connections</th>

  </tr>
  <tr>
    <td>San Francisco Ferry Building (Harry Bridges Plaza)</td>
    <td>The Embarcadero at Sansome St</td>
    <td>3344</td>
  </tr>
</table> 

<br>

### At what time during the day were the most bicycles rented

```
df.withColumn("Ex Time of Rent", date_format('start_time', 'HH:mm'))\
.select("Ex Time of Rent")\
.groupBy('Ex Time of Rent').count()\
.orderBy("count",ascending=False).show(1) 
```

<br>

 <table style="width:200px">
  <tr>
    <th>Ex Time of Rent</th>
    <th>count</th>
  </tr>
  <tr>
    <td>17:09</td>
    <td>1208</td>
  </tr>
</table> 

<br>
### The average number of rentals for individual days of the week

```
df.withColumn("Rent day", date_format('start_time', 'EEEE')).select('Rent day')\
.groupBy('Rent day').count()\
.orderBy("count",ascending=False).show() 
```
<br>

 <table style="width:200px">
  <tr>
    <th>Rent day</th>
    <th>count</th>
  </tr>
  <tr>
    <td>Tuesday</td>
    <td>87865</td>
  </tr>
   <tr>
    <td>Wednesday</td>
    <td>87752</td>
  </tr>
   <tr>
    <td>Thursday</td>
    <td>85243</td>
  </tr>
   <tr>
    <td>Monday</td>
    <td>81410</td>
  </tr>
   <tr>
    <td>Friday</td>
    <td>81165</td>
  </tr>
   <tr>
    <td>Saturday</td>
    <td>50874</td>
  </tr>
   <tr>
    <td>Sunday</td>
    <td>45391</td>
  </tr>
</table> 

<br>


### The average number of rentals for individual months

```
df.withColumn("Rent month", date_format('start_time', 'MMMM'))\
.select('Rent month').groupBy('Rent month').count()\
.orderBy("count",ascending=False).show() 
```
<br>

 <table style="width:200px">
  <tr>
    <th>Rent month</th>
    <th>count</th>
  </tr>
  <tr>
    <td>October</td>
    <td>108937</td>
  </tr>
   <tr>
    <td>September</td>
    <td>98558</td>
  </tr>
   <tr>
    <td>November</td>
    <td>95612</td>
  </tr>
   <tr>
    <td>December</td>
    <td>86539</td>
  </tr>
   <tr>
    <td>August</td>
    <td>83292</td>
  </tr>
   <tr>
    <td>July</td>
    <td>44073</td>
  </tr>
   <tr>
    <td>June</td>
    <td>2689</td>
  </tr>
</table> 

<br>

### RDD dataDaily containing data aggregated down to the level of the day. Each day of the year (element in RDD) is to contain the following information:

- 'date' : Date 
- 'avg_duration_sec' : Average duration of rentals for the day
- 'n_trips' : liczba wypożyczeń danego dnia
- 'n_bikes' : number of rentals on a given day
- 'n_subscriber' : number of rentals made by subscribers on a given day

```
dataDaily = df.withColumn("date", date_format('start_time', 'D'))\
.groupBy('date').agg(
    F.expr("avg(duration_sec)").alias("avg_duration_sec"),   
    F.expr("count(bike_id)").alias("n_trips"),
    countDistinct("bike_id").alias("n_bikes"),
    count(when(col("user_type") == "Subscriber", True)).alias("n_subscriber"))\
.withColumn('avg_duration_sec', F.round(F.col('avg_duration_sec'), 0).cast('integer'))\
.orderBy("date",ascending=True) 

dataDaily.show(5)
```
<br>
<table style="width:600px">
<tr>
    <th>date</th>
    <th>avg_duration_sec</th>
    <th>n_trips</th>
    <th>n_bikes</th>
    <th>n_subscriber</th>
</tr>
<tr>
    <td>179</td>
    <td>1131</td>
    <td>632</td>
    <td>299</td>
    <td>530</td>
</tr>
<tr>
    <td>180</td>
    <td>1077</td>
    <td>1019</td>
    <td>375</td>
    <td>885</td>
</tr>
<tr>
    <td>181</td>
    <td>1013</td>
    <td>1038</td>
    <td>392</td>
    <td>824</td>
</tr>
<tr>
    <td>182</td>
    <td>3204</td>
    <td>475</td>
    <td>255</td>
    <td>189</td>
</tr>
 <tr>
    <td>183</td>
    <td>3082</td>
    <td>523</td>
    <td>257</td>
    <td>153</td>
 </tr>

</table> 

<br>


### Number of unique combinations of stations (x -> y == y -> x) for the day

```
df2 = df.withColumn("sl2", when(df['end_station_id'] < df['start_station_id'],  df['end_station_id'])\
.otherwise(df['start_station_id']))\
.withColumn("el2", when(df['end_station_id'] > df['start_station_id'],  df['end_station_id'])\
.otherwise(df['start_station_id']))\
.drop("start_station_id", "end_station_id")

df2_agg = df2.withColumn("date", date_format('start_time', 'D'))\
.groupBy('date').agg(collect_set(struct(col('sl2'), col('el2'))).alias("n_routes")) 

df2_agg.select("date", size("n_routes")).alias("n_routes")\
.orderBy("date",ascending=True).show(5)
```

<br>

<table style="width:200px">
<tr>
    <th>date</th>
    <th>size(n_routes)</th>
</tr>
  <tr>
    <td>179</td>
    <td>292</td>
  </tr>
   <tr>
    <td>180</td>
    <td>384</td>
  </tr>
   <tr>
    <td>181</td>
    <td>390</td>
  </tr>
   <tr>
    <td>182</td>
    <td>208</td>
  </tr>
   <tr>
    <td>183</td>
    <td>190</td>
  </tr>
</table> 

<br>



### RDD key-value `bikeDaily` containing one element for each` "bike_id" `. The values ​​in the RDD are to be lists of the total daily use of a given bike in seconds (items of the list in chronological order).

```
bikeDaily = df.withColumn("date", date_format('start_time', 'D'))\
.groupBy('bike_id').pivot('date').sum("duration_sec")

bikeDaily.select('bike_id','259','260','261','262','263',).show(3)
```
<br>

<table style="width:600px">
<tr>
    <th>bike_id</th>
    <th>259</th>
    <th>260</th>
    <th>261</th>
    <th>262</th>
    <th>263</th>
</tr>

<tr>
<td>2866</td>
<td>1304</td>
<td>null</td>
<td>null</td>
<td>596</td>
<td>1805</td>
</tr>
<tr>
<td>2122</td>
<td>13834</td>
<td>799</td>
<td>null</td>
<td>null</td>
<td>2052</td>
</tr>
</table> 

<br>