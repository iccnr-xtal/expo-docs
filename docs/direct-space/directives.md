# Directives and restraints

This page describes the directives that control the direct-space procedures from the
input file. They are needed mainly to run the simulated annealing without interacting
with the graphical interface, or to set options that are not available in it.

`%sanneal` runs the simulated annealing from the input file. Adding `%automatic`, the
interaction with the graphical interface is skipped and the procedure runs with default
values:

```text
%automatic
%Structure paracetamol
%Job paracetamol
%Data
   Cell  7.100  9.380  11.708  90.0  97.42  90.0
   SpaceGroup p 21/n
   Pattern   paracetamol.xy
   Wavelength 1.54056
%fragment paracetamol.mol
%sanneal
```

Directives are written on the lines following `%sanneal`. For example, `niter` and
`nrun` increase the success rate for the very flexible molecule of verapamil
hydrochloride:

```text
%Structure verapamil
%Job Verapamil hydrochloride (C27H39N2O4.Cl)
%Data
   Cell  7.089  10.593  19.207  100.11  93.75  101.56
   SpaceGroup p -1
   Pattern    verapamil_hydrochloride.xy
   Wavelength 1.54056
%fragment verapamil_hydrochloride.mol
%sanneal
niter 2000
nrun 20
```

Directive names can be abbreviated to their first characters (at least 4), e.g.
`cost 2` instead of `cost_function 2`. The abbreviation must be the beginning of the
directive name: a longer or different word is not recognized.

## Directives of the procedure

| Directive | Description |
|-----------|-------------|
| `cost_function n` | cost function: 1 = R~wp~ (weighted profile, default), 2 = R on the integrated intensities, 3 = R on the structure factors |
| `export n` | number of CIF files (best solutions) written at the end, e.g. `export 5` with `nrun 100` saves only the best 5 solutions |
| `niter n` | number of moves at each temperature step (computed automatically by default). It is important for the success rate of the optimization |
| `nrun n` | number of runs (default 10) |
| `ncyc n` | number of cycles of the *H*BB-BC algorithm (computed automatically by default) |
| `resmax x` | maximum resolution used (default 2.0 Å) |
| `temper t` | starting temperature (computed automatically by default) |
| `tfactor x` | temperature reduction factor, between 0 and 1 (default 0.90) |
| `seed n` | seed of the random number generator |
| `rand [n]` | randomize the internal and external degrees of freedom and the atomic parameters (if refined) of the starting model; *n* is an optional seed |
| `contacts [mode]` | print in the output file the intermolecular contacts of each final model, to check for atoms too close to each other. The contacts are listed as clashes, short contacts and contacts according to the overlap (sum of the van der Waals radii − distance: ≥ 0.4 Å, 0.2–0.4 Å, < 0.2 Å), with distance and overlap, followed by a summary table with the number of contacts of each model. *mode* is `noh` (default: all atoms except hydrogen), `all` (all atoms) or `none` (no analysis). With `noh` the hydrogen atoms are still used to recognize hydrogen bonds, which are not listed as contacts |

## Directives for the model

The following directives, also written after `%sanneal`, act on the model and its
degrees of freedom (DOF). Atoms are referred to by their labels; `*` selects all atoms
and `C*` all the atoms of an element (here carbon).

`centre_of_rotation atc [atf]`
:   origin of the rotation of a fragment. *atc* is the atom chosen as centre of
    rotation; the rotation is applied to the fragment containing *atc*. When *atc* is
    not part of the fragment to rotate, give an atom *atf* of that fragment. By default
    the centre of rotation is the central atom for polyhedra and the centre of mass for
    the other objects; `@com` restores the centre of mass for a polyhedron. The origin of
    rotation of each fragment is reported in the output file (*List of fragments*).
    Examples: `centre_of_rotation P1` (the molecule P(C~6~H~5~)~3~ containing P1 rotates
    around P1); `centre_of_rotation Ni1 P1` (it rotates around Ni1);
    `centre_of_rotation @com Si1` (the polyhedron containing Si1 rotates around its centre
    of mass).

`doc [atom1 atom2 …]`
:   activate the dynamical occupancy correction (DOC), for all atoms or only for the
    listed ones (e.g. `doc Ni1`).

`nodoc atom1 atom2 …`
:   exclude the listed atoms from the DOC; e.g. `doc` followed by
    `nodoc C* N* O10` applies the DOC to all atoms except C, N and O10 (see
    [Example 4](examples.md#example-4-alpo-m)).

`extdof atom [translation rotation]`
:   fix (0) or refine (1, default) the translation and rotation of the fragment
    containing *atom*. Example: `extdof Si1 0 0` fixes the position and orientation of
    the fragment containing Si1.

`intdof atom code` or `intdof atom1 atom2 [code]`
:   fix (0) or refine (1, default) internal DOFs: with one atom, all the torsions of the
    fragment containing it; with two atoms, the torsion around the bond *atom1*–*atom2*.
    Useful when some torsions are known, e.g. for double bonds. Examples:
    `intdof C1 C2 0` fixes the rotation around C1–C2; `intdof C1 0` fixes all the
    torsions of the fragment containing C1. Together with `extdof Si1 0 0`,
    `intdof Si1 0` fixes a whole framework of silicon atoms.

`intdof_disp atom1 atom2 atom3 atom4 disp [mode]`
:   limit the sampling of the torsion *atom1*–*atom2*–*atom3*–*atom4* to
    $\theta_0 - disp \le \theta \le \theta_0 + disp$, where $\theta_0$ is its starting
    value. *mode* = 2 marks a bimodal and *mode* = 3 a trimodal torsion (default 1):
    -   bimodal, planar torsion (starting value within 30° of 0° or 180°): the ranges
        $\theta_0 \pm disp$ and $\theta_0 + 180° \pm disp$ are sampled. With $\theta_0 = 0°$
        and *disp* = 30°: −30° to 30° and 150° to 210°;
    -   bimodal, non-planar torsion: $\theta_0 \pm disp$ and $-\theta_0 \pm disp$. With
        $\theta_0 = 60°$ and *disp* = 30°: 30° to 90° and −90° to −30°;
    -   trimodal torsion: the range around $\theta_0$ and two more ranges at +120° and
        −120°.

    Example: `intdof_disp C1 C2 C3 C4 20 2` (bimodal planar torsion; its starting value
    must be close to 0° or 180°).

`move atom @(x y z)`
:   move the fragment containing *atom* so that *atom* is at the fractional coordinates
    (*x*, *y*, *z*); useful when the position of an atom is known, e.g. a heavy atom
    located by Direct Methods. Example:

    ```text
    move Ni1 @(0.5 0.286 0.75)
    extdof Ni1 0 1
    centre_of_rotation Ni1
    ```

    moves the fragment containing Ni1 and, with `extdof` and `centre_of_rotation`, lets
    it rotate around Ni1 with fixed translation.

`optimadp overall | species | molecules | atoms at1 at2 … | groups at1 at2 …`
:   optimize the isotropic atomic displacement parameters (ADP). The ADPs of hydrogen
    atoms are constrained to 1.2 times that of the atom they are bonded to. `overall`:
    one overall ADP; `species`: one ADP per element; `molecules`: one ADP per fragment;
    `atoms`: independent ADPs for the listed atoms (`*` and `C*` allowed, e.g.
    `optimadp atoms *`); `groups`: the listed atoms share the same ADP. `refinetf` is a
    synonym of `optimadp`.

`po h k l`
:   activate the March–Dollase preferred orientation correction along the direction
    [*h k l*].

`rotate_around_atom ax1 ax2 at1 at2 … [theta]`
:   rotate the atoms *at1*, *at2*, … rigidly around the axis defined by the atoms *ax1*
    and *ax2*; the optional *theta* limits the rotation to −*theta*…+*theta* degrees.
    The rotation is added to the internal DOFs in the graphical interface. It can be
    abbreviated as `rotate`. Example: `rotate C1 C4 C5 C6 C7 C8 C9 C10` rotates the
    ring C5–C10 around the axis C1–C4.

    ![Atom numbering for the rotate example](../images/direct-space/rotate.png){ width="260" }

`shift_atom [atom1 atom2 …] val`
:   refine the atomic positions, applying shifts up to *val* (default 0.5) with respect
    to the centre of mass of the fragment; give the atoms to refine only some of them.

## Restraints

When the information contained in the pattern is limited (severe overlap, broad peaks,
preferred orientation, weak scatterers) and/or the number of degrees of freedom is large,
the correct structure may not correspond to the minimum of the cost function. Restraints
on distances and angles, anti-bumping and bond valence restraints add chemical
information and favour chemically plausible models (Falcioni & Deem, 1999;
Favre-Nicolin & Černý, 2002).

Restraints are relatively time-consuming, because all the symmetry-equivalent atoms must
be considered: use them only when the data are not good enough to avoid unreasonable
solutions.

### Anti-bumping restraints

If the solution contains atoms colliding with each other, anti-bumping restraints
prevent groups of atoms from overlapping with other parts of the structure. They act as
distance restraints applied only when the distance becomes shorter than a threshold.
Their contribution to the cost function is

$$
CF_{bump} = \sum_{ij}^{n} w_{ij} \left( d_{ij}^{min} - d_{ij}^{model} \right)^{2k} ,
\qquad d_{ij}^{model} < d_{ij}^{min}
$$

where $d_{ij}^{min}$ and $d_{ij}^{model}$ are the minimum ideal distance and the model
distance between atoms *i* and *j*, the sum is over *n* contacts and *k* = 2
(Hendrickson, 1985). By default $d_{ij}^{min}$ is the sum of the van der Waals radii
(Feng *et al.*, 2010) multiplied by a scale factor ε (default 0.7):

$$
d_{ij}^{min} = \epsilon \left( R_i^{vdW} + R_j^{vdW} \right)
$$

Increase ε (values between 0.6 and 1.0 are recommended) if overlapping atoms are still
found; use small values (0.3–0.6) when the restraints act between isolated atoms of the
starting model that are expected to be bonded in the final structure. The weight
$w_{ij}$ is 1.0 by default.

Pairs of atoms connected by a rigid sequence of bonds (e.g. two atoms in a ring) are
excluded, as well as atoms separated by a path of bonds containing at most 4 rotatable
bonds. The output file lists all the restraints applied, with $d_{ij}^{min}$ and
$d_{ij}^{model}$.

`bump [atoms1 atoms2 [dist wei]]`
:   generate anti-bumping restraints. Without parameters they are generated among all C,
    N, O and S atoms. *atoms1* and *atoms2* restrict them to specific atoms or groups:
    atom labels, `S*` for all atoms of an element, `*` for all atoms, `frag1`, `frag2`, …
    for whole fragments. *dist* is the minimum distance and *wei* the weight; negative
    values select the defaults. Several `bump` directives can be combined:

    | Example | Effect |
    |---------|--------|
    | `bump` | restraints among all C, N, O, S atoms |
    | `bump C* S*` | only between C and S atoms |
    | `bump C1 S1 3.5 10.` | only between C1 and S1, minimum distance 3.5 Å, weight 10 |
    | `bump C* S1 -1 10.` | between all C atoms and S1, default distance, weight 10 |
    | `bump C1 *` | between C1 and all the other atoms |
    | `bump frag1 frag2 -1 10.` | between the atoms of fragments 1 and 2 |

`nobump atoms1 atoms2`
:   remove restraints set by `bump`. For example, `bump * *` followed by `nobump Ag1 N1`
    and `nobump Ag1 N2` restrains all atoms except the pairs Ag1–N1 and Ag1–N2;
    `bump` followed by `nobump O5 *` restrains all C, N, O, S atoms except O5.

`bweight w`
:   weight of all the anti-bumping restraints (default 1.0).

`bscale s`
:   scale factor ε applied to the sum of the van der Waals radii (default 0.7). Larger
    values increase the minimum distances and the number of contacts contributing to the
    cost function.

### Distance and angle restraints

`rest atom1 atom2 [target_dist tol weight inter]` or `rest atom1 atom2 atom3 [target_angle tol weight inter]`
:   restrain the distance between two atoms, or the angle defined by three atoms.
    *target_dist* (Å) is taken from an internal table of distances when omitted;
    *target_angle* is in degrees. *tol* is the tolerance (default 0.2 Å or 2.0°) and
    *weight* the weight (default 100). By default:

    -   if the atoms belong to the same molecule, symmetry-equivalent atoms are not
        considered and the restraint is intramolecular. This is the common use with
        direct-space methods, e.g. to force the closure of a flexible ring;
    -   if they belong to different molecules, all the intermolecular and intramolecular
        bonds *atom1*–*atom2* in the cell are considered. The keyword `inter` restricts
        the restraint to intermolecular bonds, e.g. to impose the linearity of a chain in
        a polymer.

    Examples: `rest C11 C16 1.54`, `rest C2 C1 C6 110`.

### Bond valence restraints

Bond valence sums can be added to the cost function as

$$
CF_{VB} = \sum_{n}^{N_R} w_n \left( V_n - V_n^0 \right)^2
$$

where the sum is over the $N_R$ bond valence restraints, $w_n$ is the weight of each
restraint and $V_n$ is the bond valence sum of an atom with expected formal charge
$V_n^0$.

`bvres atom valence [sigma wei]`
:   bond valence restraint for *atom*, with expected oxidation state *valence* (without
    sign). *sigma* is the allowed deviation (default 0.3) and *wei* the weight (default
    1). `*` applies the restraint to all atoms of a type: `bvres Ga* 3` is equivalent to
    `bvres Ga1 3`, `bvres Ga2 3`, … for all gallium atoms.

`bvpar El1 El2 R0 B [rmin rmax]`
:   for each pair of elements, EXPO takes the bond valence parameters $R_0$ and $B$ from
    a database and assigns a *bond window*, the range of distances (*rmin*–*rmax*)
    considered as bonds; they are listed in the output file. Use this directive only to
    change the default values.

## The `%fragment` command

`%fragment` followed by a file name imports a starting model from an external file. The
file extension is mandatory, as it identifies the format (see
[Preparing the input file](index.md#preparing-the-input-file)). `%fragment` can be
repeated to import more fragments.

`%fragment` can also generate predefined fragments, with the form
`%fragment keyword option1 [option2] [option3]`.

### Polyhedra

```text
%fragment tetra|octa|square|cube|trigonal|prism_tetra|prism_trig|icosa AtC AtV [dist]
```

generates a tetrahedron, octahedron, square planar group, cube, trigonal planar group,
tetragonal prism, trigonal prism or icosahedron, with the element *AtC* at the centre and
*AtV* at the vertices. *dist* is the *AtC*–*AtV* distance; if omitted, it is taken from
an internal table of average distances. For example, `%fragment tetra Si O` imports an
SiO~4~ tetrahedron with the default Si–O distance (1.645 Å), and
`%fragment tetra Si O 1.597` sets the distance to 1.597 Å.

### Isolated atoms

```text
%fragment atoms chemical_formula
```

imports isolated atoms at random positions in the asymmetric unit; the formula can be
written e.g. as `[C3H5(OH)3]4` or `C 28 O 8 S 4 H 24`. For example,
`%fragment atoms Pb` imports one Pb atom, and `%fragment atoms (Sb2O3)2` imports 4 Sb and
6 O atoms.

### SMILES

```text
%fragment smiles SMILES_string
```

builds the molecule from a SMILES string. For paracetamol:

```text
%Structure paracetamol
%Job Paracetamol (C8H9NO2)
%Data
   Cell 7.100 9.380 11.708 90.0 97.42 90.0
   SpaceGroup p 21/n
   Pattern paracetamol.xy
   Wavelength 1.54056
%fragment smiles CC(=O)NC1=CC=C(C=C1)O
%sanneal
```

### Examples with more fragments

**Creatine monohydrate.** The creatine molecule is imported from a CIF file and the water
molecule from an SDF file. The cell and space group in `creatine.cif` are ignored: the
molecule is placed in the cell and space group given in `%data`.

```text
%Structure creatinem
%Job Creatine monohydrate (C4H9N3O2.H2O)
%Data
   Cell  12.506  5.046  12.169  90  108.88  90
   SpaceGroup P 21/c
   Pattern    creatinem.xy
   Wavelength 1.54056
%fragment creatine.cif
%fragment H2O.sdf
%sanneal
```

**S-Ibuprofen with two molecules in the asymmetric unit (*Z*′ = 2).** The same SDF file,
downloaded from PubChem, is imported twice.

```text
%Structure S-Ibuprofen
%Job Structure solution of S-Ibuprofen (C13 H18 O2)
%Data
   Cell  12.463  8.029  13.538  90  112.93  90
   SpaceGroup p 21
   Pattern    pd_0034.pow
   Wavelength 1.54056
%fragment Structure3D_CID_3672.sdf
%fragment Structure3D_CID_3672.sdf
%sanneal
```

**Lead(II) sulfate, PbSO~4~.** An SO~4~ tetrahedron and an isolated Pb atom are
imported.

```text
%structure pbso4
%job Lead(II) sulfate (PbSO4)
%data
pattern pbso4.dat
wavelength 1.54056
cell 6.95802 8.48024 5.39754 90 90 90
spacegroup p b n m
%fragment tetra S O
%fragment atoms Pb
%sanneal
```

### Directives of `%fragment`

`deletehydro`
:   delete the hydrogen atoms of the fragment.

`cut at1 at2`
:   delete the bond between *at1* and *at2*, e.g. to open a ring and make the molecule
    more flexible.

```text
%fragment my_molecule.mol
deletehydro
cut C1 C2
```

## References

- Falcioni, M. & Deem, M. W. (1999). *J. Chem. Phys.* **110**, 1754–1766.
  [doi:10.1063/1.477812](https://doi.org/10.1063/1.477812)
- Favre-Nicolin, V. & Černý, R. (2002). *J. Appl. Cryst.* **35**, 734–743.
  [doi:10.1107/S0021889802015236](https://doi.org/10.1107/S0021889802015236)
- Feng, Z. J., Jia, R. R., Dong, C., Cao, S. X. & Zhang, J. C. (2010).
  *J. Appl. Cryst.* **43**, 179–180.
  [doi:10.1107/S0021889809049875](https://doi.org/10.1107/S0021889809049875)
- Hendrickson, W. A. (1985). *Methods Enzymol.* **115**, 252–270.
  [doi:10.1016/0076-6879(85)15021-4](https://doi.org/10.1016/0076-6879%2885%2915021-4)
