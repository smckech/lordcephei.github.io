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
prepare the input folders (lmfinput and qmcinput) and
1. add line 
'DMFT    PROJ=2 NLOHI=11,53 BETA=50 NOMEGA=1999 KNORM=0'
to the ctrl.file. 
The token NLOHI defines the projection window in band index, BETA is the inverse temperature in eV^{-1} and NOMEGA is the number of Matsubara frequencies in the mesh.
2. Copy all files of lmfinput into folder signip0 and run lmfdmft with -job=1. This will create an empty sig.inp 
lmfdmft lsco -vnk=4 -job=1
3. Compile (with gfortran for instance) the file braod_sig.f90 in broad_sig.x

THE LOOP:
The loop is composed by alternated runs of lmfdmft and ctqmc, the output of each run being the input for the successive.
1. Prepare an lmfdmft run 
mkdir it#_lmfrun (with #=iteration , #=1 if first run)  
cp lmfinput/* it#_lmfrun  ; cd it#_lmfrun
if #/=1, then :
 cp it(#-1)_qmcrun/Sig.out.brd it#_lmfrun/sig.inp
 cp it(#-1)_qmcrun/g_qmc.dat   it#_lmfrun/gimp.prev.lsco
else if #==1 then:
 cp siginp0/sig.inp it1_lmfrun/sig.inp
Then launch lmfdmft lsco -vnk=4 --rs=1,0 --ldadc=$Edc -job=1 where $Edc=U(n-1/2)-J(n/2-1/2) with U and J Hubbard on-site interaction and Hunds coupling nand n nominal occupancy (n=9 for cuprates). A reasonable value for this tutorial are U=10 eV and J=0.7 eV, leading to $Edc=82.2
The hybridization function is stored in delta.lsco (first column are Matsubaras energies and then five d-channels (each real and imaginary).The impurity levels are recorded in eimp1.lsco 

2. Prepare a ctqmc run 
mkdir it#_qmcrun/run1 (with #=iteration)
cp qmcinput/Trans.dat     it#_qmcrun/
cp qmcinput/PARAMS        it#_qmcrun/
cp qmcinput/atom_d.py     it#_qmcrun/
cp it#_lmfrun/delta.lsco  it#_qmcrun/Delta.inp
cp it#_lmfrun/eimp1.lsco  it#_qmcrun/Eimp.inp
copy the forth line of Eimp.inp in the PARAMS file (in such a way to have only one line like Ed=[ ..... ) and change accordingly the mu variable in PARAMS (it has to be the first value of the Ed string with inverted sign).
run atom_d.py using the command
atom_d.py J=0.7 l=2 cx=0.0 OCA_G=False qatom=0 "CoulombF='Ising'" HB2=False Eimp=[   0.000000,   0.248617,   0.642283,   0.248882,   0.681997] where the last command is copied from the third line of Eimp.inp
Add correct values of U, J, nf0 and beta in PARAMS. The Params file at the end should look like the following ... 

The run	should now be send using a submission script. 
Important parameters (that may need to be adjusted during the loop) are 
nom
Nmax 
M 

to start we can set them to nom 120 Nmax 200 and M 75000000
After the run you need to broad sigma using the program brad_sig.f90 
EXPLAIN WHAT ARE THE STATUS FILES


