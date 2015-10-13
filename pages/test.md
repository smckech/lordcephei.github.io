---
layout: page-fullwidth
title: "LM Suite - Test Tutorial"
subheadline: ""
show_meta: false
teaser: ""
permalink: "/test/"
header:
    image_fullwidth: "header_drop.jpg"
---

#Basic LM tutorial 

To perform a very basic QSGW calculation and set up for more accurate/specific calculations of SbSBr (or any other structure starting from POSCAR) the following steps can be used as a basic tutorial. This tutorial will take you from POSCAR to qsGW in 6 very simple steps. 

###_Step 1: Crystal structure_

<div onclick="elm = document.getElementById('box1'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box1">{:/} 

The first step is to extract the site/crystall structure from the POSCAR file. This can be achieved in two methods using ”*poscar2init*{: style="color: blue"}” or ”*poscar2site*{: style="color: blue"}”, both write output as *stdout*{: style="color: green"} while ”*poscar2site*{: style="color: blue"}” also creates a file named ”*site*{: style="color: blue"}”. To use just execute in the directory with the POSCAR file. The output of the two executables are very similar, ”Site” file is more convenient when the *ctrl.ext*{: style="color: blue"} file has been prepared and only the ”*site.ext*{: style="color: blue"}” is needed, while *init.ext*{: style="color: blue"} can be used to create a simple template input file (hence recommended for 1st runs). 

To create ”*init.ext*{: style="color: blue"}” invoke the following command in the directory containing the POSCAR, 

	$poscar2init > init.ext 

(ext should be replaced by an appropriate extension for the material e.g Si for silicon,mapi for NH3CH3PbI3).  

{::nomarkdown}</div>{:/}

###_Step 2: Initial control file_

<div onclick="elm = document.getElementById('box2'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box2">{:/} 

With the crystal structure at hand the lm package is capable of creating a basic control file for you, simply use the command

	$blm −−gw init.ext

which will create *actrl.ext*{: style="color: blue"} which is almost ready for DFT calculations, the ”a” in ”*actrl.ext*{: style="color: blue"}” is there so existing *ctrl.ext*{: style="color: blue"} files are not over written. The *−−gw*{: style="color: green"} switch produces an *init.ext*{: style="color: blue"} file more suited for GW calculation. The file is set-up with very basic tokens/switches and the rest are set as their default values, of course more can be specified manually in the ctrl file. The list of input switches are available [here](http://titus.phy.qub.ac.uk/packages/LMTO/tokens.html), this is slightly outdated but all the information there should be enough for a full DFT and QSGW plus some extra features; You can also use ”*−−input*{: style="color: green"}”

	$ lmf −−input

to print out a full list of all available input options.

{::nomarkdown}</div>{:/}

###_Step 3: DFT set up_

<div onclick="elm = document.getElementById('box3'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box3">{:/} 

To run GW calculation you must first get a self consistent DFT calculation, here are the basic steps needed for the full-potential(FP) approach (this is more versatile than atomic sphere approximation (ASA) but also slower). start by making a ctrl file that the lmto package can access:

	$cp actrl.ext ctrl.ext

before starting with FP the atomistic densities and the basis are need, these parameters can be obtained by:

	$lmfa ctrl.ext

There are two important result to take note of at this stage; The first is the GMAX written at the end of the stdout, the other is the *basp0.ext*{: style="color: blue"} file which contains the basis parameters (the ”0” is there not to over write previous *basp.ext*{: style="color: blue"} files, note that the basis set can be set in the ctrl file as well). To perform a DFT-FP calculation add the GMAX from stdout to the HAM GMAX in the ctrl file and move *basp0.ext*{: style="color: blue"} to *basp.ext*{: style="color: blue"}. Some times it is necessary to repeat this step once to avoid crashes in the future.

{::nomarkdown}</div>{:/}

###_Step 4: DFT_

<div onclick="elm = document.getElementById('box4'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box4">{:/} 

Run a DFT-FP calculation by 

	$ lmf ctrl.ext

This may give you an error regarding the k-mesh settings. The k-mesh division NKABC has been set to zero which is not valid, to proceed change the value in ctrl file (under BZ NKABC) or execute

	$ lmf ctrl.ext -vnk=4

This will set NKABC as 4x4x4.This should be enough for a self consistent calculation, you may also have to set a value for the number of iteration in *ITER NIT*{: style="color: green"} or *-vnit=*{: style="color: green"} in command line. Other parameters which are important even for a basic preliminary DFT calculation may include things such as spin polarization and spin orbit coupling; While you may find other parameters become key for certain materials.

{::nomarkdown}</div>{:/}

###_Step 5: GW set up(almost there)_

<div onclick="elm = document.getElementById('box5'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box5">{:/} 

once self consistency has been achieved with the desired parameters within DFT, you can start a qsGW calculation. First you have to create input files for the GW package, once again the lmto package make a template for you by using

	$ echo -1 | lmfgwd ctrl.ext alternatively invoke

	$ lmfgwd ctrl.ext

to see all options available through *lmfgwd*{: style="color: green"}. This should create a number of files one of which is *GWinput*{: style="color: blue"}; this serves as the input file for all GW based calculations. Variables are documented within the file, further information can be found in the Darsbury lecture power points and documentation folder of the lm package.

{::nomarkdown}</div>{:/}

###_Step 6: Sanity check_

<div onclick="elm = document.getElementById('box6'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box6">{:/} 

To check that the GW files created are *comOPTICS MODE=pelete*{: style="color: green"} for a qsgw run, and to check for some errors simply run:

	$ echo -2 | lmfgwd ctrl.ext

with this method crashes during GW runs are minimized.

{::nomarkdown}</div>{:/}

###_Step 7: qsGW (and we are done)_

<div onclick="elm = document.getElementById('box7'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box7">{:/} 

To run qsGW calculation the following command is invoked 

	$lmgwsc ext

once self-consistency has been achieved the file ”*sigm.ext*{: style="color: blue"}” (the self energy, Σ from Hedin’s equations) can be used with lmf. Running a FP calculation in the same directory will now include the electronic self energy obtained from qsGW. Using the lmf with *sigm.ext*{: style="color: blue"} allows for qsGW level calculations of Band structure, optics, DOS ....... with much faster speed as the lmf uses the single particle approximation.

{::nomarkdown}</div>{:/}

###_Bonus Step_

<div onclick="elm = document.getElementById('box8'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here to show or hide.</div>
{::nomarkdown}<div style="display:none;" id="box8">{:/} 

####Band structure

Creating the band structure is fairly simple. All you have to do is create a file which specifies which branches you want and how fine a mesh you want it for. So create a file called syml.ext; the syntax for this file is quite intuitive, each line provides information for each band structure branch to be outputted; Simply specify the number of points in the branch, starting vector and final vector; for example branch Γ to R with 50 k points will be :

	50 0.0 0.0 0.0 0.5 0.5 0.5

there are sample syml file in the ASAsampples folder. once you have your syml.ext file simply run

	$ lmf ctrl.ext −−band:fn=syml −−rs=1,0

The switch *−−band:fn=syml*{: style="color: green"} specifies a band structure calculation with the appropriate data, the second switch *−−rs=1,0*{: style="color: green"} tells the program to read from the *rst.ext*{: style="color: blue"} file (1st digit is 1) and not to write to the *rst.ext*{: style="color: blue"} file (2nd digit is 0), this is recommended so you always use the same self-consistent density with out altering each run.

####Optics

Calculating optical properties are slightly dependant on a number of switches used for your calculation.

#####Procedure A

First I will discuss for the case of where number of spins (*-vnsp=1*{: style="color: green"}) or spin orbit coupling is taken into about through ”L·S” (i.e. *-vnsp=2*{: style="color: green"} and *-vso=1*{: style="color: green"}). The simplest procedure will be to set *OPTICS MODE=1*{: style="color: green"} and set energy window and mesh density through *OPTICS WINDOW=a b*{: style="color: green"} and *OPTICS=NPTS*{: style="color: green"} respectively, note a and b are in units Rydberg. Now run the program again Cartesian

	$ lmf ctrl.ext −−rs=1,0

this will create a file called *opt.ext*{: style="color: blue"} which is the imaginary part of the dielectric function. the format of the file is:

	Energy εix εiy εiz

where x y and z are the real space directions.

#####Procedure B

For the case where *-vso=3*{: style="color: green"} the procedure has to be preformed through two steps. Once with *OPTICS MODE=1*{: style="color: green"} and once with *OPTICS MODE=2*{: style="color: green"}, in this mode the *opt.ext*{: style="color: blue"} files are spin polarized.

#####Joint Density of States

JDOS is calculated in the same manor as εi but with *OPTICS MODE=-1*{: style="color: green"} for the case of *-vnsp=1*{: style="color: green"} or *-vnsp=2*{: style="color: green"} and *-vso=1*{: style="color: green"}, and with *OPTICS MODE=-1*{: style="color: green"} and *OPTICS MODE=-2*{: style="color: green"} for case *-vnsp=2*{: style="color: green"} and *-vso=3*{: style="color: green"}.

#####Other procedures

There are currently 13 optics mode, a brief description of each is given through:

	$ lmf −−input

Optics mode 8 (non-equilibrium absorption) and 9 (non-equilibrium emission) will be documented separately.

{::nomarkdown}</div>{:/}




