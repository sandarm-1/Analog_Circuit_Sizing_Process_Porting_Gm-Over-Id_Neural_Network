# Analog Circuit Sizing/Re-Sizing/Process Porting in the gm/Id domain with Neural Network
Research idea. Analog Circuit sizing (or re-sizing, or process porting), in the gm/Id domain, assisted by Neural Network and gm/Id space exploration tools, exploring the complete design space.

## What has been done before

At a very high level, sizing Analog Circuits using Neural Networks consists of two main steps:
1) Generation of training dataset
2) Training the NN with data from said training dataset and then using the NN to make predictions on new, unseen data.

In the first step, design parameters (such as device dimensions) are fed into a circuit simulator which calculates performance metrics for each design point, i.e. each specific set of design parameters/dimensions.

* Design parameters: Xi = {W1, L1, W2, L2, W3, L3, W4}
* Performance metrics: Yj = {Ad, Ic, etc}

With this we build a labelled training dataset.

The first step can be represented as follows:

![image](https://user-images.githubusercontent.com/95447782/172617075-1e61ea35-52e7-41ac-8ee0-034e29249635.png)

Image above: Generation of the training dataset using integrated circuit CAD simulators, from [1].


In the second step, we feed the Neural Network with performance metrics Yj at the input of the Neural Network and we get predicted design parameters Xi at the output of the Neural Network.

First we do this in a training phase, and once the Neural Network is trained we do it with data that the Neural Network has not seen before, and we expect the Neural Network to give an accurate prediction of device dimensions that correspond to a set of performance metrics.

The second step can be represented as follows:

![image](https://user-images.githubusercontent.com/95447782/172617971-b2b5ce75-4c82-49f1-b87a-db9f467ad28e.png)

Image above: Inputs/outputs of a neural network using the performance metrics directly as features, from [1].

================

## What has/hasn't been done before

Previous research work using Neural Networks for Analog Circuit sizing did not explore the full design space of the circuit topology under test.

![image](https://user-images.githubusercontent.com/95447782/172623499-993efdc4-3a68-4373-98d6-2ddee7e12694.png)

Image above: Exploration and generalization capabilities of all the studied techniques in the prior state-of-the-art, from [1].

In the figure above, the white space enclosed by a dotted line represents the whole design space, represented in two dimensions, but of course in most cases the design space will be represented by more than two performance metrics.

**Ideally the whole design space should be explored. Why?**
* Better, larger, more comprehensive training dataset leads to higher model accuracy.
* The wider the exploration within the design space, the higher the generalization of the computed model will be [1].
* Ideally, fully random dataset generation would cover the whole design space.

Existing techniques to generate the dataset for Supervised learning approaches are:

* using an existing pre-sized circuit database
* random generation in a close region around a predefined initial solution
* from a global normal distribution

Existing techniques to generate the dataset for Hybrid approaches are:
* speedup the search within the exploration space by sampling, guided by NNs
* Some randomized methods start by sampling over the whole design space before zooming-in on promising areas


Previous research on using Neural Networks for Analog circuit sizing **did not explore the whole design space**.

This was due to two main limitations:

1) Previous research **relied on circuit simulator** to compute performance of each design point. Each time we invoke the circuit simulator, it's time-consuming & computationally expensive which makes it impractical to search the whole design space or large portions of it.
2) Previous research has been **focused on optimization of device dimensions (W, L)**. Search range for W is of multiple orders of magnitude which makes it impractical to explore the whole design space.



### Problem 1: using W/L

Problem is that the search range for W is of multiple orders of magnitude. From sub-1um to hundreds of um or more.

Hence, with W/L it's not practical to explore the whole design space. Sweeping W in such a large range is not ideally suited for design space exploration.

Perhaps it could be possible, but not practical. One could sweep the whole W range, but it's 3 orders of magnitude it does not necessarily lend itself to an efficient search.

**Solution:** Work in the the gm/Id space rather than on the W/L space. Then compute W backwards from gm/Id using pre-computed LUT data. Search range for gm/Id is approximately from 5 - 25 **regardless of process node**, as opposed to ~3 orders of magnitude for W.



### Problem 2: relying on time-consuming SPICE/spectre circuit simulations

SPICE/spectre circuit simulations are time-consuming. High computational cost of circuit simulations (SPICE / spectre).

Again, not possible to explore the whole design space with circuit simulations (SPICE / spectre).

**Solution:** Use pre-computed LUT data of devices from simulator on target process node, then compute performance specs from this data, without using SPICE/spectre circuit simulator. This can be done with a tool like ADT. 



## Proposed approach: explore design space in the gm/Id domain AND using pre-computed lookup tables / design database AND using ADT instead of circuit simulator

### Why gm/Id:
Smaller search range for gm/Id (5 - 25 instead of 3 orders of magnitude of W).

### Why using pre-computed LUTs / design databases:
With pre-computed LUTs and design databases approach, you run circuit simulations upfront, characterizing your devices for a particular process, you run your sweeps across VGS, VDS, VSB, etc.

Then once that data is available, you have a design database, for a particular topology (database of design topologies). **You have the whole design space covered**.

And those points (performance metrics Versus Device gm/Id which equates to versus W/L) are simulator-accurate data.

### Why using ADT
ADT does not invoke the circuit simulator to calculate performance specs outputs for every design point input.

## From there, this is the proposed approach:

* Pre-compute LUTs for your devices on given process, store LUTs. This can be done either with ADT or with alternative, open-source characterzation framework such as pyCHAR or code similar to/based on Dr. Boris Murmann code. Sweep whole design space at this point.
* Calculate output performance metrics for whole design space for circuit topology of interest. Store output performance metris in design database (DC Gain, BW, UGF, etc) for ALL DESIGN POINTS in the DESIGN SPACE. Do this using a tool like ADT without invoking circuit simulator for every design point in the design space.
* Use this comprehensive dataset to train Neural Network. Overall dataset could be split in training, cross-validation and test set. The whole design space is available but not all has to be shown to the Neural Network during training.

Benefits:
* More data, from complete design space, would potentially allow for more accurate models to be developed.
* Potentially more accurate models could perhaps be able to generalize better to other process nodes.
* Potential benefit for process porting.
* Faster overall process, less spice/spectre circuit simulations



## References
[[1](https://www.mdpi.com/2079-9292/11/3/435)] Mina R, Jabbour C, Sakr GE. A Review of Machine Learning Techniques in Analog Integrated Circuit Design Automation. Electronics. 2022; 11(3):435. https://doi.org/10.3390/electronics11030435

[[2](http://dx.doi.org/10.1145/3297156.3297160)] Wang, Z.; Luo, X.; Gong, Z. Application of Deep Learning in Analog Circuit Sizing. In Proceedings of the 2018 2nd International Conference on Computer Science and Artificial Intelligence, Shenzhen, China, 8–10 December 2018; pp. 571–575.

[[3](http://dx.doi.org/10.1109/ICCAD45719.2019.8942062)] Hakhamaneshi, K.; Werblun, N.; Abbeel, P.; Stojanovic, V. BagNet: Berkeley Analog Generator with Layout Optimizer Boosted with Deep Neural Networks. In Proceedings of the 2019 IEEE/ACM International Conference on Computer-Aided Design (ICCAD), Westminster, CO, USA, 4–7 November 2019; pp. 1–8.


