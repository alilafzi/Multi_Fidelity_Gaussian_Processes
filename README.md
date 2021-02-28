# Multi_Fidelity_Gaussian_Processes
## Project Goals:
The Dynamics of a droplet in an oscillatory flow of a Newtonian fluid in a microchannel has been studied numerically. The effects of oscillation frequency, surface tension, and channel flow rate have been explored by simulating the drop within a microchannel. An oscillatory channel flow enables the focusing of extremely small biological particles by eliminating the necessity to design impractical long channels. The presented results show that the equilibrium position of the drop changes not only by the addition of an oscillation frequency to the pressure gradient wave but also is influenced by its surface tension and channel flow rate. <br>

The simulation results are very sensitive to the grid resolution due to the unsteady behavior of the base flow. Therefore, a set of fine grids have been used in this study to capture the physics of this problem more accurately. However, these fine grids make the computations significantly expensive. Therefore, a Multi Fidelity Gaussian processes method with two levels of fidelity has been used to predict the results of the remaining fine-grid simulations along with their uncertainties based on their correlations with those of the coarse-grid cases over a wide range of input parameters. 

## Introduction
Part of my PhD research is to study the dynamics of droplets suspended in microchannel flows numerically. In addition to the traditional steady flows, we have used an oscillatory flow with a certain frequency. An oscillatory channel flow enables the focusing of extremely small biological particles by eliminating the necessity to design impractical long channels. Furthermore, in our recent publication ( https://doi.org/10.1103/PhysRevE.102.063110 ), we showed that the equilibrium position of particles depends on the oscillation frequency. This has potential applications for separating and sorting particles in microfluidic systems. <br>

The main challenge of performing this numerical work is using a sufficient grid resolution to resolve the underlying physics accurately. Since the background flow is unsteady, the mesh has to be fine enough to capture the velocity gradients and lift forces acting on the drop correctly. Consequently, the transient dynamics of the drop and its final equilibrium position, which is the main goal of the presented study, are all affected by this choice. Therefore, after carrying out the mesh dependence study, a very fine grid has been selected to enable getting the realistic results we are interested in. However, running simulations with these grids can take a few months in some cases. Hence, it is impossible to get all the data in the wide range of input parameters we are looking for by just running these simulations. An alternative approach is to produce some new data using a coarser grid resolution and implement the Multi Fidelity Gaussian processes (MFGP) to predict the outputs of the finer mesh.


## Methods
Multi-fidelity modeling enables accurate inference of quantities of interest by synergistically combining realizations of low-cost/low-fidelity models with a small set of high-fidelity observations. This is particularly effective when the low and high-fidelity models exhibit strong correlations and can lead to significant computational gains over approaches that solely rely on high-fidelity models. <br>

In this method, a normal Gaussian processes is performed on the first-fidelity level, which contains the data associated with the coarse grid or low-fidelity level. Then the posterior of the low fidelity level along with the training pairs of the fine grid results or high-fidelity level are used as the inputs of the GP with a modified kernel for the second level. In other words, GP is done recursively. The predictive mean and variance of this final posterior can be calculated using Monte Carlo integration of its corresponding equation. We can then sample from this distribution. The training sets need to have a nested structure, i.e. for any training pair in the high-fidelity level, we should have a corresponding pair in the low-fidelity level. <br>

Since there is no analytical solution to compare with, we decided to split the high fidelity data into training and test sets. This helps us evaluate the performance of the implemented MFGP. We assign 15 training and 6 test points randomly, and train the MFGP on the whole low-fidelity inputs and only the training high-fidelity inputs, and evaluate the responses on the high-fidelity test points. We do this 500 times to eliminate the dependence of the results on the choice of test points. This especially helps examine the performance at the regions with less amount of data. The successful reproducibility of these results is also checked. After this bootstrapping, the average of the mean squared error (MSE) was 0.00015 and the average of the R2 score was 0.9858.


## Results:
1. To have an initial assessment of the variation of percent change in the death ratio, we plotted the percent death ratio as functions of population, median income, and percent of the population that frequently uses mask, which has a relatively high correlation coefficient. There is no detectable pattern between parameters of interest and death ratio.<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/all_scatter_2.png"> <br>

2. Figure (a) shows that in general, communities that voted republican in presidential election of 2020 were affected worse compared to democratic counties. Furthermore, a noticeable correlation is observed between average median income and the change of death ratio, presented in Figure (b). It is shown that, on average, the communities with less median income experienced a positive change in death ratio, meaning more mortality rate regardless of their political inclination. The strongest correlation however is observed by considering county population, shown in Figure (c). The counties with fewer residents were affected more adversely by the pandemic compared to high-population counties. The counter intuitive relation between population and change in death ratio further corroborates necessity of inclusion of the two other supplementary features. <br>
<br>

<img src="https://github.com/alilafzi/corona/blob/main/images/BarGraph_Political.png" height = 400 width = 500> <br>
<b> (a) </b> Change in death ratio and representation of number of counties based on political inclination.<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/BarGraph_Income.png" height = 400 width = 500> <br>
<b> (b) </b> Change in death ratio and median income based on political inclination.<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/BarGraph_Population.png" height = 400 width = 500> <br>
<b> (c) </b> Change in death ratio and average population, based on political inclination.<br>
<br>

3. The change in death ratio from one month before to one month after the date of mandating face-covering in the three PC states for each county  is visualized in the following figure. Two clusters of increase in death ratio can be seen, one near northern Washington, and one near central California, but the overall pattern is totally random. <br>
<img src="https://github.com/alilafzi/corona/blob/main/images/death_ratio_county_map_1.png"> <br>
<br>

4. The effect of each feature on the change of death ratio is visualized by the correlation heatmap provided in the figure below. Each row of the correlation matrix is an appropriate indicator of how correlated that feature is with change in death ratio. A more negative value implies that increase of that specific feature is positively correlated by a decrease in change of death ratio. For instance, increase in population, median income, and votes for democratic party would result in a decrease in change of death ratio. On the other hand, the positive correlation for republican votes leads to a higher change of positive increase in death ratio. An interesting observation is the disordered correlation pattern for mask usage. It can be seen that, as one expects, increasing the number never and rarely mask users is positively correlated with change in death ratio. However, the data associated with frequently mask users have resulted in a positive correlation value. Such erratic correlation behavior necessitates inclusion of other features in the analysis. <br>
<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/output_features_correlations.png"> <br>
<br>

5. The accuracy scores for all 9 ML algorithms on PC states are presented in the following table. We can see that Naive Bayes, Random Forest, and XGBoost have outperformed the others:
<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/performances.png" >
<br>
Below, we see the confusion matrices for our top 3 algorithms:<br>
<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/confusion_matrix_NB.png"> 
Confusion matrix of Naive Bayes <br>
<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/confusion_matrix_RF.png"> 
Confusion matrix of Random Forest <br>
<br>
<img src="https://github.com/alilafzi/corona/blob/main/images/confusion_matrix_XGBoost.png"> 
Confusion matrix of XGBoost <br>
<br>

6. Using the calculated hyper-parameters from the best performing algorithms, it would be possible to predict effect of similar viral illnesses in future. To verify the legibility of the proposed work, the best performing algorithms (Naive Bayes, Random Forest, and XGBoost), were chosen with the computed hyper-parameters to process the data for four additional states, namely, Arizona, New Jersey, New York, and Texas. For choosing states for testing purposes, three main criteria were considered: <br>
(a) Availability of data provided by NY Times survey <br>
(b) Population <br>
(c) Versatility of death rate ratio <br> 
The NY Times mask usage survey is only available for the time period of interest, 7/2/2020 to 7/14/2020; therefore, the month after the corresponding MM order should contain this period for validity of our analysis. The chosen states all have high population. Lastly, Arizona, New Jersey, and New York all experienced a negative change of death ratio, while Texas suffered significant losses in the month after the MM was placed. Inclusion of cases with extreme positive and negative change of death ratio was done deliberately to assay functionality of the selected algorithms. The accuracy score for the processed algorithms on these four states are presented in the given table. <br>
It should be noted that the results of the three west coast states were chosen as training data set. The entire data from the four states is treated as test data set. Hence, it is expected for the accuracy score to drop for testing the additional states. However, the trend of high accuracy for train and test data sets, signifies the existence of a pattern between the chosen features and the change in death ratio.

<img src="https://github.com/alilafzi/corona/blob/main/images/2.PNG"> <br>

## Conclusions:
In this body of work, we have analyzed the effect of mask covering on the intensity of spread of the COVID-19 virus by considering the death ratio at the county level to be the
primary indicator. To bridge the gap between level of adherence to mask mandate, four main features are used as input data, population, income, political inclination, and the results of the survey on mask usage from New York Times. The change in the death ratio is used as the metric to quantify the effectiveness of face-coverings on the COVID-19 spread. After extracting and refining the data-set from reliable sources, we analyzed the information using 9 different algorithms. Among all the methods used, Random Forest, XGBoost, and Naive Bayes had the best performance with a classification accuracy of 94%. The high performing algorithms, with the computed hyper-parameters, are then used to process four additional states, Arizona, New Jersey, New York, and Texas entirely used as test data set. The acceptable accuracy results for the large test case, further verifies legibility of the chosen features as influential criteria for modeling purposes. The obtained hyper-parameters for these models (except for Naive Bayes) can now be used to predict future conditions of the spread of the virus. It is shown that, in most of the counties, there exist a connection between adherence to the mask mandate and change in death ratio. The findings of this work emphasizes importance of immediate legislative action on well-being of societies. It is hoped that the findings of this work, highlight importance of socioeconomic and political settings on behavior of different communities, which as portrayed could be complex and counter-intuitive. For instance, if the mask mandate had been issued earlier, with better implementation procedures along with effective incentives targetted at specific communities, more people would be encouraged to abide by the issued ordinance, and consequently, fewer individuals and families would have become the victim of the pandemic.

## Reference:
The original code can be found at: <br>
https://github.com/paraklas/NARGP
