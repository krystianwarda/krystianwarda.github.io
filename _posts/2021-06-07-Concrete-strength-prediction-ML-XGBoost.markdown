---
layout: post
title: Concrete strength prediction - ML - XGBoost
date: 2021-06-07 00:00:00 +0300
description: You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. # Add post description (optional)
img: xgboost.JPG # Add image post (optional)
tags: [machine-learning, python] # add tag
---

The Goal of this project to is to predict the concrete strength with neural network. Project data set consist of 1030 samples of concrete, with specified ingriedients amounts used to create a sample, compressive strength and the age of the sample in the day of measurement.

Code can be viewed at following github page:
<br>
<a href="
https://github.com/krystianwarda/concrete_strength_ML">
https://github.com/krystianwarda/concrete_strength_ML</a>
<br>



### 1. Introduction
In the concrete factory to maintain quality assurance on high level, the concrete used for construction elements has to be tested continuously.
In modern prefabricated concrete elements factories process of concrete strength testing is a daily basis. 
Production technologist selects ingredients for a batch to achive particular properties of concrete.

Database on which the research was performed consist of following ingredients: <br>
Cement - measured in kg/m3,<br>
Blast Furnace Slag - measured in kg/m3, [pol., 'Żużel'], <br>
Fly Ash- measured in kg/m3, [pol., 'Popiół lotny'], <br>
Water - measured in kg/m3, [pol., 'Woda'], <br>
Superplasticizer - measured in kg/m3, [pol., 'Superplastyfikator'], <br>
Coarse Aggregate - measured in kg/m3, [pol., 'Kruszywo grube'], <br>
Fine Aggregate - measured in kg/m3, [pol., 'Kruszywo drobne'], <br>
Age - measured in days, [pol., 'Czas'], <br>
Strength - measured in N/mm2, [pol., 'Wytrzymałość'].<br>

To predict concrete compressive strength of a particular batch, XGBoost machine learning model will be used. 
XGBoost is an open-source software library which provides a regularizing gradient boosting framework.


 <p>Here is a quote from Tianqi Chen about XGBoost from Quora website:</p>
<blockquote cite="https://www.quora.com/What-is-the-difference-between-the-R-gbm-gradient-boosting-machine-and-xgboost-extreme-gradient-boosting">
The name xgboost, though, actually refers to the engineering goal to push the limit of computations resources for boosted tree algorithms. Which is the reason why many people use xgboost.
</blockquote> 


In the table presented below, first rows of database are presented:
![baza danych]({{site.baseurl}}/assets/img/0_baza_danych.png)
<br>

As it can be seen, the data is stuctured in tabular format, which allows to find the data easily and also manage it better. To get a grip on this database, summary has been presented below:
![podsumowanie bazy danych]({{site.baseurl}}/assets/img/0_podsumowanie.png)

It can be seen that Slag, Ash and Superplasticizer columns have zero values, which has to be filled using regression.<br>


<br>
Check for outliers: <br>
![Odstające wartości]({{site.baseurl}}/assets/img/outliers.png)

Based on graphs above, it can be assumed that Slag, Water, Superplasticizer, Fine Aggregate and Age columns have a few outliers. 
The data of outliers is 10% part of the total data and may be replaced with substitute mean values.

To visualize the data, multivariate analysis has beed conducted using seaborn library for python:  <br>
![Analiza wymiarowa]({{site.baseurl}}/assets/img/analiza_wielowymiarowa.png)

In the set of charts above, first observations can be made. At the same time columns with zero values are in display aswell. 
That is why those values should be impute using Linear Regression by considering other features to predict them.<br>

![Wybrane wykresy analizy]({{site.baseurl}}/assets/img/analiza_wykresy.png)

![Wybranie kluczowych wartości]({{site.baseurl}}/assets/img/wykresy_podkreslenie.png)

<br>
Above plots are showing application of Linear Regression. Imputed zero values are marked green and the rest is marked in blue. Imputed values fit the patterns.
<br>


Feature engineering<br>
Based on knowledge from construction engineering industry, two composite features has been created: water-cement ratio [pol., 'Współczynnik woda/cement'] and aggregate ratio [pol., 'Średnia kruszywa'].
<br>

![Mapa zależności składników]({{site.baseurl}}/assets/img/zaleznosc_skladnikow.png)
<br>
From the above graphs, civil engineering doctrines can be spotted:<br>
1. The strength of the concrete sample increases as the age of the sample increases.<br>
2. The water-to-cement ratio has the positive correlation to concrete sample's strength.<br>
3. The aggregate ratio have negative correlation to concrete sample's strength.<br>

Above statements are visualized below:
<br>
<span>
    <img src="/assets/img/doctrine_graphs13.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/doctrine_graphs23.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/doctrine_graphs33.png" style="width:33%">
</span>
<br>

Exploring for Gaussians<br>
![Wykresy Gaussa]({{site.baseurl}}/assets/img/wykresy_gaussa.png)

KDE plots (described as Kernel Density Estimate) visualize the Probability Density of a continuous variable or in other words, data is infact a mixture of Gaussians. 
On the below graphs correlation between age, water-to-cement ratio and aggregate to strength has been shown again. 
Looking at strength vs age graph, we can spot two seperate clusters. It can indiciate that the concrete above certain age (80-100 days) has avarage strength.


<br>
<span>
    <img src="/assets/img/gaussa_czas.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/gaussa_kruszywo.png" style="width:33%">
</span>
<span>
    <img src="/assets/img/gaussa_wc.png" style="width:33%">
</span>
<br>

Creating the model<br>
Multiple linear regression
<br>
![Model 01]({{site.baseurl}}/assets/img/01_model.png)
![Tabela 01]({{site.baseurl}}/assets/img/01_tabela.png)
![Wykres 01]({{site.baseurl}}/assets/img/01_wykres.png)

Ridge regressor <br>
![Model 02]({{site.baseurl}}/assets/img/02_model.png)
![Tabela 02]({{site.baseurl}}/assets/img/02_tabela.png)

Decide on complexity of the model <br>
Polynomial regression<br>
![Model 03]({{site.baseurl}}/assets/img/03_model.png)
![Tabela 03]({{site.baseurl}}/assets/img/03_tabela.png)
![Wykres 03]({{site.baseurl}}/assets/img/03_wykres.png)

Support Vector Regressor<br>
![Model 04]({{site.baseurl}}/assets/img/04_model.png)
![Tabela 04]({{site.baseurl}}/assets/img/04_tabela.png)

KNN (k-Nearest-Neighbor)<br>
![Model 05]({{site.baseurl}}/assets/img/05_model.png)
![Tabela 05]({{site.baseurl}}/assets/img/05_tabela.png)

Decision Tree Regression<br>
![Model 06]({{site.baseurl}}/assets/img/06_model.png)
![Tabela 06]({{site.baseurl}}/assets/img/06_tabela.png)

Random Forest Regression<br>
![Model 07]({{site.baseurl}}/assets/img/07_model.png)
![Tabela 07]({{site.baseurl}}/assets/img/07_tabela.png)

XGBoost<br>
![Model 08]({{site.baseurl}}/assets/img/08_model.png)
![Tabela 08]({{site.baseurl}}/assets/img/08_tabela.png)

XGBoost Tuned<br>
![Model 08]({{site.baseurl}}/assets/img/09_model.png)
![Tabela 08]({{site.baseurl}}/assets/img/09_tabela.png)
