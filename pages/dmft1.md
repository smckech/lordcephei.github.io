---
layout: page-fullwidth
title: "First tutorial on QSGW+DMFT"
subheadline: ""
show_meta: false
teaser: ""
permalink: "/dmft1/"
sidebar: "left"
header: no
---
<hr style="height:5pt; visibility:hidden;" />
# First tutorial on QSGW+DMFT calculation

As explained in the introduction to GSGW+DMFT, the fundamental step of DMFT is the self-consistent solution of the (local) impurity problem. This is connected to the electronic structure of the material (bath) through the hybridization function. 
The self-consistent DMFT loop is composed by repeated steps:
1. The lattice Greens function is projected onto the local correlated subsystem (Gloc) to define the hybridization function (Delta) and the impurity levels (Eimp). 
2. These two quantities together with the effective Hubbard interaction U are fed into the Continuous Time Quantum Monte Carlo (CTQMC) solver to solve the corresponding impurity problem. This leads to an impurity self-energy and a corresponding impurity Greens function (Gimp)
3. The double counting is subtracted from it and result is embedded into an updated lattice Greens function. The loop then starts again from point 1. untill Gimp is equal to Gloc. 

In this tutorial, we will go through the steps needed to run the DMFT loop until convergence.
This will be done starting from a converged QSGW of the paramagnetic phase of La2CuO4.


SETUP: 
The loop is structured as a repeated sequence of lmfdmft runs and CTQMC runs where the output of one calculation are used as input for the next.
A useful bash script can help you in automatising this process (as e.g. the script ContIte.sh).
prepare the input folders (lmfinput and qmcinput) and add line 
'DMFT    PROJ=2 NLOHI=11,53 BETA=50 NOMEGA=1999 KNORM=0'
to the ctrl.file
Copy all files of lmfinput into folder signip0 and run lmfdmft. This will create an empty sig.inp 




