---
tags:
  - gsasii
  - dev
  - symmetry
---

# Imposition of Symmetry Constraints

> Authored by Brian Toby, edited by Yuanpeng Zhang

> Tuesday, Nov 05, 2024

Symmetry determines how structures are refined in two ways. First, symmetry determines what unit cell constants can be varied;
second, atoms on special positions need to be constrained to stay on the point, plane or line required by non-translational symmetry.

The routine `GSASIIstrIO.cellVary` returns a list of variable names for the reciprocal cell tensor terms that can be varied for each of the Laue classes.
While tensor terms are variable, they are related by symmetry. For example, one has to constrain `a=b=c` in a cubic cell. The relation between the reciprocal
cell terms is recorded using `GSASIImapvars.StoreEquivalence`, but the variables are all included in the returned list. Note that for monoclinic cells,
the setting for a, b or c-axis unique must be checked to see which terms can be varied. Also note that there are two extra Laue classes,
as rhombohedral classes can be set in either hexagonal or rhombohedral cells. The added R in classes 3R and 3mR indicate a rhombohedral cell. 

Symmetry constraints on atom positions and other structural parameters are determined in `GSASIIstrIO.GetPhaseData`.
The `GSASIIspc.SytSym` routine will determine the site symmetry for any point's fractional coordinates based on the
space group and `GSASIIspc.GetCSxinel` translates that into restrictions on parameters that must be related or fixed.
The atom position parameters that must be related by symmetry have constraints created by a call to
`GSASIImapvars.StoreEquivalence` and those that cannot be refined are fixed using `GSASIImapvars.StoreHold`.
Note that only the change from initial positions are varied, i.e.,(dAx, dAy & dAz) not (Ax, Ay & Az) terms are varied. The new
positions are `Ax = Ax + dAx`, etc. So, offsets do not need to be considered. For example, in the case of a symmetry
operation `x, 1/2-x, z`, the symmetry constraint will be that `dAx` and `dAy` have opposite sign but equal magnitude.
No special care is needed to enforce the 1/2 cell offset as long as the equivalence that `p::dAx = -1 * p::dAy` is applied.

Likewise, `GSASIIspc.GetCSuinel` determines the symmetry constraints on anisotropic Atomic Displacement Parameters (ADPs) and again
`GSASIImapvars.StoreEquivalence` is used to enforce symmetry relations. Parameters fixed by symmetry are
not included in the generated variable list. For magnetic spins, the allowed variables and constrained terms are
determined by `GSASIIspc.GetCSpqinel`. For modulated structures, the allowed variables and constrained terms
are determined by `GSASIIspc.GetSSfxuinel`. Also, for Pawley fits, reflections that have required
overlaps (tetragonal, rhombohedral, hexagonal and cubic cells) have constraints on intensities determined
in the `GetPawleyConstr` routine, Also, the `MakeRBParms` routine determines constraints on the rigid body origin using
`GSASIIspc.SytSym` and `GSASIIspc.GetCSxinel` on the origin coordinates. That routine also 'fixes' the coordinates
for all atoms in the rigid body using `GSASIImapvars.StoreHold`, as the coordinates are generated from the rigid
body terms. Note, however, that the refinement mode for the rigid body orientation quaternion (A, V or AV) is
not currently determined from symmetry, but potentially could be. Guidelines on this are presented in the several slides down below,

![GSAS Rigid Body](/assets/images/gsasii_rb_1.png)

![GSAS Rigid Body](/assets/images/gsasii_rb_2.png)

![GSAS Rigid Body](/assets/images/gsasii_rb_3.png)
