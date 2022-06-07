# Analog Circuit Sizing with Neural Network in the gm/Id domain
Research idea, Analog Circuit sizing with Neural Network exploring the whole design space in the gm/Id domain.

## What has been done before

Previous research on using Neural Networks for Analog circuit sizing has been **focused on optimization of device dimensions (W, L)**.

Also **relied on circuit simulator** to compute performance of each design point. Each time we invoke the circuit simulator, it's time-consuming & computationally expensive.



## Problem 1: using W/L

problem is that the search range for W is of multiple orders of magnitude.

Hence, with W/L it's not practical

Not practical to explore the whole design space.

(let's say maybe possible but not practical, one could sweep the whole W range, but it's 3 orders of magnitude)

Better to sweep the gm/Id space.

Previous research work did not explore full design space.

![image](https://user-images.githubusercontent.com/95447782/172449598-03581459-475b-4cce-91a9-ffd84f4c8e58.png)

Image from PAPER_NAME.

Existing techniques to generate the dataset for Supervised learning approaches:

* using an existing pre-sized circuit database
* random generation in a close region around a predefined initial solution
* from a global normal distribution

Existing techniques to generate the dataset for Hybrid approaches:
* speedup the search within the exploration space by sampling, guided by NNs
*  Some randomized methods start by sampling over the whole design space before zooming-in on promising areas


**Ideally the whole design space should be explored. Why?**
* Better, larger, more comprehensive training dataset leads to higher model accuracy
* "The wider the exploration within the design space, the higher the generalization of the computed model will be"
* "Ideally, fully random dataset generation would cover the whole white space "


## Problem 2: relying on SPICE/spectre circuit simulations are time-consuming

SPICE/spectre circuit simulations are time-consuming

High computational cost of circuit simulations (SPICE / spectre).

Again, with circuit simulations (SPICE / spectre) Not possible to explore the whole design space.

Because they are so time-consuming.



## Proposed approach: explore design space in the gm/Id domain AND using pre-computed lookup tables / design databases.

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








