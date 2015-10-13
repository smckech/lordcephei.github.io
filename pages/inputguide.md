---
layout: page-fullwidth
title: "Input File Guide"
subheadline: ""
show_meta: false
teaser: ""
permalink: "/inputguide/"
header: no
---

### _Table of Contents_
{:.no_toc}
*  Auto generated table of contents
{:toc}  

### _Purpose_
_____________________________________________________________
This guide aims to detail the structure and use of the input file and related topics. Additionally, the guide details the different categories that the input file uses and the tokens that can be set within each category.

### _Input Methods: The Input File_
_____________________________________________________________

##### _Introduction_
Here is a sample input file for the compound $$ Bi_2Te_3 $$ written for the **lmf**{: style="color: blue"} package.

<div onclick="elm = document.getElementById('sampleinput'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click to show.</div>
{::nomarkdown}<div style="display:none;padding:25px;" id="sampleinput">{:/} 

        categories                  tokens
        ↓                            ↓
        
        VERS    		LM:7 FP:7
        HAM     		AUTOBAS[PNU=1 LOC=1 LMTO=3 MTO=1 GW=0]
                                GMAX=8.1
        ITER    		MIX=B2,b=.3  NIT=10  CONVC=1e-5
        BZ      		NKABC=3  METAL=5  N=2 W=.01
        STRUC
                                NSPEC=2  NBAS=5  NL=4
                                ALAT=4.7825489
                                PLAT= 1     0          4.0154392
                                -0.5   0.8660254  4.0154392
                                -0.5  -0.8660254  4.0154392
        SPEC
                                ATOM=Te         Z= 52  R= 2.870279
                                ATOM=Bi         Z= 83  R= 2.856141
        SITE
                                ATOM=Te         POS=  0.0000000   0.0000000   0.0000000
                                ATOM=Te         POS= -0.5000000  -0.8660254   1.4616199
                                ATOM=Te         POS=  0.5000000   0.8660254  -1.4616199
                                ATOM=Bi         POS=  0.5000000   0.8660254   0.8030878
                                ATOM=Bi         POS= -0.5000000  -0.8660254  -0.8030878

{::nomarkdown}</div>{:/}

Each element of data follows a **token**{: style="color: blue"}. The **token**{: style="color: blue"} tells the reader what the data signifies.  

Each token belongs to a category. **VERS**{: style="color: red"}, **ITER**{: style="color: red"}, **BZ**{: style="color: red"}, **STRUC**{: style="color: red"}, **SPEC**{: style="color: red"}, **SITE**{: style="color: red"} are categories that organize the input by topic.  
Any text that begins in the first column is a category.  

Thus the full identifier consists of a sequence of tags, e.g. **BZ**{: style="color: red"}\_**METAL**{: style="color: blue"}, whose components are stuctured like a tree.  

Sometimes the identifier has three tags, e.g. **HAM**{: style="color: red"}\_**AUTOBAS**{: style="color: green"}\_**LOC**{: style="color: blue"}. The leading tag is the **category**{: style="color: red"}; the last is the **token**{: style="color: blue"}.  

##### _Tags, Categories and Tokens_

The input file offers a very flexible free format: tags identify data to be read by a program, e.g.

        W=.01

reads a parameter (.01) from tag W.  

Tags are organized in a tree structure: a tag connected to the trunk of the tree is called a **category**{: style="color: red"}. A category holds information for a family of data, for example **BZ**{: style="color: red"} contains parameters associated with Brillouin zone integration. The entire input system has at present a grand total of 17 categories, though any one program uses only a subset of them.  

A tag in the outermost branch of the tree points to actual data and is called a **token**{: style="color: blue"}. Each category contains a collection of tags within it −− usually tokens, but sometimes an intermediate tag which contains a collection of tokens within it, as described below.  

<div onclick="elm = document.getElementById('tagexample'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click here for an example.</div>
{::nomarkdown}<div style="display:none;padding:25px;" id="tagexample">{:/} 

Suppose for example, the calculation you want to do e −‘; some have two (−−‘). requires Brillouin zone integrations of a metallic material, which you plan to to carry out using the Methfessel-Paxton sampling method.  

Two parameters: polynomial order n and gaussian width w. 
Two tags are used to identify them: internally they are represented as **BZ_N** and **BZ_W**, but in the input file they are usually expressed as follows: 

        BZ	N=2	W=.01

This format style is the most commonly used because it is clean and easy to read; but it conceals the tree structure a little. The same data can equally be written:

        BZ[	N=2 	W=.01]

Now the tree structure is apparent: [..] delimits the scope of tag **BZ**.  

The root tag, or category, is BZ in this case. Any tag that starts in the first column is a category. Thus any non-white character appearing in the first column automatically starts a new category, and also terminates any prior category, if it hasn’t been terminated already. N= and W= mark tokens **BZ_N** and **BZ_W**.  

Apart from the special use of the first column to identify categories, data is free-format, apart from a few mild exceptions. Thus:

        BZ      N=2
                W=.01
        BZ	W=.01	N=2
        BZ[	W=.01	N=2]

all represent the same information.  

_Note:_{: style="color: red"} if two categories appear in an input file, only the first is used. Subsequent categories are ignored. Generally, only the first tag is used when more than one appears within a given scope.

{::nomarkdown}</div>{:/}

Usually the tag tree has only two levels (category and token) but not always. For example, data associated with atomic sites must be supplied for each site. In this case the tree has three levels, e.g. **SITE_ATOM_POS**. Site data is typically represented in a format along the following lines:

        SITE    ATOM=Ga  POS=  0   0   0    RELAX=T
                ATOM=As  POS= .25 .25 .25
                ATOM=...
                ...
        END

The scope of  **SITE**  starts at "SITE"  and terminates just before "END". There will be multiple instances of the  **SITE_ATOM**  tag, one for each site. The scope of the first instance begins with the first occurrence of  ATOM  and terminates just before the second: 

        ATOM=Ga  POS=  0   0   0    RELAX=T 

And the scope of the second **SITE_ATOM** is 

        ATOM=As  POS= .25 .25 .25 

Note that **ATOM** simultaneously acts like a token pointing to data (e.g. Ga) and as a tag holding tokens within it, in this case **SITE_ATOM_POS** and (for the first site) **SITE_ATOM_RELAX**.  

Some tags are required; others are optional; still others (in fact most) may not be used at all by a particular program. If a code needs site data, **SITE_ATOM_POS** is required, but **SITE_ATOM_RELAX** is probably optional, or not read at all.  

##### _Preprocessor_

Input lines are passed through a preprocessor, which provides a wide flexibility in how input files are structured. The preprocessor has many features in common with a programming language, including the ability to declare variables, evaluate algebraic expressions; and it has constructs for branching and looping, to make possible multiple or conditional reading of input lines.  

For example, supposing through a prior preprocessor instruction you have declared a variable range, and it has been assigned the value 3. This line in the input file:

        RMAX={range+1/4}

is parsed and turned in to:

        RMAX=3.25
 
The preprocessor treats text inside brackets {...} as an expression (usually an algebraic expression), which is evaluated and rendered back as an ASCII string.  

The preprocessor’s programming language makes it possible for a single file to serve as input for many materials systems in the manner of a database; or as documentation. Also you can easily vary input conditions in a parameteric fashion.  

Many files other than  `ctrl.ext‘ are first parsed by the preprocessor: files for site positions, Euler angles for noncollinear magnetism, among others.  

### _Input Methods: Command Line Arguments_

Command-line arguments allow for a second, very convenient input stream. In particular you can use them to declare and assign values to variables which can modify the input file via the preprocessor. A number of other directives are also possible. Here is an example:

         lmchk -vns=4 -vnm=5 --wpos=pos ... 

lmchk reads site positions, checks for augmentation sphere overlaps, and can print out a wealth of other structural data.  

The switches tell lmchk to create variables  ns  and  nm  (assigning values 4 and 5 respectively), and includes an instruction to dump site positions to file  pos.ext.  

Following unix style, switches always begin with `−‘. There are many command-line arguments that are specific to a particular executable; here some generic ones common to most or all programs are described.  

Some switches have a single "−"; some have two ("−−"). Those with a single "−" tend to have an "assignment" function, such as a variables declaration (eg −vx=3), while those with two tend to control program flow (eg. −−wpos=name). Sometimes there is not a clear distinction , e.g. −−pr and −pr  perform the same function, namely setting the output verbosity via the command line.  

You can also put these switches in the  CMD  category in the input file. The function is similar to a command-line argument, but not identical, since preprocessor has already read the input file before the "CMD" switches are read. Thus the  "−v"  and  "−c"  variable declarations have no effect.  

Some switches common to most or all programs.  
<div onclick="elm = document.getElementById('commonSwitch'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';">Click to show common switches.</div>
{::nomarkdown}<div style="display:none;padding:25px;" id="commonSwitch">{:/} 

        --h                     lists switches for that program and quits
                                (warning: sometimes documentation is slightly out of date)

        --input                 same as turning on HELP=T in category IO; see HELP= above.

        --show                  same as turning on SHOW=T in category IO; see SHOW= above.

        --showp                 prints out input file after having run through the
                                preprocessor, and exits.

        --pr#1[,#2]             sets print verbosities.  #2 is verbosity for generation
         -pr#1[,#2]             of the potential, and assumes the value of #1 unless specified.

        --time=#1[,#2]          prints out a summary of timings in various branches of
                                the code at the close of program execution.  Timings
                                are kept to a nesting level of #1.  If #2 is
                                nonzero, timings are printed 'on the fly'

        --iactive               turns on 'interactive' mode.  User is queried at
                                various points, such as whether to continue iterations.
                                You can specify this in ctrl file 'IO IACTIV='
        --no-iactive            turns off 'interactive' mode

        -v"name=expr"           declares a numeric variable and assigns to the value of
                                expression `expr'. Be advised that only the first
                                declaration of a variable is used.  Later declarations
                                have no effect.  In addition to the declaration
                                `name=...'  there are assignment operators
                                '*=','/=','+=','-=','^=' modify existing variables,
                                following C syntax, as described in the preprocessor documentation.

        -c"name=strn"           declares a character variable and assigns to value `strn'

        --rpos=filnam           After reading input file, read site positions from "filnam"

        --fixpos[:tol=#]        Adjust positions slightly, rendering them as
                                exactly as possible consistent with the symmetry group

{::nomarkdown}</div>{:/}

### _Input File Categories_
_____________________________________________________________
This section details the various categories and tokens used in the input file.

##### _Preliminaries_
_Note:_{: style="color: red"} The tables below list the input systems’ tokens and their function. Tables are organized by category.

*   The Arguments column refers to the cast belonging to the token ("l", "i", "r", and "c" refer to logical, integer, floating-point and character data, respectively)
*   The Program column indicates which programs the token is specific to, if any
*   The Optional column indicates whether the token is optional (Y) or required (N)
*   The Default column indicates the default value, if any
*   The Explanation column describes the token’s function.

##### _BZ_
Category BZ holds information concerning the numerical integration of quantities such as energy bands over the Brillouin Zone (BZ). The LMTO programs permit both sampling and tetrahedron integration methods. Both are described in bzintegration.html, and the relative merits of the two different methods are discussed. As implemented both methods use a uniform, regularly spaced mesh of k-points, which divides the BZ into microcells as described here. Normally you specify this mesh by the number of divisions of each of the three primitive reciprocal lattice vectors (which are the inverse, transpose of the lattice vectors PLAT); NKABC below.

These tokens are read by programs that make hamiltonians in periodic crystals (lmf,lm,lmgf,lmpg,tbe). Some tokens apply only to codes that make energy bands, (lmf,lm,tbe).

<div onclick="elm = document.getElementById('bztable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><b>Click to show table.</b></div>
{::nomarkdown}<div style="display:none;padding:25px;" id="bztable">{:/} 

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | - 
GETQP | l | | Y | F | Read list of k-points from a disk file. This is a special mode, and you normally you would let the program choose its own mesh by specifying the number of divisions (see NKABC). <br> If token is not parsed, the program will attempt to parse NKABC.
NKABC | l to 3 i | | N | | The number of divisions in the three directions of the reciprocal lattice vectors. k-points are generated along a uniform mesh on each of these axes. (This is the optimal general purpose quadrature for periodic functions as it integrates the largest number of sine and cosine functions exactly for a specified number of points.) <br><br> The parser will attempt to read three integers. If only one number is read, the missing second and third entries assume the value of the first. <br><br> Information from NKABC, together with BZJOB below, contains specifications equivalent to the widely used “Monkhorst Pack” scheme. But it is more transparent and easier to understand. The number of k-points in the full BZ is the product of these numbers; the number of irreducible k-points may be reduced by symmetry operations.
PUTQP | l | | Y | F | If T, Write out the list of irreducible k-points to file qpts, and the weights for tetrahedron integration if available.
BZJOB | l to 3 i | | Y | 0 | Controls the centering of the k-points in the BZ: <br>0: the mesh is centered so that one point lies at the origin <br>1: points symmetrically straddle the origin <br><br>Three numbers are supplied, corresponding to each of the three primitive reciprocal lattice vectors. As with NKABC if only one number is read the missing second and third entries assume the value of the first.
METAL | i | lmf, lm, tbe | Y | 1 | Specifies how the weights are generated for BZ integration. <br><br>Numerical quadrature is used to to accumulate the output density or any BZ-integrated property.<br>In insulators, each point in the full BZ gets equal weight; in metals, the weights depend on the Fermi level $$ E_F $$, which must be determined from the eigenvalues. <br>There are these possibilities: {::nomarkdown} <ul><li>either the weights for each k are known in advance, as is the case for insulators;</li><li>{:/} $$ E_F $$ {::nomarkdown}must be determined before BZ integrations are performed (tetrahedron integration).<br><br>In this more difficult case, there are two possibilities:<ul><li>the weight at k does not depend on values of neighboring k (the case for Methfessel-Paxton sampling integration)</li><li>the weight at k does depend on values of neighboring k (tetrahedron integration). </li></ul></li></ul><br> The first case is easy to handle. For the latter several strategies have been developed.<br>The METAL token accepts the following:<ul><li>System assumed to be an insulator; weights determined a priori.</li><li>Eigenvectors are written to disk, in which case the integration for the charge density can be deferred until all the bands are obtained. This option is available only for the ASA:<br>When accumulating just the spherical part of the charge, eigenvector data can be contracted over m, reducing memory demands.</li><li>Integration weights are read from file wkp.ext, would have been generated in a prior band pass. If wkp.ext is missing or unsuitable, the program will temporarily switch to METAL=3.</li><li>Two band passes are made; the first generates only eigenvalues to determine {:/} $$ E_F $$ {::nomarkdown}. It is slower than METAL=2, but it is more stable which can be important in difficult cases.</li><li>Three distinct Fermi levels are assumed and weights generated for each. After {:/} $$ E_F $$ {::nomarkdown} is determined, the actual weights are calculated by quadratic interpolation through the three points. The three Fermi levels are gradually narrowed to a small window around the true {:/} $$ E_F $$ {::nomarkdown} in the course of the self-consistency cycle.<br>This scheme works for sampling integration where the k-point weights depend on {:/} $$ E_F $$ {::nomarkdown} only and not eigenvalues at neighboring k.<br>You can also use this scheme in a mixed tetrahedron/sampling method:<br>Weights for the band sum are determined by tetrahedron, but the charge density is integrated by sampling.<br>It works better than straight sampling because the total energy is variational in the density.</li><li>like METAL=3 in that two passes are made but eigenvectors are kept in memory so there is no additional overhead in the first pass.<br>This is the recommended mode for lmf unless you are working with a large system and need to conserve memory.</li></ul>{:/}<br>The ASA code lm implements METAL=0,1,2; the FP lmf implements METAL=0,2,3,4,5.
TETRA | 1 | lmf,lm,tbe | Y | T | Selects BZ integration method<br>0: Methfessel-Paxton sampling integration. Tokens NPTS, N, W, EF0, DELEF (see below) are relevant to this integration scheme.<br>1: tetrahedron integration, with Bloechl weights
N | i | lmf,lm,tbe | Y | 0 | Polynomial order for sampling integration; see Methfessel and Paxton, Phys. Rev. B, 40, 3616 (1989). (Not used with tetrahedron integration or for insulators)<br><br>0: integration uses standard gaussian method<br><br>>0: integration uses generalized gaussian functions, i.e. polynomial of order N * gaussian to generate integration weights<br><br>−1: use the Fermi function rather than gaussians to broaden the δ-function. This generates the actual electron (fermi) distribution for a finite temperature.<br><br>100: By default, if a gap is found separating occupied and occupied states, the program will treat the system as and insulator, even when MET>0. To suppress this, add 100 to N (use −101 for Fermi distribution).
W | r | lmf,lm,tbe | Y | 5e-3 | {::nomarkdown} <ul><li>Case BZ_N\>0:<br>Broadening (Gaussian width) for Gaussian sampling integration (Ry).</li><li>Case BZ_N\<0:<br>temperature, in Ry. (the sampling weights are computed from the Fermi function instead of the error function).</li></ul>{:/}<br>Not used with tetrahedron integration or for insulators.
EF0 | r | lmf,lm,tbe | Y | 0 | Initial guess at Fermi energy. Used with BZ_METAL=4.
DELEF | r | lmf,lm,tbe | Y | 0.05 | Initial uncertainty in Fermi level for sampling integration.<br>Used with BZ_METAL=4.
ZBAK | r | lmf,lm | Y | 0 | Homogeneous background charge
SAVDOS | i | lmf,lm,tbe | Y | 0 | {::nomarkdown} <ul><li>0: does not save dos on disk</li><li>1: writes the total density of states on NPTS mesh points to disk file dos.ext.</li><li>2: Write weights to disk for partial DOS (In the ASA this occurs automatically).</li><li>4: Same as (2), but write weights m-resolved.</li></ul>{:/}You may also cause lm or lmf to generate m-resolved dos using the –pdos command-line argument.<br>NB: you must turn OFF all symmetry operations to produce correct results. (The extra inversion symmetry in the k-points from time-reversal symmetry is still allowed.)<br><br>Note:<br>SAVDOS>0 requires BZ_NPTS and BZ_DOS also.
DOS | 2 r | | Y | -1,0 | Energy window over which DOS accumulated. Needed either for sampling integration or if SAVDOS>0.
NPTS | i | | Y | 1001 | Number of points in the density-of-states mesh used in conjunction with sampling integration. Needed either for sampling integration or if SAVDOS>0.
EFMAX | r | lmf,lm,tbe | Y | 2 | Only eigenvectors whose eigenvalues are less than EFMAX are computed; this improves execution efficiency.
NEVMX | i | lmf,lm,tbe | Y | 0 | \>0 : Find at most NEVMX eigenvectors<br>=0 : program uses internal default<br>\<0 : no eigenvectors are generated (and correspondingly, nothing associated with eigenvectors such as density.)<br><br>Caution: if you want to look and partial DOS well above the Fermi level (which comes out around 0), you must set EFMAX and NEVMX high enough to encompass the range of interest.
ZVAL | r | | Y | 0 | Number of electrons to accumulate in BZ integration. Normally zval is computed by the program.
NOINV | l | lmf,lm,tbe | Y | F | Suppress the automatic addition of the inversion to the list of point group operations. Usually the inversion symmetry can be included in the determination of the irreducible part of the BZ because of time reversal symmetry. There may be cases where this symmetry is broken:<br>e.g. when spin-orbit coupling is included or when the (beyond LDA) self-energy breaks time-reversal symmetry. In most cases, the program will automatically disable this addition in cases that knows the symmetry is broken.
FSMOM | 2 r | lmf,lm | Y | 0 0 | Set the global magnetic moment (collinear magnetic case). In the fixed-spin moment method, a spin-dependent potential shift Beff is added to constrain the total magnetic moment to value assigned by FSMOM=. No constraint is imposed if this value is zero (the default).<br>Optional second arg supplies an initial Beff. It is applied whether or not the first argument is 0. If arg#1 ≠ 0, Beff is made consistent with it.
DMAT | l | lmf,lmgf | Y | F | Calculate the density matrix.
INVIT | l | lmf,lm | Y | F | Enables inverse iteration generate eigenvectors (this is the default). It is more efficient than the QL method, but occasionally fails to find all the vectors. When this happens, the program stops with the message:<br> DIAGNO: tinvit cannot find all evecs<br>If you encounter this message set INVIT=F.
EMESH | r | lmgf,lmpg | Y | 10,0,-1,... | Parameters defining contour integration for Green’s function methods.<br><br>Element: {::nomarkdown} <ol><li>number of energy points n</li><li>contour type:<ul><li>0: Uniform mesh of nz points: Real part of energy z between emin and emax</li><li>1: Same as 0, but reverse sign of Im z</li><li>10: elliptical contour</li><li>11: same as 10, but reverse sign of Im z</li><li>100s digit used for special modifications</li><li>Add 100 for nonequil part using Im(z)=delne</li><li>Add 200 for nonequil part using Im(z)=del00</li><li>Add 300 for mixed elliptical contour + real axis to find fermi level</li><li>Add 1000 to set nonequil part only.</li></ul></li><li>lower bound for energy contour emin (on the real axis)</li><li>upper bound for energy contour emax, e.g. Fermi level (on the real axis)</li><li>(elliptical contour) eccentricity: ranges between 0 (circle) and 1 (line)<br>(uniform contour) Im z</li><li>(elliptical contour) bunching parameter eps : ranges between 0 (distributed symmetrically) and 1 (bunched toward emax)<br>(uniform contour) not used</li><li>(nonequilibrium GF, lmpg) nzne = number of points on nonequilibrium contour</li><li>(nonequilibrium GF, lmpg) vne = difference in fermi energies of right and left leads</li><li>(nonequilibrium GF, lmpg) delne = Im part of E for nonequilibrium contour</li><li>(nonequilibrium GF, lmpg) substitutes for delne when making the surface self-energy.</li></ol> {:/}
MULL | i | tbe | Y | 0 | Mulliken population analysis. Mulliken population analysis is also implemented in lmf, but you specify the analysis with a command-line argument.

{::nomarkdown}</div>{:/}

##### _CONS_

##### _DYN_
Contains parameters for molecular statics and dynamics.

<div onclick="elm = document.getElementById('dyntable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><b>Click to show table.</b></div>
{::nomarkdown}<div style="display:none;padding:25px;" id="dyntable">{:/} 

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
T | E | S | T | 1 | 2
T | E | S | T | 3 | 4<br>5

{::nomarkdown}</div>{:/}

##### _EWALD_

##### _HAM_

##### _GF_

##### _GW_

##### _HEADER_

##### _IO_

##### _ITER_

##### _OPTICS_

##### _OPTIONS_

##### _PGF_

##### _SITE_

##### _SPEC_

##### _STR_

##### _START_

##### _STRUC_

##### _SYMGRP_

##### _VERS_
