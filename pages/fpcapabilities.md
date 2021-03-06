---
layout: page-fullwidth
title: "Full Potential LMTO Capabilities"
subheadline: ""
show_meta: false
teaser: ""
permalink: "/fpcapabilities/"
header: no
---
<hr style="height:5pt; visibility:hidden;" />
### *Introduction*

This page should serve as a demonstration of a number of the capabilities of the **lmf** code and can be used as a reference for the corresponding procedures. 

<hr style="height:5pt; visibility:hidden;" />
##### *Energy bands*

You can run **lmf** in band mode to generate energy bands along lines or planes, for generating, e.g. Fermi surfaces.  *k*-point specifications and invocation is described [here](//). Particularly useful are the color weights.

<hr style="height:5pt; visibility:hidden;" />
##### *Partial DOS and Mulliken analysis*

**lmf** can generate partial **DOS** within augmentation spheres, and construct a Mulliken analysis. **DOS** can be resolved by site, by site and *l*, or by site and *lm*. These options are invoked through in command-line switches. For illustrations, invoke

    fp/test/test.fp co 2 
    
    fp/test/test.fp fe 2

<hr style="height:5pt; visibility:hidden;" />
##### *Charge density*

**lmf** can generate the charge density (smooth or not, with or without cores), and the contribution to the density from a selected window of states. See *--wden*{: style="color: green"} and *--window*{: style="color: green"} in command-line switches

<hr style="height:5pt; visibility:hidden;" />
##### *Core-level spectroscopy*

**lmf** can generate **EELS** spectra, which involve matrix elements between core and valence electrons. The **EELS** option is invoked with a command-line switches. For illustrations, invoke

    fp/test/test.fp fe 2 
    
    fp/test/test.fp crn 2

<hr style="height:5pt; visibility:hidden;" />
##### *LDA+U*

The **LDA** + **U** functional was built into lmf in v6.15 and later, by Walter Lambrecht. **LDA** + **U** needs in addition to the **LDA** parameters **U** and **J** for selected orbitals, which are empirical. The **LDA** + **U** constructs an additional potential for a particular *l* subblock (*m = -l..l*) from the **U** supplied by the user, and the density-matrix, which is generated by **lmf**.

Two modifications must be added to the input, which are described here.

In a strictly **LDA** calculation, complete information is carried by the density, contained in the restart file, *rst.ext*{: style="color: blue"}. In the **LDA** + **U** case, complete information is carried by density and on-site density matrices, which are contained in file *dmats.ext*{: style="color: blue"}.

An example that illustrates **LDA** + **U** method is ErAs, which you can run by

    fp/test/test.fp eras

ErAs is an interesting case because **LDA** puts all 4 minority *f* electrons in a single extremely narrow band at the Fermi level. In **LDA** + **U** the minority *f* is split into a 4 - and 3-manifold; see *PRB 67, 035104 (2003)*.

<hr style="height:5pt; visibility:hidden;" />
##### *GW*

**lmf** is designed to work in coordination with a **GW** package by T. Kotani (the **GW** package comes separately). **lmf** acts both as a driver for the **GW** package and also can be used in a self-consistent **GW** cycle. An extra driver **lmfgw** is compiled as part of this extension. Use of this driver is described in the **GW** driver documentation. You need the extension package *GW.version.tar.gz*{: style="color: blue"}. Also you will need the **GW** package itself. For illustrations of the driver invoke

    gw/test/test.gw si 

    gw/test/test.gw gas

<hr style="height:5pt; visibility:hidden;" />
##### *Spin-Orbit coupling*

**lmf** solves the scalar Dirac hamiltonian. The dominant difference between the full Dirac hamiltonian and the scalar one is the spin orbit coupling, which can be added as a term λL · S to the scalar Dirac Hamiltonian.

Starting with v6.15, lmf can add λL·S to the scalar Dirac hamiltonian (courtesy of A. Chantis). It is possible to include the full L · S or just the L<sub>z</sub> · S<sub>z</sub> part. Beginning with v7.9,   L<sub>z</sub> · S<sub>z</sub> + (L · S − L<sub>z</sub> · S<sub>z</sub>) can be added where the last term is treated in an approximate manner. The approximation turns out to be rather good. See here for some description and analysis of all three approximations. L · S in all three forms can be combined with the self-energy read by a **QSGW** calculation. In the L<sub>z</sub> · S<sub>z</sub> and approximate L · S forms the effect of S<sub>O</sub> coupling can be passed through to the **GW** code, to include its effect on the self-energy.

Use HAM_S<sub>O</sub> to turn on S<sub>O</sub> coupling.

For illustrations of all three kinds of approaches, try

    fp/test/test.fp felz 

    fp/test/test.fp gasls

<hr style="height:5pt; visibility:hidden;" />
##### *Local orbitals*

In v6.12 and later, local orbitals may be added to the basis set. These orbitals are important when energy bands over a very wide energy window are required, when high accuracy is needed for shallow (semi-)core states, or for energies far above the Fermi energy. Examples of the former occur in oxides: bond lengths are small and cations with shallow p orbitals extend somewhat beyond the augmentation radius.

Local orbitals are presented in one of two flavors. The first, conventional type of local orbital is constructed by solving the radial Schrödinger at a different linearization energy than the usual valence states, and then subtracting off a particular linear of the valence wave function $\phi$ and energy derivative $\dot{\phi}$ such that the local orbital’s value and slope vanish at the augmentation radius. Thus

+ A local orbital is strictly confined to the augmentation sphere, and has no envelope function at all.
+ When taken in linear combination with the valence augmentation functions, it can solve the Schrödinger equation exactly for linearization the energy chosen (that is, for the spherical potential that defines the wave functions).
+ It turns the *linear* method into a *quadratic* one. Of course, it would be possible, and more accurate, to construct a **LMTO** orbital of a different principal quantum number complete with envelope function; however, there is a corresponding loss in efficiency because the additional matrix elements of the envelope function must be evaluated.
+ for states with energies in the vicinity of the local orbital energy, the envelope functions of the regular valence states combine with the local orbital to make the interstitial part of the wave function.  

Examples that illustrate local orbitals of this type are


      fp/test/test.fp gas 

      fp/test/test.fp cu

The Ga 3*d* semi-core the high-lying As 5*s* state are included as local orbitals. In the Cu case, the high-lying Cu 4*d* is included, which is important in **GW** calculations.

The second, extended, kind of local orbital can only be used for semi-core states. Instead of artificially subtracting off some linear of the phi and phi-dot to make the orbital vanish at the augmentation radius, a smooth Hankel tail is attached to the orbital. The smoothing of tail is constructed to match as well as possible the kinetic energy of the semi-core state. This type of orbital has the advantage that the valence envelope function need not "carry" the tail of the semi-core state. Its drawback is that more things can “go wrong,” namely it may fail to do a good job of fitting the kinetic energy.

An example that illustrates the second kind of local orbital is

    fp/test/test.fp srtio3  

The Sr 4*p* and Ti 3*p* semi-core states are included as local orbitals. In the first part of the test, they are included as local orbitals of the first type; then the last step is recalculated using local orbitals of the second type.

<hr style="height:5pt; visibility:hidden;" />
##### *Floating Orbitals*

In v6.15 and later, floating orbitals may be added to the basis set. These orbitals can be important when very accurate calculations are needed in open systems, e.g. when reliable energy bands are needed for a wide energy window. These orbitals differ from the usual smooth Hankels in that they are not centered at an atom. They are augmented just as the other orbitals, but there is no augmentation radius and thus no “head” sphere.

The following illustrate the inclusion of floating orbitals in the basis:

    fp/test/test.fp te 

    fp/test/test.fp gaslc

<hr style="height:5pt; visibility:hidden;" />
##### *Augmented Plane Waves*

Starting with v6.17, Augmented Plane Waves can also be included in the basis. They play the same role as floating orbitals do, but APWs are superior because they are simpler to use (there are no parameters and they do not need to be located at any particular site), and the control over convergence is more systematic.

The following illustrate the inclusion of APWs in the basis:

    fp/test/test.fp te 

    fp/test/test.fp srtio3 

    fp/test/test.fp felz 4

<hr style="height:5pt; visibility:hidden;" />
##### *Parallel Implementation*

Two separate parallel versions of **lmf** have been made (courtesy of A. T. Paxton). One parallelizes over *k*-points, and is the most efficient for scaling; the other parallelizes many points at a lower level.
