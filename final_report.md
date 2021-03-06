# Predicting a COVID-19 Score Using a CNN Model from Food Images 

Team Members: Margarita Groisman, Davi Nakajima An, Rishikesh Daoo, Akash Goyal 

Team 45, Project Cluster Covid-19

## Overview

**Goal** 

Create a model that is able to label food images and classify nutrition information in relation to health outcomes for patients with Covid-19. 
![Image](images/project-infographic.png)

## Introduction & Background information

With Covid-19 posing a worldwide health risk and impacting so many people’s lives, it is essential to use every tool at our disposal to help fight against this disease. One likely important factor in immune response to the virus is nutritional status. In fact, increasing evidence is showing that poor nutritional profiles, and factors like obesity and lack of exercise are correlated with poorer outcomes when confronted with the virus (Belanger). This may be a contributor to poorer outcomes among minorities and lower-income populations with less access to nutritionally dense food. 


**Therefore:** 
* Covid-19 poses a worldwide health risk, impacting millions.
* Nutrition can be key to fighting COVID.
* Poor nutrition -> poorer immune responses 
* Seen particularly in lower-income populations.

## Methods

![Image](images/pyramid.png)

#### Dataset 1 Exploration
COVID Healthy Diet dataset - [https://www.kaggle.com/mariaren/covid19-healthy-diet-dataset/notebooks?sortBy=hotness&amp;group=everyone&amp;pageSize=20&amp;datasetId=618335](https://www.kaggle.com/mariaren/covid19-healthy-diet-dataset/notebooks?sortBy=hotness&amp;group=everyone&amp;pageSize=20&amp;datasetId=618335)

This dataset has fat quantity, energy intake (kcal), food supply quantity (kg), and protein for different categories of food (all calculated as percentage of total intake amount). It also has data on the obesity and undernourished rate (also in percentage) for comparison. The end of the datasets also included the most up to date confirmed/deaths/recovered/active cases (also in percentage of current population for each country).

Different food group supply quantities, nutrition values, obesity, and undernourished percentages are obtained from Food and Agriculture Organization of the United Nations FAO website.

**Step 1: Data Cleaning**

**Cleaning Dataset 1 with Normalization:** Removing unnecessary columns and data normalization and scaling -

- The datasets had a column that contains information about the unit of rest of the columns. So we could just remove this column and use the information it provided.

- The &#39;Population&#39; feature had values in the range 10^4 to 10^9. This could potentially introduce a bias in the model. So we did a MinMax scaling on the feature.

Before scaling -

![](images/pop_unscaled.png)

After scaling -

![](images/pop_scaled.png)

**Dealing with Null Values in Dataset 1 :** Dealing with data types and missing values -

- The &#39;Undernourished&#39; feature had percentage values in String data type. For values that were below 2.5% the dataset denotes &#39;&lt;2.5&#39;. We converted all the string values to numeric data type and changed &#39;&lt;2.5&#39; to 2 as a crude way to handle the issue.

- To check for missing values in the dataset, we plotted a heatmap.

![](images/missing_val.png)

- As we can see, there are quite a few missing values. Although there are some datapoints with missing labels. We removed these rows first, since we won&#39;t be able to use the datapoints that do not have label values.
- Then, we used K-Nearest Neighbours algorithm to impute missing values and got a heatmap with no missing values - ![](images/nomissed_val.png)

**Step 2: Data Visualizations**

To see the correlation between features in the datasets, we calculated the Correlation matrix and generated the plots.

Fat intake dataset Correlation Matrix

![](images/fat_corr.png)

Food supply in kCal dataset -

![](images/kCal_corr.png)

Protein intake dataset -

![](images/protein_corr.png)

Food Supply Quantity kg dataset -

![](images/kg_corr.png)

Our dataset provided country-wide information for the consumption and supply (in kg and kcal) of various food items, as well as statistics for the confirmed, recovered, and active cases, and deaths due to COVID-19 for each country in the study. For each piece of information we created visualizations of their correlation matrices above. Although each correlation matrix depicts a different type of measurement for each food item in a country, they are all similarly correlated. This means that the relationships between the food items are similar between the different measurements in the dataset. For this reason we have chosen to do our discussion on the Fat intake dataset Correlation Matrix.

This correlation matrix provides useful insight into both the intra-relationship between the dietary intake of different food items of people in a country as well as the relationship between the dietary intake and the statistics of each country in the COVID-19 pandemic. For instance, we can see the relatively high positive correlation of obesity with the consumption of animal products and animal fats, depicting the intra-relationship of three features. We can further note that obesity is relatively highly correlated with the country statistics for confirmed, recovered, and active cases, depicting the relationship between the dietary intake and some country-wide statistics of the COVID-19 pandemic. Therefore, the correlation matrix above gives us an idea of where our model may be drawing information from and how each feature may influence its predictions.

**Step 3: Unsupervised Learning**

We ran a k-Means clustering on our dataset to see if there are any clusters. We plotted the elbow curve for the loss score of the k-Means algorithm. We ran for a number of clusters upto 20.

We did not get any conclusive results from the elbow function for the optimal number of clusters for the datasets.

Hence, it seems that there are no clusters forming in the dataset. Below figure shows the elbow plot for one of the datasets -

![](images/elbow.png)

**Step 4: Dimensionality Reduction**

We ran Principal Component Analysis on the dataset.

99% cumulative expected variance - 22 components

Protein intake dataset -

![](images/protein_kmeans.png)

Food supply in kCal dataset -

![](images/kCal_kmeans.png)

Fat intake dataset -

![](images/fat_kmeans.png)

Food supply in kg dataset -

![](images/kg_kmeans.png)

#### Dataset 2 Exploration

[Individual Health data with dietary information CDC](https://www.kaggle.com/cdc/national-health-and-nutrition-examination-survey?select=diet.csv)

This dataset contains a large amount of information collected as part of a study by the CDC to assess the health of the American population. This dataset includes a variety of data, including a breakdown of the dietary intake of the subjects in the study, as well as a thorough assessment of their health, collecting information such as their body mass index and blood pressure. This dataset will be useful for us to make predictions of a person&#39;s health based on their dietary information.

Plot 1- Correlation Matrix for Nutrition information

![](images/image11.png)

Plot 1 Discussion-

This correlation matrix provided us a lot of useful information that helped inform further exploration of the data. Furthermore, we noticed patterns of correlation such as certain nutritional aspects, like different types of fats (monounsaturated, polysaturated, etc) tending to correlate but things like carotene finding no correlation with other nutritional pieces. This will help us further breakdown our nutritional information into food groups and clusters rather than analyzing them piece by piece. Furthermore, joining highly positively correlated nutritional information into food groups will help us link this dataset to our other datasets, which do not give such granularity of nutritional intake.

Plot 2- Correlation for Participant Examination Information

![](images/image15.png)

Plot 2 Discussion- This correlation matrix shows the correlation between four features in our dataset: BPXSY1 (blood pressure reading 1), BPXSY2 (blood pressure reading 2), BPXSY3 (blood pressure reading 3), and BMXBMI (the body mass index [BMI] of a person in the dataset). We wanted to see how well blood pressure correlated with BMI, both of the signals which will help us determine the health of a person. We found that the three blood pressure readings were highly correlated, indicating that we could only use one in our evaluation of a person&#39;s health. Furthermore, we found that the BMI of a person was not highly correlated with the blood pressure reading, which means that when evaluating the health of an individual only taking into account the blood pressure readings will not be enough, both pieces of information will be necessary.

**Plot 3 - Nutritional Information Data projected to the first two principal components and its associated Scree Plot**

![](images/nutPCA.png) ![](images/nutPCA_scree.png)

We can see that the data projected to vector space of the two principal components aggregates around a specific cluster resembling a tilted triangle. This may indicate that the nutritional diets of the people in the study are very similar to each other. Furthermore, as seen in the scree plot, the first and second principal components in the scree plot do not capture more than half of the variance in the dataset, indicating that we will need to use many more principal components to see more variance in their diets.

**Plot 4 - Examination Data projected to the first two principal components and its associated Scree Plot**

![](images/ExamPCA.png) ![](images/ExamPCA_scree.png)

The examination information of the people in the dataset (only including blood pressure and BMI features) projected to its first principal components shows a similar structure to the one in plot 3, where the data is clustered in a shape resembling a triangle. This could show that the linear projections of the data for both types of information in our dataset to their respective principal components are very similar. Furthermore, the explained variance in the first two principal components for this type of information is very high, indicating that we could use less dimensions while retaining a large percentage of the information contained in the dataset.

**Plot 5 - Nutritional Information Data projected to the first two principal components with color depicting data point&#39;s BMI rating**

![](images/2comp_PCA.png)

In this plot we color each of the data points in the projection for the Nutritional Information Data to the first two components with its associated BMI rating of being overweight (blue), normal weight (green), or underweight (red). Here we can see that the majority of the people in the study are overweight according to their recorded BMI&#39;s, but we also see that in the first two principal components, the people who are overweight, underweight, and normal weight cannot be linearly separated. This gives us some intuition to the complexity that our model for predicting health information from a person&#39;s diet must be in order to give accurate results.

#### Additional analysis on our chosen dataset (Country-wide COVID-19 and nutrition data)

**Plot 6** - Correlation matrices between nutrition and mortality

![](images/Correlation_nutrition_mortality.png)

**Plot 7** - Country COVID-19 mortality rates

![](images/co_mortality.png)

#### Training our Models

**Stage 1: FineTuned ResNet18**

*Plot 8* - Visualization of our CNN model

![](images/co_finetuned_res18.53.58 AM.png)

*Plot 9* - Training Information 

![](images/co_training_data.png)

#### Stage 2: Learning from Country-wise Nutrition Data**

*Plot 10* - Determination of our covid-scores

![](images/co_learn_by_country.png)

**Stage 3: Regression Model**

*Plot 11* - Training information from our Regression models

![](images/Screen Shot 2020-12-07 at 7.54.53 AM.png)

*Plot 12* - Fat prediction Regression 

![](images/Screen Shot 2020-12-07 at 7.55.04 AM.png)

*Plot 13* - Kcal prediction Regression 

![](images/Screen Shot 2020-12-07 at 7.55.12 AM.png)

*Plot 14* - Protein prediction Regression

![](images/Screen Shot 2020-12-07 at 7.55.18 AM.png)

**Addendum**

*Plot 15* Google Cloud AutoML

![](images/Screen Shot 2020-12-07 at 7.55.26 AM.png)

Utilized Google Cloud's AutoML to increase labeling accuracy.

## Data Analysis and Conclusions

Dimensionality Reduction

After conducting the PCA and graphing the recovered variance versus the number of components for each dataset, we decided to keep the number of components which recover 99% of the variance. We then restructured our data to only incorporate these particular components which we will then use for future analyses. The number of components retained are as follows:

Dataset 1: 22 out of 26

Dataset 2: 34 out of 46

## Next Steps 

* Increase accuracy of training model by adding more data and labeling to food images and Covid-19 health outcomes
* Continuing to train with tools that can handle larger sets of data in the cloud.
* Building an front-end for insertion of new images.
* Link the stages




# References

A. Salvador, M. Drozdzal, X. Giro-i-Nieto and A. Romero, “Inverse Cooking: Recipe Generation from Food Images,” Computer Vision and Pattern Recognition, 2018.

Aman, Faseeha, and Sadia Masood. “How Nutrition can help to fight against COVID-19 Pandemic.” Pakistan journal of medical sciences vol. 36,COVID19-S4 (2020): S121-S123. doi:10.12669/pjms.36.COVID19-S4.2776
https://www.nejm.org/doi/full/10.1056/NEJMp2021264

Belanger, Matthew J., et al. “Covid-19 and Disparities in Nutrition and Obesity: NEJM.” New England Journal of Medicine, 8 Sept. 2020, www.nejm.org/doi/full/10.1056/NEJMp2021264.

Centers for Disease Control and Prevention. “National Health and Nutrition Examination Survey.” Kaggle, 26 Jan. 2017, www.kaggle.com/cdc/national-health-and-nutrition-examination-survey?select=diet.csv.

“Recipe1M+: A Dataset for Learning Cross-Modal Embeddings for Cooking Recipes and Food Images.” MIT, pic2recipe.csail.mit.edu/.
Ren, Maria. “COVID-19 Healthy Diet Dataset.” Kaggle, 22 Sept. 2020, www.kaggle.com/mariaren/covid19-healthy-diet-dataset?select=Food_Supply_Quantity_kg_Data.csv.

He, Kaiming, et al. “Deep Residual Learning for Image Recognition.” ArXiv:1512.03385 [Cs], Dec. 2015. arXiv.org, http://arxiv.org/abs/1512.03385.
