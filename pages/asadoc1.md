---
layout: page-fullwidth
title: "LM Suite - ASA Tutorial"
subheadline: ""
show_meta: false
teaser: ""
permalink: "/asadoc1/"
header:
    image_fullwidth: "header_drop.jpg"
---

### Table of Contents
*  Auto generated table of contents
{:toc}

# ASA Tutorial (v0.00)

### _Purpose_

This tutorial demonstrates how to set up and run band calculation in the Atomic Spheres Approximation (ASA), using the **lm** program. It:

&nbsp;&nbsp;&nbsp;&nbsp;1\. describes how to build the input file.

&nbsp;&nbsp;&nbsp;&nbsp;2\. explains the output of a self-consistent calculation.

&nbsp;&nbsp;&nbsp;&nbsp;3\. shows how to generate energy bands and partial DOS.

### _Prerequisites_

You will need the executables **lmchk**, **lm**, **lmstr** etc in your path (all of these are installed when scons is run with the target flag _all_).

(Optional) It is also advised to have a plotting tool installed, such as **FPLOT**.

(Optional) Read the ASA documentation

### _Brief Tutorial_

The aim of this section of the tutorial is to give a brief outline of the steps involved in using the **lm** program for those who may be more familiar with it. A detailed outline is provided later on in the tutorial for those who need a more in depth guide.

Note: All instances of _si_ in the command lines can be replaced with _ctrl.si_, depending on what your input file has been named.

##### _1\. Building the Input File_

It is assumed for the brief tutorial that the specific categories in the input file are known. The sample input file for Si is provided [here](http://titus.phy.qub.ac.uk/packages/LMTO/v7.11/doc/ASAsamples/ctrl.si). 

<div onclick="elm = document.getElementById('box'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><i>For more information on this step click here.</i></div> 

<div style="display:none;" id="box">

  This tutorial explains an a sample calculation for Si, using <a href="http://titus.phy.qub.ac.uk/packages/LMTO/v7.11/doc/ASAsamples/ctrl.si">this input file</a> as a template. The top-level directory is assumed to be "~/lm."<br><br>

  Before going through these steps, read how the <a href="input-file-style.html">input file is structured</a>, including the <b>categories</b> and <b>tokens</b> that organize it...<br><br>

  <ul>

    <li> create category [VERS](tokens.html#VERScat). It is is required to check version control.<br><br>

    <li> (optional) create category [HEADER](tokens.html#HEADERcat) (if you want some documentation written to the output file)<br><br>

    <li> (optional) create the [IO](tokens.html#IOcat) category. Tokens in IO control how much and what kind of information is printed out.<br><br>

    <li> (optional) create the [HEADER](tokens.html#HEADERcat) category. This is intented to enable documentation to be printed to standard out.<br><br>

    <li> (optional) create the [SYMGRP](tokens.html#SYMGRPcat) category, which enables the user to set symmetry operations by hand. Normally symmetry operations are determined autmatically but there are some useful special cases where additional information may be supplied.<br><br>
      
    <li> Set up the crystal structure. (categories [STRUC](tokens.html#STRUCcat) and [SITE](tokens.html#SITEcat)). Si is zincblende, with a lattice constant 5.431 A. Because the input takes atomic units, the input file converts to a.u. by declaring a variable a0 (which later on, STRUC_ALAT uses):<br><br>

          <code>CONST   a0=.5292</code><br><br>

      Category [CONST](input-file-style.html) is special. It does not supply any tokens for input; its only purpose is to declare symbolic variables for use elsewhere.<br><br>

      You supply information defining the lattice structure in the [STRUC](tokens.html#STRUCcat) category through ALAT and PLAT; supply site position data in the [SITE](tokens.html#SITEcat) category. Each site must be associated with a chemical element, or species; you supply information about the "personality" of species in the [SPEC](tokens.html#SPECcat) category.<br><br>

      Si has one species and two atoms/cell, but because the ASA makes shape approximations to the potential, its use becomes problematic when dealing with open structures. To compensate two artificial "empty" sites (atomic number 0) are added to the basis. Thus for purposes of this calculation, the basis has four atoms: two Si atoms and two others just to fill the interstitial. Specify the number of sites in STRUC_NBAS.<br><br>

    <li>   Set up the [SPEC](tokens.html#SPECcat) category. Species labels (following ATOM=) can be any short string. In this case there are two species. In addition, use STRUC_NSPEC to specify how many species.<br><br>
      <i>Aside:</i>  If you do not supply STRUC_NSPEC, the parser will look for all the species as it can find in the SPEC category, and set NSPEC to the number it finds.<br><br>

      <i>Note:</i> Each species label (following ATOM) in the SITE category must match a label in the SPEC category. This is how the code determines the chemical character of element at each site.<br><br>

      These programs will splits species into symmetry-equivalent classes. In the ASA codes (**lm**, **lmgf**, **lmpg**) labels are assigned to each class. Class labels are derived from species labels by appending numbers to the species labels. They cannot exceed 8 characters, so usually you should limit the species labels to 6 or so characters (fewer if you have many atoms).<br><br>

      The atomic number [Z=](tokens.html#SPECcat) is required for each species.<br><br>

      You must choose a sphere radius, choosing one of [R=](tokens.html#SPECcat), [R/W=](tokens.html#SPECcat) or [R/A=](tokens.html#SPECcat). The choice of sphere radii is the Achilles heel in augmented wave methods. This is particularly true in the ASA because one must choose a balance between the following competing needs:<br><br>

      <ol>
        <li> the average potential at each MT surface should be similar<br><br>

        <li> small sphere overlaps<br><br>

        <li> spheres that are not too large<br><br>

        <li> small interstitial<br><br>
      </ol>
     
      It is difficult for the user to choose radii that satisfy (1), but a switch is available in program **lmchk** to determine these radii automatically, as described HEREXX. (The automatic input file generator, program **blm** uses this same algorithm to supply sphere radii.)<br><br>

      2. and 4. are diametrically opposed. In the ASA it is essential the the sum-of-sphere volumes equals the cell volume (i.e. the interstitial volume be zero) which means empty spheres are needed for all but close-packed structures. In the FP case one still prefers to make the interstitial as small as possible because the augmented wave basis is better than the envelope (interstitial) basis.<br><br>

      The sample input file makes a simple choice: all the spheres have the same radii, namely the average Wigner-Seitz radius <i>W</i>; radii are chosen as R/W=1.<br><br>
  </ul>
  <ul>
    <li>   create the [HAM](tokens.html#HAMcat) category. This category is responsable for reading the parameters defining the hamiltonian. Token QASA=0 specifies that Methfessel conventions are to be used for 2nd generation ASA moments [Q0, Q1, Q2](lmto.html#section2). Usually it doesn't make much difference, but QASA=3 is generally recommended. NSPIN=1 tells the program that the calculation is not spin polarized. (You can omit NSPIN all together; the token is optional as you can see by invoking the program with [--input](input-file-style.html#inputswitch).) Note also the token REL=F. This means use the Schrodinger equation rather than the scalar form of the Dirac equation. This is not usual.<br><br>
      <i>Aside:</i>  In versions prior to v7, NSPIN= and REL= belonged to the OPTIONS category.<br><br>

    <li>   (optional) create the [OPTIONS](tokens.html#OPTIONScat) category. The sample file uses this option<br><br>

              <code>OPTIONS ASA[ CCOR=F ADNF=F]</code><br><br>

      Brackets [..] are ncessary because OPTIONS_ASA_CCOR and OPTIONS_ASA_ADNF are tokens nested to the 3rd level. CCOR tells the ASA not to include the ``combined correction'' term (not generally recommended). ADNF=T would turn on a feature to automatically search for orbitals to downfold (remove from the basis). All these tags (the OPTIONS category itself, and OPTIONS_ASA_CCOR and OPTIONS_ASA_ADNF optional; see see below.
    <li>   (optional) create the [STR](tokens.html#STRcat) category. For 2nd generation ASA, the most important input is RMAX, which specifies the range of real-space structure constants, see discussion following [invoking lmstr](#lmstr).

    <li>   create the [BZ](tokens.html#BZcat) category. Note that the number of <i>k</i>-point divisions is specified through variable nk, which was declared and assigned to the value '4' in CONST.  

    <li>   create the [ITER](tokens.html#ITERcat) category. This contains tokens governing the maximum number of iterations (NIT=7) to converge to self-consistency, and convergence criteria (CONVC=1e-4 for charge, CONV=0 for energy change from prior iteration) that tell the program when you are sufficiently converged to self-consistency.

      ITER also holds the content of the MIX token, MIX=A,b=.8, a string. This string is passed charge mixer, which mixes the input density <i>n</i><sub>in</sub> (some trial density which generates the potential), with the output density <i>n</i><sub>out</sub> generated by the potential. The aim is to find the self-consistent density, where <i>n</i><sub>out</sub>= <i>n</i><sub>in</sub>. Some kind of mixing is usually necessary to stabilize convergence. 'A' tells the mixer to use the Anderson mixing scheme; it mixes 80% of output charge with 20% of input charge because b=.8. The [ASA density is completely specified](lmto.html#section2) by the logarithmic derivatives at the sphere boundary, via parameters P, and the multiple moments Q0,Q1,Q2. Theu the parameters mixed in the ASA are (P,Q): four numbers for each <i>l</i> channel and class. In the [FP implementation](FP.html), the full charge density is mixed. Other programs, e.g. [self-consistent empirical tight-binding](tbe.html), mix still other quantities. But the mixing commands are mostly common to all programs.

      There are many other choices in this string. Unfortunately difficult to make a general specification about the best mixing procedure to converge P and Q to self-consistency (in the full-potential case, it mixing the sphere and interstitial densities) If you have problems, The first thing to do is down the mixing parameter `b'. You can also use Broyden mixing (MIX=B) rather than Anderson mixing; indeed this is recommended. For difficult cases, see [linear-response-asa.html](linear-response-asa.html).

    <li>   (optional) create the [START](tokens.html#STARTcat) category to supply guesses for [P,Q](lmto.html#section2) for both Si and ES species. If you do not specify anything the program will use some defaults. In the example, the specifications of P and Q were commented out (note the '#') so default values will be used. Default values are crude, so the initial trial density will be far from the self-consistent one.

  </ul>

  <br><br>

  This completes the creation of the input file.<br><br>

</div>

##### _2\. Running Programs in the ASA Package_

**lmchk**:

One can use the program **lmchk** for some preliminary checks. This program's main function is to checks sphere packing and overlaps, but it has a number of other features. 

*    Get a list of neighboring shells surrounding each site:

        lmchk --shell si 

*    Move Atoms to minimize sphere overlaps (useful to locate empty spheres in low-symmetry geometries):

        lmchk --mino~z --wpos=_newpos_ file-ext ...
	
     Once created, **lmchk** and other programs can read these position files too:

        lmchk --rpos=_newpos_ file-ext ...

*    Invoke **lmchk** to check sphere overlaps:

        lmchk si

     Verify [output](http://titus.phy.qub.ac.uk/packages/LMTO/v7.11/doc/ASAsamples/out.si.lmchk) that the sum-of-sphere volumes equals the total volume.

**lmstr** can be used to:

*   Invoke **lmstr** to generate real-space structure constants:

        lmstr si

*   Change the verbosity of the output:

        lmstr si --pr41

    where 41 is the verbosity setting and can be changed.

**lm** can be used to:

*   Invoke **lm** to generate a self-consistent potential:

        lm si

**lmctl** can be used to:

*   Invoke **lmctl** to extract self-consistent P,Q.

##### _3\. Generating Energy Bands and Densities-of-States_

1.  Energy bands are typically plotted along specified symmetry lines, which information you supply through a symmetry-line file. Rather than create one, we will just copy one from the startup directory to syml.si.

        cp startup/syml.fcc ./syml.si

    To generate ASA bands, do:

        lm si --band:fn=syml

    The bands are generated and saved in file bands.si.

    The bands can be plotted with various plotting packages. Using **FPLOT** as an example; the **plbnds** program found within the **FPLOT** package can be used to plot with:

        echo -15 15 5 10 | plbnds -scl=13.6 -ef=0 si

    which creates a postscript file ps.si, better yet:

        echo -15 15 5 10 | plbnds -fplot -scl=13.6 -ef=0 si
    
    which creates an fplot command in the file plot.plnds. Use fplot to create a postscript file:

        fplot -f plot.plnds

    which creates a postscript file ps.dat. 

    The energy bands generator has an optional feature that enables you to highlight a particular orbital character in the energy bands. See color weights below.

2.  To generate the _total_ density-of-states (DOS), you don't have to do anything special. If BZ_SAVDOS=T, the total DOS is automatically written to disk every band pass. We will generate the partial DOS here. It is particularly simple in the ASA; in the FP code you need to set some extra switches.

    Instead of altering the ctrl file, we take advantage of the ability to alter the value of variables from command-line arguments. Since CONST doesn't change the value of variables already declared, command-line declarations take precedence over the CONST entries. Also, because we don't want the program to change the potential, we use interactive mode, and make it stop after the bands have been completed. Finally, we turn down the verbosity, since there will be so many _k_-points

        lm si -vnk=16 -vmet=1 --iactiv --pr30 

    You will encounter lines beginning with _QUERY:_. Press  return  to each query until you reach this line:

        QUERY: beta (def=0.8)? 

    which is asking you if you want to change the mixing parameter. Just type  q  and the program will stop.

    The ASA program has the ability to use files generated as a byproduct of the band pass to immediately generate DOS resolved by orbital and class. This is done using **lmdos**. Invoke

        lmdos si -vnk=16 -vmet=1 --iactiv --pr30 

    You will be queried with a prompt:

        Enter npts (def=501), emin and emax (def=-1,0): 

    enter:

        1001 -1 .3 RETURN

    or do the whole thing in one go:

        echo 1001 -1 .3 / | lmdos si -vnk=16 -vmet=1 --iactiv --pr30 

    If you have installed the FPLOT package, you can use **pldos** to create pictures of DOS from this file. For example,

        echo 8 7 / | pldos -fplot '-lst=1,2,3;4,5,6' dos.si 

    creates a file  dosp.dat  with two columns containing partial dos (one column combines dos 1,2,3 ---the Si dos and the other combines dos 4,5,6---the Es dos), and a file  plot.pldos  which holds the **fplot** command that creates a postscript file for this DOS.

        fplot -disp -pr10 -f plot.dos 

    creates and displays postscript file ps.dat. 

    _Note:_ there is a facility to draw two DOS in a single panel, one above the 'zero' and one below. This is particulary convenient in spin polarized cases when you want to compare the majority and minority DOS. Example: in file _dos.dat_ the majority DOS are in channels 1,3,5 (atom 1) and 7,9,11 (atom 2), and the minority DOS are in channels 2,4,6 (atom 1) and 8,10,12 (atom 2), invoke, e.g:

        echo 8 7 / | pldos -fplot '-lst=1,3,5;7,9,11' '-lst2=2,4,6;8,10,12' dos.dat

#### _Use of Color to Highlight Orbital Character in Energy Bands or DOS_

Starting with v6.16, the energy bands maker will allow you to generate weights together with the bands themselves. With this option, each energy levels is assigned a corresponding weight which can be used by a graphics package to highlight orbital character associated with them. The **fplot** graphics package, for example, can read these weights and to draw bands with continuously varying color fixed by the weights.

To see how the orbitals are ordered, run the energy band program (**lm** or **lmf**) with rather high verbosity (>51) and look for the tables following Makidx.
Here is a sample output for GaAs run using **lmf**. This case contains two sites, and two additional sites with floating orbitals.

             Makidx:  basis arranged in downfolding order:
          ib     low      intermed       high        .. offH ..
           1  spdf (16)       (0)        g (9)       0    0    0
          k2    sd (6)        (0)      pfg (19)     16    0    9
          k3     d (5)        (0)          (0)      22    0   28
           2  spdf (16)       (0)        g (9)      27    0   28
          k2     p (3)        (0)     sdfg (22)     43    0   37
          k3     s (1)        (0)          (0)      46    0   59
           3   spd (9)        (0)       fg (16)     47    0   59
          k2       (0)        (0)    spdfg (25)     56    0   75
           4   spd (9)        (0)       fg (16)     56    0  100
          k2       (0)        (0)    spdfg (25)     65    0  116
    
         Makidx:  hamiltonian dimensions Low, Int, High, Negl: 65 0 141 94
         kappa   Low   Int   High  L+I  L+I+H  Neglected
           1      50     0    50    50   100       0
           2       9     0    91     9   100       0
           3       6     0     0     6     6      94
          all     65     0   141    65   206      94
    
         Orbital positions in hamiltonian, resolved by l:
         Site  Spec  Total   By l ...
           1   Ga     1:27   1:1(s)   2:4(p)   5:9(d)   10:16(f) 17:17(s) 18:22(d) 23:27(d)                                     
           2   As    28:47   28:28(s) 29:31(p) 32:36(d) 37:43(f) 44:46(p) 47:47(s)                                              
           3   EA1   48:56   48:48(s) 49:51(p) 52:56(d)                                                                         
           4   EC1   57:65   57:57(s) 58:60(p) 61:65(d)                                                                         

The  Orbital Positions  table offers the most convenient way to find orbitals for color weights for color weights. The As atom, for example, occupies columns 28..47 in the hamiltonian. To assign a color to orbitals associated with As, invoke **lmf** with the --band switch, modified by the list of orbitals you want to highlight, i.e. --band~col=orbital-list~.... For example:

        lmf --band~col=28:47~syml ... 

will read information about what lines to plot from file syml.ext, and assign a color weight to orbitals 28..47\. For the general syntax of `orbital-list', see [Syntax of Integer Lists](Integer-list-syntax.html). Supposing you wanted to assign a color to just the As _p_ orbitals. As either of the above tables show, there are two sets of As _p_ orbitals (no local orbitals in this example). Invoke **lmf** with:

        lmf --band~col=29:31,44:46~syml ... 

**lmf** (or **lm**) will place into the bands band file bnds.ext both the eigenvalues and a a corresponding set of weights which can be used by a graphics package to highlight band features connected with them. For example, the **fplot** plotting package will use these weights to color the energy bands according to the weight. It is a very useful way to pick out a particular orbital character in the energy bands.

To see this feature illustrated, copy doc/ASAsamples/ctrl.gas to your current working directory. Verify that the potential is self-consistent:

        lmstr gas
        lm gas
        
Let's select pick out the orbitals of As _p_ character. To see where they are in the hamiltonian, do

        lm gas --pr51 --quit=ham
        
These tables should appear in the output:

             Makidx:  basis arranged in downfolding order:
          ib     low      intermed       high        .. offH ..
           1    sp (4)      d (5)   +                0    0    0
           2    sp (4)      d (5)   +                4    5    0
           3    sp (4)      d (5)   +                8   10    0
           4    sp (4)      d (5)   +               12   15    0
    
         Makidx:  hamiltonian dimensions Low, Int, High, Negl: 16 20 0 0
         kappa   Low   Int   High  L+I  L+I+H  Neglected
           -      16    20     0    36    36       0
    
         Orbital positions in hamiltonian, resolved by l:
         Site  Spec  Total    By l ...
           1   GA    1:4    1:1(s)   2:4(p)                                                                                     
           2   AS    5:8    5:5(s)   6:8(p)                                                                                     
           3   E1    9:12   9:9(s)   10:12(p)                                                                                   
           4   E2   13:16   13:13(s) 14:16(p)                                                                                   
        

The second atom is the As atom. Orbitals 6,7,8 are the As _p_ orbitals. Create a symmetry-lines file or copy one:

        cp ../startup/syml.fcc ./syml.gas 

Then generate the bands with

        lm gas --band~col=6,7,8~fn=syml
        

If you have the **FPLOT** graphics installed, you can draw a picture with

        echo -14,10,5,10 | plbnds -fplot -ef=0 -scl=13.6 -lt=1,col=1,.2,.1,colw=.1,.2,1 -lbl=L,G,X,W,G  bnds.gas
        fplot -f plot.plbnds
        
The blue lines are valence bands, The two heavy hole bands _px_ and _py_ character between 0 and -3 eV.

Another useful application is to distinguish between majority and minority bands in the spin-orbit coupled case. Suppose the basis consists of 80 orbitals. With SO coupling, the basis is doubled to 160 orbitals. Using option 
        ~col=1:80 
in the _--band_ switch assigns whatever weight the first spins contribute to the eigenvalue. The color of the energy bands as generated by fplot will be related to its spin character.

##### Pairs of color weights: examples using **lmf**

Both **lm** and **lmf** will generate _two sets_ of color weights.

For a ready-made example using **lmf** with spin orbit coupling, run the Co test case in the standard distribution. If lm is the top-level directory where the codes are installed, run

        ~/lm/fp/test/test.fp co 1      
        
This test has two sets of _spd_ orbitals in the basis. The _--band_ switch reads:

        --band~col=5:9,dup=9~col2=18+5:18+9,dup=9~fn=syml
        
Orbitals 5-9 are the Co _d_ orbitals of the first set (corresponding to EH...). String 'dup=#' is an extension of the standard integer-list syntax. It means: take the list generate so far, and replicate each element adding # to it. In this example orbitals 14:18 are the _d_ bands of the EH2 channel. The basis consists of 18 orbitals, which is doubled to 36 with spin-orbit coupling. Thus

        col=5:9,dup=9 
        and
        col2=18+5:18+9,dup=9
        
correspond respectively to the two pairs of _d_ orbitals of the first spin and of the second spin. Thus the first color weight singles out all spin-up states of _d_ character, the second spin-down _d_ states.

### [_Extended Tutorial_](/asaext/)

This is a link to the extended tutorial, explaining everything in more detail.