# Multi_Fidelity_Gaussian_Processes
## Project Goals:
The Dynamics of a droplet in an oscillatory flow of a Newtonian fluid in a microchannel has been studied numerically. The effects of oscillation frequency, surface tension, and channel flow rate have been explored by simulating the drop within a microchannel. An oscillatory channel flow enables the focusing of extremely small biological particles by eliminating the necessity to design impractical long channels. The presented results show that the equilibrium position of the drop changes not only by the addition of an oscillation frequency to the pressure gradient wave but also is influenced by its surface tension and channel flow rate. <br>

The simulation results are very sensitive to the grid resolution due to the unsteady behavior of the base flow. Therefore, a set of fine grids have been used in this study to capture the physics of this problem more accurately. However, these fine grids make the computations significantly expensive. Therefore, a Multi Fidelity Gaussian processes method with two levels of fidelity has been used to predict the results of the remaining fine-grid simulations along with their uncertainties based on their correlations with those of the coarse-grid cases over a wide range of input parameters. 

## Introduction
Part of my PhD research is to study the dynamics of droplets suspended in microchannel flows numerically. In addition to the traditional steady flows, we have used an oscillatory flow with a certain frequency. An oscillatory channel flow enables the focusing of extremely small biological particles by eliminating the necessity to design impractical long channels. Furthermore, in our recent publication (https://doi.org/10.1103/PhysRevE.102.063110), we showed that the equilibrium position of particles depends on the oscillation frequency. This has potential applications for separating and sorting particles in microfluidic systems. <br>

The main challenge of performing this numerical work is using a sufficient grid resolution to resolve the underlying physics accurately. Since the background flow is unsteady, the mesh has to be fine enough to capture the velocity gradients and lift forces acting on the drop correctly. Consequently, the transient dynamics of the drop and its final equilibrium position, which is the main goal of the presented study, are all affected by this choice. Therefore, after carrying out the mesh dependence study, a very fine grid has been selected to enable getting the realistic results we are interested in. However, running simulations with these grids can take a few months in some cases. Hence, it is impossible to get all the data in the wide range of input parameters we are looking for by just running these simulations. An alternative approach is to produce some new data using a coarser grid resolution and implement the Multi Fidelity Gaussian processes (MFGP) to predict the outputs of the finer mesh.


## Methods
Multi-fidelity modeling enables accurate inference of quantities of interest by synergistically combining realizations of low-cost/low-fidelity models with a small set of high-fidelity observations. This is particularly effective when the low and high-fidelity models exhibit strong correlations and can lead to significant computational gains over approaches that solely rely on high-fidelity models. <br>

In this method, a normal Gaussian processes is performed on the first-fidelity level, which contains the data associated with the coarse grid or low-fidelity level. Then the posterior of the low fidelity level along with the training pairs of the fine grid results or high-fidelity level are used as the inputs of the GP with a modified kernel for the second level. In other words, GP is done recursively. The predictive mean and variance of this final posterior can be calculated using Monte Carlo integration of its corresponding equation. We can then sample from this distribution. The training sets need to have a nested structure, i.e. for any training pair in the high-fidelity level, we should have a corresponding pair in the low-fidelity level. <br>

Since there is no analytical solution to compare with, we decided to split the high fidelity data into training and test sets. This helps us evaluate the performance of the implemented MFGP. We assign 15 training and 6 test points randomly, and train the MFGP on the whole low-fidelity inputs and only the training high-fidelity inputs, and evaluate the responses on the high-fidelity test points. We do this 500 times to eliminate the dependence of the results on the choice of test points. This especially helps examine the performance at the regions with less amount of data. The successful reproducibility of these results is also checked. After this bootstrapping, the average of the mean squared error (MSE) was 0.00015 and the average of the R2 score was 0.9858.


## Results:
Here, we evaluate the MFGP performance by having a dataset consisting of 29 low-fidelity and 22 high-fidelity observations. The following figure shows all these observations together. It can be seen that the required nested structure is satisfied. In other words, for any data point in the high-fidelity level, there is a corresponding point in the low-fidelity level. <br>

<img src="https://github.com/alilafzi/Multi_Fidelity_Gaussian_Processes/blob/main/images/observations.png"> <br>

The following figures illustrate the predictions on the distance of the drop equilibrium position from the channel center over the range of 0 to 1 for the frequency and 0.09 to 1.67 for the Capillary number. The algorithm is trained over the entire data. A few sanity checks have been done to ensure that these results make sense. First, the first figure denotes that at any fixed value of the Capillary number, there is an optimum frequency for which the distance from the center (the z axis) is an extremum. This is also previously observed in the simulations for the input values in the dataset. Secondly, the predictions made by the high response are slightly higher than those of the lower response, which is also compatible with the simulations outcomes. Lastly, we know from the underlying physics that in the steady flow (at a frequency of 0), the distance decreases by increasing the Capillary number. This can be seen in both contour plots of the mean low and high responses (second and third figures) as well as being confirmed quantitatively in the code. The last figure illustrates a relatively low variance for the high-fidelity response, being our main goal, in the entire domain. The red-colored region in this plot corresponds to places where the density of data points is less, and hence, the predicted outputs have more uncertainty. <br>

<img src="https://github.com/alilafzi/Multi_Fidelity_Gaussian_Processes/blob/main/images/surface_plot.png"> <br>
<img src="https://github.com/alilafzi/Multi_Fidelity_Gaussian_Processes/blob/main/images/mean_low_response.png"> <br>
<img src="https://github.com/alilafzi/Multi_Fidelity_Gaussian_Processes/blob/main/images/mean_high_response.png"> <br>
<img src="https://github.com/alilafzi/Multi_Fidelity_Gaussian_Processes/blob/main/images/variance_high_fidelity.png"> <br>

Since there is no analytical solution to compare with, we decided to split the high fidelity data into training and test set in this section. This helps us evaluate the performance of the implemented MFGP. We assign 15 training and 6 test points randomly, and train the MFGP on the whole low-fidelity inputs and only the training high-fidelity inputs, and evaluate the responses on the high-fidelity test points. We do this 100 times to eliminate the dependence of the results on the location of the test inputs. This especially helps examine the performance at the regions with less amount of data. The successful reproducibility of these results is also checked. After this bootstrapping, the average of the mean squared error (MSE) was 0.00011 and the average of the $R^2$ score was 0.9858. Fig. \ref{test performance} shows this evaluation at the last test set. Fig. \ref{pred and obs} expresses the predictions along with their 95\% intervals, and denotes that the observed data lie within these shaded error bands. Having more data would help get an standardized error obeying a closer distribution to normal according to Fig. \ref{std error}.
\begin{figure}
\centering
  \begin{subfigure}[h!]{.485\textwidth}
  \centering
  \includegraphics[height=.24\textheight]{prediction_and_observations.png}
  \caption{}
  \label{pred and obs}
  \end{subfigure}
  ~
  \begin{subfigure}[h!]{.485\textwidth}
  \centering
  \includegraphics[height=.24\textheight]{prediction_vs_observations.png}
  \caption{}
  \label{pred vs obs}
  \end{subfigure}
  ~
  \begin{subfigure}[h!]{.485\textwidth}
  \centering
  \includegraphics[height=.24\textheight]{correlations.png}
  \caption{}
  \label{correlations}
  \end{subfigure}
  ~
  \begin{subfigure}[h!]{.485\textwidth}
  \centering
  \includegraphics[height=.24\textheight]{std_error.png}
  \caption{}
  \label{std error}
  \end{subfigure}
   \caption{Evaluation of the MFGP performance on a random test set by illustrating the (a) observations and predictions with their uncertainties, (b) predictions versus observations, (c) exact versus predicted correlations, and (d) the standardized error}
     \label{test performance}
\end{figure}



## Conclusions:
Determination of the particles' equilibrium positions in the microchannels is extremely crucial as it can help in a variety of microfluidics applications. This importance as well as the need to overcome the issue of designing impractically long channels led us to do some simulations to capture the dynamics of a single droplet suspended in an oscillatory flow within the channel. Due to the significant cost of these simulations, a recursive version of the Multi Fidelity Gaussian processes has been used to replace the numerous high-fidelity simulations that cannot be afforded numerically. The MFGP algorithm is used to predict the equilibrium distance of the drop from the channel center for a given range of the interplaying input parameters, namely the Capillary number and frequency, assuming a constant Reynolds number. In addition, its performance was evaluated by randomly shuffling the high-fidelity data 500 times and assigning around 30% of it as the test set for an accurate quantitative comparison each time. 

## Reference:
The original code can be found at: <br>
https://github.com/paraklas/NARGP
