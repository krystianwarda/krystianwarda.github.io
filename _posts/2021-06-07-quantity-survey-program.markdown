---
layout: post
title: Quantity survey program
date: 2021-06-07 00:00:00 +0300
description: Program that helps with cost-estimation. # Add post description (optional)
img: engineering.jpg # Add image post (optional)
tags: [construction, python] # add tag
---

Quantity survey program is a code written in python programming langugage, which calculate the amounts of construction material needed for particular project based on datasets from marked PDF drawings.

### 1. Introduction

Project PDF drawings are marked using Revu Bluebeam software. Bluebeam creates data that is logically organized in rows and columns divided by subject, floors, buildings, element height and more.


 <html>
<head>
<style>
div.gallery {
  margin: 10px;
  border: 1px solid #ccc;
  float: left;
  width: 20%;
}

div.gallery:hover {
  border: 1px solid #777;
}

div.gallery img {
  width: 100%;
  height: auto;
}

div.desc {
  padding: 15px;
  text-align: center;
}
</style>
</head>
<body>

<div class="gallery">
  <a target="_blank" href="/assets/img/QSP_prefab.png">
    <img src="../assets/img/QSP_prefab.png" alt="Prefab" width="600" height="400">
  </a>
  <div class="desc">Prefabricated elements</div>
</div>

<div class="gallery">
  <a target="_blank" href="/assets/img/QSB_partitions.png">
    <img src="/assets/img/QSB_partitions.png" alt="Partitions" width="600" height="400">
  </a>
  <div class="desc">Light partition walls</div>
</div>

<div class="gallery">
  <a target="_blank" href="/assets/img/QSB_finishings.png">
    <img src="/assets/img/QSB_finishings.png" alt="Finishings" width="600" height="400">
  </a>
  <div class="desc">Finishings works incl. floors, ceilings and walls</div>
</div>

<div class="gallery">
  <a target="_blank" href="/assets/img/QSB_windows.png">
    <img src="/assets/img/QSB_windows.png" alt="Windows" width="600" height="400">
  </a>
  <div class="desc">Windows divided by size</div>
</div>

</body>
</html> 

<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>

### Prefab dataframe

List of products: Balcony, Filigran Slabs, Massive Walls, Sandwich Walls, Steel Beams.


Samples rows of dataframe:
<br>
![prefab_head]({{site.baseurl}}/assets/img/QSP_prefab_head.PNG)

Summary of dataframe rows by building floor:
<br>
![prefab_describe]({{site.baseurl}}/assets/img/QSB_prefabbyfloor.png)

### Partitions dataframe

List of elements marked on drawings: Room-Room Wall, Room-Bathroom Wall, Room-Shaft Wall, Bathroom-Shaft Wall, CommonArea-CommonArea Wall, CommonArea-Shaft Wall, Acoustic Tape, Additional Plywood, Door Wood.

Samples rows of dataframe:
<br>
![prefab_head]({{site.baseurl}}/assets/img/QSP_partitions_head.PNG)

Summary of dataframe rows by building floor:
<br>
![prefab_describe]({{site.baseurl}}/assets/img/QSB_partitionsbyfloor.png)

### Finishings dataframe
List of marked rooms/elements on drawings: Apartment, Bathroom, Corridor, Holl, Technichal Room, Fan Room and Kitchen Tiles.

Samples rows of dataframe:
<br>
![prefab_head]({{site.baseurl}}/assets/img/QSP_finishings_head.PNG)

Summary of dataframe rows by building floor:
<br>
![prefab_describe]({{site.baseurl}}/assets/img/QSB_finishingsbyfloor.png)

### 2. PROJECT STRUCTURE
![project_structure]({{site.baseurl}}/assets/img/QSB_project_structure.png)

### Setup.py
Package and distribution managment placed at the root of repository, due to module package structure of the project.

### Requirements File
A pip requirements file is placed at the root of repository. It specifies the dependencies required to contribute to the project: testing, building and generating documentation.

### Modules
Modules allows separating code into parts holding related data and functionality. The division allows to expand the functions, without the risk of breaking whole program.


### 3. Results

Results are generated to Excel spreedsheets. 



