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

<img src="https://github.com/alilafzi/corona/blob/main/images/all_scatter_2.png"> <br>



## Conclusions:
Determination of the particles' equilibrium positions in the microchannels is extremely crucial as it can help in a variety of microfluidics applications. This importance as well as the need to overcome the issue of designing impractically long channels led us to do some simulations to capture the dynamics of a single droplet suspended in an oscillatory flow within the channel. Due to the significant cost of these simulations, a recursive version of the Multi Fidelity Gaussian processes has been used to replace the numerous high-fidelity simulations that cannot be afforded numerically. The MFGP algorithm is used to predict the equilibrium distance of the drop from the channel center for a given range of the interplaying input parameters, namely the Capillary number and frequency, assuming a constant Reynolds number. In addition, its performance was evaluated by randomly shuffling the high-fidelity data 500 times and assigning around 30% of it as the test set for an accurate quantitative comparison each time. 

## Reference:
The original code can be found at: <br>
https://github.com/paraklas/NARGP
