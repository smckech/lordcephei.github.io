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
Two tags are used to identify them: internally they are represented as **BZ\_N** and **BZ_W**, but in the input file they are usually expressed as follows: 

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

<div onclick="elm = document.getElementById('bztable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="bztable">{:/}

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

##### _CONST_

Category CONST enables the user to declare variables for use in algebraic expressions. The syntax is a string of declarations inside the category, e.g:

        CONST   a=10.69 nspec=4+2

Variables declared this way are similar to, but distinct from variables declared for the preprocessor, such as 

        % const nbas=5

In the latter case the preprocessor makes a pass, and may use expressions involving variables declared by e.g. "% const nbas=5" to alter the structure of the input file.  

Variables declared for use by the preprocessor lose their definition after
the preprocessor completes.  

The following code segment illustrates both types:

~~~
% const nbas=5
CONST   a=10.69 nspec=4
SPEC    ALAT=a  NSPEC=nspec NBAS={nbas}
~~~

After the preprocessor compiles, the input file appears as:

~~~
CONST   a=10.69 nspec=4
SPEC    ALAT=a  NSPEC=nspec NBAS=5
~~~

When the CONST category is read (it is read before other categories),
variables a and nspec are defined and used in the SPEC category.

##### _DYN_
Contains parameters for molecular statics and dynamics.

<div onclick="elm = document.getElementById('dyntable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="dyntable">{:/}

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
NIT | i | lmf, lmmc, tbe | Y | | maximum number of relaxation steps (molecular statics)
SSTAT[...] | | lm, lmgf | Y | | (noncollinear magnetism) parameters specifying how spin statics (rotation of quantization axes to minimze energy) is carried out
SSTAT\_MODE | i | lm, lmgf | N | 0 | 0: no spin statics or dynamics<br>-1: Landau-Gilbert spin dynamics<br>1: spin statics: quantization axis determined by making output density matrix diagonal<br>2: spin statics: size and direction of relaxation determined from spin torque<br>Add 10 to mix angles independently of P,Q (Euler angles are subject to mixing to accelerate convergence)<br>Add 1000 to mix Euler angles independently of P,Q
SSTAT\_SCALE | i | lm, lmgf | N | 0 | (used with mode=2) scale factor amplifying magnetic forces
SSTAT\_MAXT | i | lm, lmgf | N | 0 | maximum allowed change in angle
SSTAT\_TAU | i | lm, lmgf | N | 0 | (used with mode=-1) time step
SSTAT\_ETOL | i } lm, lmgf | N | 0 | (used with mode=-1) Set tau=0 this iter if etot-ehf>ETOL
MSTAT[...] | | lmf, lmmc, tbe | Y | | (molecular statics) parameters specifiying how site positions are relaxed given the internuclear forces
MSTAT\_MODE | i | lmf, lmmc, tbe | N | 0 | 0: no relaxation<br>4: relax with conjugate gradients algorithm (not generally recommended)<br>5: relax with Fletcher-Powell alogirithm. Find minimum along a line; a new line is chosen. The Hessian matrix is updated only at the start of a new line minimization. Fletcher-Powell is more stable but usually less efficient then Broyden.<br>6: relax with Broyden algorithm. This is essentially a Newton-Raphson algorithm, where Hessian matrix and direction of descent are updated each iteration.
MSTAT\_HESS | l | lmf, lmmc, tbe | N | T | T: Read hessian matrix from file, if it exists<br>F: assume initial hessian is the unit matrix
MSTAT\_XTOL | r | lmf, lmmc, tbe | Y | 1e-3 | Convergence criterion for change in atomic displacements<br>>0: criterion satisfied when xtol > net shift (shifts summed over all sites)<br><0: criterion satisfied when xtol > max shift of any site<br>0: DO not use this criterion to check convergence<br><br>Note: When molecular statics are performed, either GTOL or XTOL must be specified. Both may be specified
MSTAT\_GTOL | r | lmf,lmmc,tbe | Y | 0 | Convergence criterion for tolerance in forces.<br>>0: criterion satisfied when gtol > "net" force (forces summed over all sites)<br><0: criterion satisfied when xtol > max absolute force at any site<br>0: Do not use this criterion to check convergence<br><br>Note: When molecular statics are performed, either GTOL or XTOL must be specified. Both may be specified
MSTAT\_STEP | r | lmf, lmmc, tbe | Y | 0.015 | Initial (and maximum) step length
MSTAT\_NKILL | i | lmf, lmmc, tbe | Y | 0 | Remove hessian after NKILL iterations.<br>Never remove Hessian if NKILL=0
MSTAT\_PDEF= | r | lmf, lmmc, tbe | Y | 0 0 0 ... | Lattice deformation modes (not documented)
MD[...] | | lmmc, tbe | Y | | Parameters for molecular dynamics
MD\_MODE | i | lmmc | N | 0 | 0: no MD<br>1: NVE<br>2: NVT<br>3: NPT
MD\_TSTEP | r | lmmc | Y | 20.671 | Time step (a.u.)<br>NB: 1 fs = 20.67098 a.u.
MD\_TEMP | r | lmmc | Y | 0.00189999 | Temperature (a.u.)<br>NB: 1 deg K = 6.3333e-6 a.u.
MD\_TAUP | r | lmmc | Y | 206.71 | Thermostat relaxation time (a.u.)
MD\_TIME | r | lmmc | N | 20671000 | Total MD time (a.u.)
MD\_TAUB | r | lmmc | Y | 2067.1 | Barostat relaxation time (a.u.)

{::nomarkdown}</div>{:/}

##### _EWALD_
Category EWALD holds information controlling the Ewald sums for structure consstants entering into, e.g. the Madelung summations and Block summed structure constants(**lmf**{: style="color: blue"}). Most programs use quantities in this category to carry out Ewald sums (exceptions are **lmstr**{: style="color: blue"} and the molecules codes).

<div onclick="elm = document.getElementById('ewaldtable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="ewaldtable">{:/}

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
AS | r | | Y | 2 | Controls the relative number of lattice vectors in the real and reciprocal space
TOL | r | | Y | 1e-8 | Tolerance in the Ewald sums
NKDMX | i | | Y | 800 | The maximum number of real-space lattice vectors entering into the Ewald sum, used for memory allocation.<br>Normally you should not need this token. Increase NKDMX if you encounter an error message like this one: xlgen: too many vectors, n=...
RPAD | r | | Y | 0 | Scale rcutoff by RPAD when lattice vectors padded in oblong geometries

{::nomarkdown}</div>{:/}

##### _HAM_
This category contains parameters defining the one-particle hamiltonian.  

Pportions of HAM are read by these codes: 

~~~
lm
lmfa
lmfgwd
lmfgws
lmf
lmmc
lmgf
lmdos
lmchk
lmscell
lmstr
lmctl
lmpg
tbe
lmmag
~~~

<div onclick="elm = document.getElementById('hamtable'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="hamtable">{:/}

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
NSPIN | i | ALL | Y | 1 | 1 for non-spin-polarized calculations<br>2 for spin-polarized calculations
REL	| i | ALL | Y | 1 | 0: for nonrelativistic Schrodinger equation<br>1: for scalar relativistic approximation to the Dirac equation<br>2: for Dirac equation<br><br>NB: For the magnetic parameters below to be active, HAM_NSPIN must be 2
SO | i | ALL | Y | 0 | 0: no SO coupling<br>1: Add L·S to hamiltonian<br>2: Add Lz·Sz only to hamiltonian<br>3: Like 2, but also add L·S−LzSz in an approximate manner that preserves indpendence in the spin channels.<br>See [here](http://titus.phy.qub.ac.uk/packages/LMTO/v7.11/doc/spin-orbit-coupling.html) for analysis and description of the different approximations
NONCOL | l | ASA | Y | F | F: collinear magnetism<br>T: non-collinear magnetism
SS | 4 r | ASA | Y | 0 | Magnetic spin spiral, direction vector and angle.<br><br>Example: nc/test/test.nc 1
BFIELD | i | lm, lmf | Y | 0 | 0: no external magnetic field applied.<br>1: add site-dependent constant external Zeeman field (requires NONCOL=T).<br>Fields are read from file bfield.ext.<br>2: add Bz·Sz only to hamiltonian.<br><br>Examples:<br>fp/test/test.fp gdn<br>nc/test/test.nc 5
BXCSCAL | i | lm, lmgf | Y | 0 | This tag provides an alternative means to add an effective external magnetic field in the LDA.<br>0: no special scaling of the exchange-correlation field.<br>1: scale the magnetic part of the LDA XC field by a site-dependent factor 1 + sbxci as described below.<br>2: scale the magnetic part of the LDA XC field by a site-dependent factor $(1 + sbxc_i^2)^{1/2}$ as described below.<br>This is a special mode used to impose contraining fields on rotations, used, e.g. by the CPA code.<br>Site-dependent scalings sbxci are read from file bxc.ext.
XCFUN | i | ALL LDA | Y | 2 | Specifies local exchange correlation functional:<br>1: Ceperly-Alder<br>2: Barth-Hedin (ASW fit)<br>3: PW91 (same as PBE)<br>4: PBE (Same as PW91)
GGA | i | ALL LDA | Y | 0 | Specifies a GGA functional:{::nomarkdown}<ol><li>No GGA (LDA only)</li><li>Langreth-Mehl</li><li>PW91</li><li>PBE</li><li>PBE with Becke exchange<br>Example: fp/test/test.fp te</li></ol>{:/}
PWMODE | i | lmf, lmfgwd | Y | | Controls how APWs are added to the LMTO basis.{::nomarkdown}<br><br>1s digit:<ol><li>LMTO basis only</li><li>Mixed LMTO+PW<br>Examples: <br>fp/test/test.fp srtio3 and fp/test/test.fp felz 4</li><li>PW basis only</li></ol>10s digit:<ol><li>PW basis fixed (less accurate, but simpler)</li><li>PW basis k-dependent. Symmetry-consistent but basis and basis dimension depend on k.<br>Example: fp/test/test.fp te</li></ol>{:/}
PWEMIN | r | lmf, lmfgwd | Y | 0 | Include APWs with energy E > PWEMIN (Ry)
PWEMAX | r | lmf, lmfgwd | Y | -1 | Include APWs with energy E < PWEMAX (Ry)
NPWPAD | i | lmf, lmfgwd | Y | -1 | Overrides default padding of variable basis dimension
RDSIG | i | lmf, lmfgwd, lm, lmgf | Y | 0 | Controls how the self-energy Σ is added to local exchange correlation functional.<br>1s digit:{::nomarkdown}<ul><li>0 do not read Σ</li><li>1 read file Σ in sigm.ext, if it exists, and add it to the LDA potential<br>Add 2 to symmetrize Σ(T)<br>Add 4 to include Re(Σ(T)) only</li></ul>10s digit:<ul><li>0 simple interpolation</li><li>1 approximate high energy parts of sigma by diagonal (see sigp)</li><li>3 interpolate sigma by LDA eigenvectors (no longer supported)<br>In this mode use 100s digit for # interpolation points.<br>Add 10000 to indicate the sigma file was stored in the full BZ (no symmetry operations are assumed)<br>Add 20000 to use the minimum neighbor table (only one translation vector at the surfaces or edges; cannot be used with symmetrization)<br>Add 40000 to allow file mismatch between expected k-points and file values.</li></ul>{:/}<br>Note: Some options can also be supplied through the command-line argument --rsig[~option~option...].
RSSTOL | r | ALL | Y | 5e-6 | Max tolerance in Bloch sum error for real-space Σ.<br><br>Unless otherwise specified (by e.g. --rsig~rs), Σ is read in k-space and is immediately converted to real space by inverse Bloch transform.<br>The r.s.form is forward Bloch summed and checked against the original k-space Σ.<br>If the difference exceeds RSSTOL the program will abort.<br>The conversion should be exact to machine precision unless the range of Σ is truncated.<br>You can control the range of real-space Σ with RSRNGE below.
RSRNGE | r | ALL | Y | 5 | Maximum range of connecting vectors for real-space Σ (units of ALAT)
NMTO | i | ASA | Y | 0 | Order of polynomial approximation for NMTO hamiltonian
KMTO | r | ASA | Y | | Corresponding NMTO kinetic energies.<br>Read NMTO values, or skip if NMTO=0
EWALD | l | lm | Y | F | Make strux by Ewald summation (NMTO only)
VMTZ | r | ASA | Y | 0 | Muffin-tin zero defining wave functions
QASA | i | ASA | Y | 3 | A parameter specifying the definition of ASA moments Q0,Q1,Q2; see lmto documentation{::nomarkdown}<ul><li>0. Methfessel conventions for 2nd gen ASA moments Q0,Q1,Q2</li><li>1. Q2 = coefficient to φ̇2 − p φ2 in sphere.</li><li>2. Q1,Q2 accumulated as coefficients to <φ·φ̇> and <φ̇2>, respectively.</li><li>3. 1+2 (Stuttgart conventions)</li></ul>{:/}
PMIN | r | ALL | Y | 0 0 0 ... | Global minimum in fractional part of logarithmic derivative parameters Pl.<br>Enter values for l=0,..lmx<br>0: no minimum constraint<br>\# : with \#<1, floor of fractional P is \#<br>1: use free-electron value as minimum<br><br>Note: lmf always uses a minimum constraint, the free-electron value (or slightly higher if AUTOBAS_GW is set).<br>You can set the floor still higher with PMIN=#.
PMAX | r | ALL | Y | 0 0 0 ... | Global maximum in fractional part of potential functions Pl. Enter values for l=0,..lmx<br>0 : no maximum constraint<br>\#: with \#<1, uppper bound of of fractional P is \#
OVEPS | r | ALL | Y | 0 | The overlap is diagonalized and the hilbert space is contracted, discarding the part with eigenvalues of overlap < OVEPS<br>Especially useful with the PMT basis, where the combination of smooth Hankel functions and APWs has a tendency to make the basis overcomplete.
OVNCUT | i | ALL | Y | 0 | This tag has a similar objective to OVEPS.<br>The overlap is diagonalized and the hilbert space is contracted, discarding the part belonging to lowest OVNCUT evals of overlap.<br>Supersedes OVEPS, if present.
GMAX | r | lmf, lmfgwd | N | | G-vector cutoff used to create the mesh for the interstitial density. A uniform mesh is with spacing between points in the three directions as homogeneous as possible, with G vectors |G|<GMAX.<br>This input is required; but you may omit it if you supply information with the FTMESH token.
FTMESH | i1 [i2 i3] | FP | N | | The number of divisions specifying the uniform mesh density along the three reciprocal lattice vectors. The second and third arguments default to the value of the first one, if they are not specified. <br>This input is used only if if the parser failed to read the GMAX token.
TOL | r | FP | Y | 1e-6 | Specifies the precision to which the generalized LMTO envelope functions are expanded in a Fourier expansion of G vectors.
FRZWF | l | FP | Y | F | Set to T to freeze the shape of the augmented part of the wave functions. Normally their shape is updated as the potential changes, but with FRZWF=t the potential used to make augmentation wave functions is frozen at what is read from the restart file (or free-atom potential if starting from superposing free atoms).<br>This is not normally necessary, and freezing wave functions makes the basis slightly less accurate. However, there are slight inconsistencies when these orbitals are allowed to change shape. Notably the calculated forces to not take this shape change into account, and they will be slightly inconsistent with the total energy.
FORCES | i | FP | Y | 0 | Controls how forces are to be calculated, and how the second-order corrections are to be evaluated. Through the variational principle, the total energy is correct to second order in deviations from self-consistency, but forces are correct only to first order. To obtain forces to second order, it is necessary to know how the density would change with a (virtual) displacement of the core+nucleus, which requires a linear response treatment.<br>lmf estimates this changes in one of two ways:{::nomarkdown}<ol><li>the free-atom density is subtracted from the total density for nuclei centered at the original position and added back again at the (virtually) displaced position.<br><br>For this ansatz, use FORCES=1.</li>the core+nucleus is shifted and screened assuming a Lindhard dielectric response. <br><br>For this ansatz, use FORCES=12. You also must specify ELIND, below.</li></ol>{:/}
ELIND | r | lmf | Y | -1 | a parameter in the Lindhard response function, (the Fermi level for a free-electron gas relative to the bottom of the band). You can specify this energy directly, by using a positive number for the parameter. If you use instead a negative number, the program will choose a default value from the total number of valence electrons and assuming a free-electron gas, scale that default by the absolute value of the number you specify. If you have a simple sp bonded system, the default value is a good choice. If you have d or f electrons, it tends to overestimate the response.<br>Use a something smaller, e.g. ELIND=-0.7.<br><br>ELIND is used in three contexts:<br><br>(1) in the force correction term; see FORCES= above<br>(2) to estimate a self-consistent density from the input and output densities after a band pass<br>(3) to estimate a reasonable smooth density from a starting density after atoms are moved in a relaxation step.
SIGP[...] | r | lmf, lmfgwd | Y | | Parameters used to interpolate the self-energy Σ. Used in conjunction with the GW package. See gw.html for description. Default: not used
SIGP\_MODE | r | lmf, lmfgwd | Y | 4 | Specifies the linear function used for matrix elements of Σ at highly-lying energies. With recent implementations of the GW package 4 is recommended; it requires no input from you.
SIGP\_EMAX SIGP\_NMAX SIGP\_EMIN SIGP\_NMIN SIGP\_A SIGP\_B | r | lmf, lmfgwd | Y | | See gw.html
AUTOBAS[...] | r | lmfa, lmf, lmfgwd | Y | | Parameters associated with the automatic determination of the basis set.<br>These switches greatly simplify the creation of an input file for lmf.<br>Note: Programs lmfa and lmf both use tokens in the AUTOBAS tag but they mean different things, as described below. This is because lmfa generates the parameters while lmf uses them.<br><br>Default: not used
AUTOBAS\_GW | i | lmfa | Y | 0 | Set to 1 to tailor the autogenerated basis set file basp0.ext to a somewhat larger basis, better suited for GW.
AUTOBAS\_GW | i | lmf | Y | 0 | Set to 1 to float log derivatives P a bit more conservatively — better suited to GW calculations.
AUTOBAS\_LMTO | i | lmfa | Y | 0 | lmfa autogenerates a trial basis set, saving the result into basp0.ext.<br>LMTO is used in an algorithm to determine how large a basis it should construct: the number of orbitals increases as you increase LMTO. This algorithm also depends on which states in the free atom which carry charge.<br>Let lq be the highest l which carries charge in the free atom.<br><br>There are the following choices for LMTO:{::nomarkdown}<ul><li>0. standard minimal basis; same as LMTO=3.</li><li>1. The hyperminimal basis, which consists of envelope functions corresponding those l which carry charge in the free atom, e.g. Ga sp and Mo sd.<br><br>Note: this basis is only sensible when used in conjunction with APWs.</li><li>2. All l up to lq+1 if lq<2; otherwise all l up to lq</li><li>3. All l up to min(lq+1, 3).<br>For elements lighter than Kr, restrict l≤2.<br>For elements heavier than Kr, include l to 3.</li><li>4. (Standard basis) Same as LMTO=3, but restrict l≤2 for elements lighter than Ar.</li><li>5. (Large basis) All l up to max(lq+1,3) except for H, He, Li, B (use l=spd).</li></ul>{:/}<br>Use the MTO token (see below) in combination with this one. MTO controls whether the LMTO basis is 1-κ or 2-κ, meaning whether 1 or 2 envelope functions are allowed per l channel.
AUTOBAS\_MTO | i | lmfa | Y | 0 | Autogenerate parameters that control which LMTO basis functions are to be included, and their shape.<br><br>Tokens RSMH,EH (and possibly RSMH2,EH2) determine the shape of the MTO basis. lmfa will determine a reasonable set of RSMH,EH automatically (and RSMH2,EH2 for a 2-κ basis), fitting to radial wave functions of the free atom.<br><br>Note: lmfa can generate parameters and write them to file basp0.ext.<br>lmf can read parameters from basp.ext.<br>You must manually create basp.ext, e.g. by copying basp0.ext into basp.ext. You can tailor basp.ext with a text editor. There are the following choices for MTO:<br> 0: do not autogenerate basis parameters<br>1: or 3 1-κ parameters with Z-dependent LMX<br>2: or 4 2-κ parameters with Z-dependent LMX
AUTOBAS\_MTO | i | lmf, lmfgwd | Y | 0 | Read parameters RSMH,EH,RSMH2,EH2 that control which LMTO basis functions enter the basis.<br><br>Once initial values have been generated you can tune these parameters automatically for the solid, using lmf with the --optbas switch; see Building_FP_input_file.html and FPoptbas.html.<br><br>The --optbas step is not essential, especially for large basis sets, but it is a way to improve on the basis without increasing the size.<br><br>There are the following choices for MTO:<br><br>0 Parameters not read from basp.ext; they are specified in the input file ctrl.ext.<br>1 or 3: 1-κ parameters may be read from the basis file basp.ext, if they exist<br>2 or 4: 2-κ parameters may be read from the basis file basp.ext, if they exist<br>1 or 2: Parameters read from ctrl.ext take precedence over basp.ext<br>3 or 4: Parameters read from basp.ext take precedence over those read from ctrl.ext.
AUTOBAS\_PNU | i | lmfa | Y | 0 | Autoset boundary condition for augmentation part of basis, through specification of logarithmic derivative parameters P.<br><br>0 do not make P <br>1 Find P for l < lmxb from free atom wave function; save in file basp0.ext
AUTOBAS\_PNU | i | lmf, lmfgwd | Y | 0 | Autoset boundary condition for augmentation part of basis, through specification of logarithmic derivative parameters P.<br><br>0 do not attempt to read P from basp.ext.<br>1 Read P from basp.ext, for those species which P is given.
AUTOBAS\_LOC | i | lmfa, lmf, lmfgwd | Y | 0 | Autoset local orbital parameters PZ, which determine which deep or high-lying states are to be included as local orbitals.<br><br>Used by lmfa to control whether parameters PZ are to be sought:<br>0: do not autogenerate PZ<br>1 or 2: autogenerate PZ<br><br>Used by lmf and lmfgwd to control how PZ is read:<br><br>1 or 2: read parameters PZ<br>1: Nonzero values from ctrl file take precedence over basis file input
AUTOBAS\_ELOC | r | lmfa | Y | -2 Ry | The first of two criteria to decide which orbitals should be included in the valence as local orbitals. If the energy of the free atom wave function exceeds (is more shallow than) ELOC, the orbital is included as a local orbital.
AUTOBAS\_QLOC | r | lmfa | Y | 0.005 | The second of two criteria to decide which  orbitals should be included in the valence as local orbitals.<br>If the fraction of the free atom wave function’s charge outside the augmentation radius exceeds QLOC, the orbital is included as a local orbital.
AUTOBAS\_PFLOAT | i1 i2 | lmf, lmfgwd | y | 1 1 | Governs how the logarithmic derivative parameters Pl are set and floated in the course of a self-consistency cycle.<br>The 1st argument controls default starting values of P and lower bounds to P when it is floated<br>0: Use version 6 defaults and float lower bound<br>1: Use defaults and float lower bound designed for LDA<br>2: Use defaults and float lower bound designed for GW<br>The 2nd argument controls how the band center of gravity (CG) is determined — used when floating P.<br>0: band CG is found by a traditional method<br>1: band CG is found from the true energy moment of the density

{::nomarkdown}</div>{:/}

##### _GF_

##### _GW_

##### _HEADER_
This category is optional, and merely prints to the standard output whatever text is in the category. For example:

~~~
HEADER  This line and the following one are printed to
        standard out on execution of a program.
XXX
~~~

Alternately:

~~~
HEADER [ In this form only two lines reside within the
        category delimiters,]
        and only two lines are printed.
~~~

##### _IO_

##### _ITER_

##### _OPTICS_

##### _OPTIONS_

##### _PGF_

##### _SITE_

##### _SPEC_
Category SPEC contains species-specific information. Because data must be read for each species, tokens are repeated (once for each species). For that reason, there is some restriction as to the order of tokens. Data for a specific species (Z=, R=, R/W=, LMX=, IDXDN= and the like described below) begins with a token ATOM=; input of tokens specific to that species must precede the next occurence of ATOM=.  

The following four species apply to the automatic sphere resizer:

<div onclick="elm = document.getElementById('spec1table'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="spec1table">{:/}

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
SCLWSR | r | ALL | Y | 0 | SCLWSR>0 turns on the automatic sphere resizer. It defaults to 0, which turns off the resizer.<br>The 10’s digit tells the resizer how to deal with resizing empty spheres; see lmto.html.
OMAX1 | r1 r2 r3 | ALL | Y | 0.16, 0.18, 0.2 | Constrains maximum allowed values of sphere overlaps.<br>You may input up to three numbers, which correspond to atom-atom, and atom-empty-sphere, and empty-sphere-empty-sphere overlaps respectively.
OMAX2 | r1 r2 r3 | ALL | Y | 0.4, 0.45, 0.5 | Constrains maximum allowed values of sphere overlaps defined differently from OMAX1; see lmto.html.<br>Both constraints are applied.
WSRMAX | r | ALL | Y | 0 | Imposes an upper limit to any one sphere radius

{::nomarkdown}</div>{:/}

The following tokens are input for each species. Data sandwiched between successive
occurences of ATOM apply to one species.

<div onclick="elm = document.getElementById('spec2table'); if(elm.style.display == 'none') elm.style.display = 'block'; else elm.style.display = 'none';"><button type="button" class="button tiny radius">Click to show table.</button></div>
{::nomarkdown}<div style="display:none;margin:0px 25px 0px 25px;"id="spec2table">{:/}

Token | Arguments | Program | Optional | Default | Explanation
- | - | - | - | - | -
ATOM | c | ALL | N | | A character string (8 characters or fewer) that labels this species. This label is used, e.g. by the SITE category to associate a species with an atom at a given site.<br>Species are split into classes; how and when this is done depends whether you are using an ASA or full-potential implementation.<br><br>ASA-specific:<br>The species ID also names a disk file with information about that atom (potential parameters, moments, potential and some sundry other information). More precisely, species are split into classes, the program differentiates class names by appending integers to the species label. The first class associated with the species has the species label; subsequent ones have integers appended.<br><br>Example: testing/test.ovlp 3

{::nomarkdown}</div>{:/}

##### _STR_

##### _START_

##### _STRUC_

##### _SYMGRP_

##### _VERS_
