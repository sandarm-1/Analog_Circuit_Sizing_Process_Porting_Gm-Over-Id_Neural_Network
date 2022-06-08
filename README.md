# Analog Circuit Sizing with Neural Network in the gm/Id domain
Research idea, Analog Circuit sizing with Neural Network exploring the whole design space in the gm/Id domain.

## What has been done before

Previous research work using Neural Networks for Analog Circuit sizing did not explore the full design space of the circuit topology under test.

![image](https://user-images.githubusercontent.com/95447782/172449598-03581459-475b-4cce-91a9-ffd84f4c8e58.png)

Image above: Exploration and generalization capabilities of all the studied techniques in the prior state-of-the-art, from [1].

Existing techniques to generate the dataset for Supervised learning approaches:

* using an existing pre-sized circuit database
* random generation in a close region around a predefined initial solution
* from a global normal distribution

Existing techniques to generate the dataset for Hybrid approaches:
* speedup the search within the exploration space by sampling, guided by NNs
* Some randomized methods start by sampling over the whole design space before zooming-in on promising areas


**Ideally the whole design space should be explored. Why?**
* Better, larger, more comprehensive training dataset leads to higher model accuracy.
* The wider the exploration within the design space, the higher the generalization of the computed model will be [1].
* Ideally, fully random dataset generation would cover the whole design space.


Previous research on using Neural Networks for Analog circuit sizing **did not explore the whole design space**.

This was from two main limitations:

1) Previous research **relied on circuit simulator** to compute performance of each design point. Each time we invoke the circuit simulator, it's time-consuming & computationally expensive which makes it impractical to search the whole design space or large portions of it.
2) Previous research has been **focused on optimization of device dimensions (W, L)**. Search range for W is of multiple orders of magnitude which makes it impractical to explore the whole design space.



## Problem 1: using W/L

Problem is that the search range for W is of multiple orders of magnitude. From sub-1um to hundreds of um or more.

Hence, with W/L it's not practical to explore the whole design space. Sweeping W in such a large range is not ideally suited for design space exploration.

Perhaps it could be possible, but not practical. One could sweep the whole W range, but it's 3 orders of magnitude it does not necessarily lend itself to an efficient search.

**Solution:** Work in the the gm/Id space rather than on the W/L space. Then compute W backwards from gm/Id using pre-computed LUT data. Search range for gm/Id is approximately from 5 - 25 **regardless of process node**, as opposed to ~3 orders of magnitude for W.



## Problem 2: relying on time-consuming SPICE/spectre circuit simulations

SPICE/spectre circuit simulations are time-consuming. High computational cost of circuit simulations (SPICE / spectre).

Again, not possible to explore the whole design space with circuit simulations (SPICE / spectre).

**Solution:** Use pre-computed LUT data of devices from simulator on target process node, then compute performance specs from this data, without using SPICE/spectre circuit simulator. This can be done with a tool like ADT. 



## Proposed approach: explore design space in the gm/Id domain AND using pre-computed lookup tables / design database.

### Why gm/Id:
Smaller search range for gm/Id (5 - 25 instead of 3 orders of magnitude of W).

### Why using pre-computed LUTs / design databases:
With pre-computed LUTs and design databases approach, you run circuit simulations upfront, characterizing your devices for a particular process, you run your sweeps across VGS, VDS, VSB, etc.

Then once that data is available, you have a design database, for a particular topology (database of design topologies).

**You have the WHOLE DESIGN SPACE COVERED**.

And those points (performance metrics Versus Device gm/Id which equates to versus W/L) are SIMULATOR-ACCURATE.

### From there, this is the approach:

* Pre-compute LUTs for your devices on given process, store LUTs.
* Design-database (existing library of circuit topologies) shows PERFORMANCE METRICS (UGBW, Gain, etc) for ALL DESIGN POINTS in the DESIGN SPACE.
* Take 





## References
[[1](https://www.mdpi.com/2079-9292/11/3/435)] Mina R, Jabbour C, Sakr GE. A Review of Machine Learning Techniques in Analog Integrated Circuit Design Automation. Electronics. 2022; 11(3):435. https://doi.org/10.3390/electronics11030435


