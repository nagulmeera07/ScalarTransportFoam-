# scalarTransportFoam — passive scalar transport tutorial

This repository introduces transport equations for a passive scalar, with a hands‑on tutorial based on OpenFOAM's `scalarTransportFoam` solver. The goal is to understand how convection and diffusion act on a passive scalar, how to set up a simple 1D case (adapted from the `shockTube` case), and how to run a small parameter study by changing velocity and diffusivity.

---

## Contents

- Overview and motivation
- Governing equation (what `scalarTransportFoam` solves)
- What "passive scalar" means
- Case setup (which files to change and why)
- Initial and boundary conditions used in the tutorial
- Parameter study (pure diffusion, pure convection, combined)
- Running the case (commands)
- Expected results and visualization tips
- Practical notes and numerical suggestions
- References

---

## Overview

Transport of a passive scalar (e.g., temperature in an isothermal flow, or concentration of a non-reacting species) is a fundamental process in CFD. It is governed by time evolution, convective transport by a velocity field, and diffusion driven by gradients in the scalar field. Understanding this simple equation helps with intuition for more complex systems (for example, the role of viscosity in Navier–Stokes is analogous to diffusivity for momentum).

This repo demonstrates:
- How to set up a 1D case where a central box of scalar(Temperature) = 1 is embedded in a domain of scalar = 0. 
- How to adapt an existing `shockTube` case instead of creating all dictionaries from scratch.
- A parameter study by changing velocity and diffusivity to observe:
  - Pure diffusion (U = 0)
  - Pure convection (diffusivity Γ = 0)
  - Combined convection and diffusion (both nonzero)

---

## Governing equation

The scalar transport equation solved by `scalarTransportFoam` can be written as:

$$
\frac{\partial \phi}{\partial t} + \nabla\cdot(\mathbf{U}\,\phi) - \nabla\cdot(\Gamma\nabla\phi) = 0
$$

where:
- φ is the passive scalar,
- U is the velocity field,
- Γ is the scalar diffusivity (assumed constant in this tutorial).

Interpretation:
- The first term is the time derivative (transient behaviour).
- The second term is convection (transport of φ by the flow).
- The third term is diffusion (spreading/smoothing of φ by gradients and diffusivity).

---

## What is a passive scalar?

A passive scalar is a quantity that is advected and diffused by the flow but does not affect the velocity field (no coupling back to momentum or energy). Examples: dye concentration, a non-reacting tracer, or temperature in an isothermal flow.

In this case passive scalar is Temperature

---

## Case setup — files to modify

We base the tutorial on an existing `shockTube` case. Key files to edit are:

- `0/T` (or whichever scalar field file you use in place of `φ`)  
  - Defines initial scalar field φ (the central box set to 1, rest set to 0). Geometry is from -5, 5 then region of -0.5 to 0.5 set to Temperature value 1.
- `0/U`  
  - Defines the velocity field. Set to zero for pure diffusion, set to a constant nonzero value for convection tests.
- `constant/transportProperties`  
  - Set the scalar diffusivity (e.g., `DT` or `Gamma`). Use `0` (or very small) to approximate pure convection and a positive constant for diffusion tests.
- `system/controlDict`  
  - Control time-stepping, start/end time, write intervals, and solver to run (`application     scalarTransportFoam;`).
- `system/fvSchemes`  in this endtime is 5 seconds and write interval is 1.
  - Choose spatial discretization schemes for grad/div/laplacian. Convection-dominated cases often need upwind or total variation diminishing schemes; diffusion terms use central schemes. In this fvschemes, fvsolution and controldict files are copied from the pitzdaily case.
- `system/fvSolution`  
  - Solver controls and tolerances for the scalar linear solver.

Paths summary (typical structure):
- `caseFolder/0/T`
- `caseFolder/0/U`
- `caseFolder/constant/transportProperties`
- `caseFolder/system/controlDict`
- `caseFolder/system/fvSchemes`
- `caseFolder/system/fvSolution`

---

## Initial mesh and field setup

- Mesh: a 1D mesh is sufficient for the tutorial. You can keep the same mesh structure as `shockTube` (1 cell in two dimensions, many cells along the x-direction).
- Initial scalar field T:
  - Central box (region in the middle of the domain): T = 1 ( central part 1 i.e -0.5 1 1 to 0.5 1 1)
  - Rest of domain: T = 0
- Initial velocity: typically zero for pure diffusion; for convection or combined cases set U = (U_x,0,0) with constant U_x (choose the sign for left→right or right→left transport).

Example logic for `0/T` (pseudocode):
- set T = 1 for x in [x1, x2]
- set T = 0 elsewhere

---

## Parameter study (recommended tests)

1. Pure diffusion
   - U = 0 (zero velocity in `0/U`)
   - DT (diffusivity) = positive constant (e.g., 1e-4, 1e-3 — tune to observe diffusion rate) in this case 1e-6 to 1 ( 2 step)
   - Expectation: initial box smooths and spreads symmetrically.

2. Pure convection
   - DT = 0 (or extremely small) in `constant/transportProperties`
   - U = constant nonzero (e.g., U_x = 1.0) in this case 1 0.5 2 tested.
   - Expectation: the shape is transported downstream with minimal change in amplitude/shape (numerical diffusion may appear; upwind schemes will smooth more than central ones).

3. Combined convection + diffusion
   - Both U and DT nonzero (choose combinations to explore dominance)
   - Expectation: scalar is transported and also spreads; sharper fronts when convection dominates, smoother when diffusion dominates.

### Parameter study

| Case No | **u** [m/s] | **D<sub>T</sub>** [m²/s] | **Transport equation** |
|----|-------------|-------------------------|------------------------|
| scalarTransportFoam_1 | (0 0 0)     | 1e-6                    |  ∂T/∂t = D<sub>T</sub> ∆T  |
| scalarTransportFoam_2 | (0 0 0)     | 1e-4                    | ∂T/∂t = D<sub>T</sub> ∆T |
| scalarTransportFoam_3  | (0 0 0)     | 1e-2                    | ∂T/∂t = D<sub>T</sub> ∆T  |
| scalarTransportFoam_4 | (0 0 0)     | 1e0                     |∂T/∂t = D<sub>T</sub> ∆T |
| scalarTransportFoam_5| (1 0 0)     | 0                       |  ∂T/∂t + ∇·(uT) = 0     |
| scalarTransportFoam_6  | (0.5 0 0)   | 0                       | ∂T/∂t + ∇·(uT) = 0      |
| scalarTransportFoam_7| (2 0 0)     | 0                       |  ∂T/∂t + ∇·(uT) = 0   |
| scalarTransportFoam_8 | (1 0 0)     | 1e-6                    |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆  |
| scalarTransportFoam_9 | (1 0 0)     | 1e-4                    | ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T |
| scalarTransportFoam_10 | (1 0 0)     | 1e-2                    |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T  |
| scalarTransportFoam_11 | (1 0 0)     | 1e0                     |  ∂T/∂t + ∇·(uT) = D<sub>T</sub> ∆T|

- This sequesce is followed in this repo. like scalarTransportFoam_1, scalarTransportFoam_2 upto scalarTransportFoam_11.

---

## How to run

Typical sequence (in your case folder):

1. Generate or check mesh (if using `blockMesh`):
   ```
   Already generated just clone the repo into your working directory of openFoam
   ```

2. Run the solver:
   ```
   scalarTransportFoam
   ```

3. Visualize results:
   - Using ParaView:
     ```
     paraFoam
     ```
   - Or load the `case.foam` file in ParaView and inspect `T` (or the scalar field name) through time.

---

## Expected behavior and visualization tips

- For pure diffusion, plot T(x) at several time instants to show symmetric spreading and decreasing peak.
- For pure convection, overlay T(x) at different times and observe translation of the profile.
- For combined cases, observe both translation and spreading.
- Use line plots in ParaView to see 1D profiles clearly.
- When convection dominates, try higher-order convection schemes or limiters to reduce numerical oscillations or excessive numerical diffusion.



---

## Relation to Navier–Stokes

The scalar diffusion term (DT) is analogous to viscosity in the Navier–Stokes equations: both act to diffuse gradients (one for scalar, one for momentum). Understanding scalar transport behavior builds intuition for transport of momentum and energy.

---

## References

- József Nagy (OpenFOAM tutorials) 
