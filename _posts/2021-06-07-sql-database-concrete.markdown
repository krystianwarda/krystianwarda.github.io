---
layout: post
title: MySQL database designed for manufacturing concrete products in factory
date: 2021-06-07 13:32:20 +0300
description: Database dedicated for manufacturing and controlling purposes
img: cement_pouder.jpg # Add image post (optional)
fig-caption: SQL, python # Add figcaption (optional)
tags: [mySQL, python]
---

Database made using mySQL dedicated for manufacturing process of concrete elements in factory. Controlling functions available through integrating database with python.



### 1. Introduction

Database was created using mySQL.

MySQL database scheme:
<br>
 <input type="image" src="/assets/img/sql_concrete_scheme.PNG" alt="Submit" width="600" height="400"> 
<br>


First chain of this process is an orders list, containing information about project, Client, date of the order, number of order and volume of needed concrete in cubic meters.


<br>
![prefab_describe]({{site.baseurl}}/assets/img/sql_zlecenia_tabela.PNG)
<br>

Depending on the recipe for concrete, different ratio between the ingredients has to be applied to recive the conrete with expected properties. Recipes with different ingredients ratio needed to produce 1 cubic meter of concrete are shown in the table. 

<br>
![prefab_describe]({{site.baseurl}}/assets/img/sql_receptury_head.PNG)
<br>


Calculating the usage of ingredients are key for controlling purposes of tankage. Cement silo and other tanks for ingredients have to be regulary refilled.



<br>
![prefab_describe]({{site.baseurl}}/assets/img/sql_raport_surowce.PNG)
<br>


### 2. Graphs from database using python

Graph presented below shows demand on concrete [m3] from Clients with a breakdown into product type

<br>
 <input type="image" src="/assets/img/zlecenia.png" alt="Submit" width="650" height="400"> 
<br>


Graph presented below shows production volume in m3 of concrete, broken down by products
<br>
 <input type="image" src="/assets/img/wielkosc_produkcji.png" alt="Submit" width="650" height="400"> 
<br>


As stated before ingredients for concrete production are crucial and have to be routinely refilled. Purpose of graph presented below is the control function for cement silo. The graph is showing the process of cement consumption and refill. 

<br>
 <input type="image" src="/assets/img/sql_cement_zbiornik.png" alt="Submit" width="650" height="400"> 
<br>





