# CFD Analysis of the VKI Turbine Blade Cascade using ANSYS CFX

**Tools:** ANSYS CFX 2022, SpaceClaim | **Context:** Graduate coursework — Turbomachinery and Propulsion (MECH 6171), Concordia University

## Why this project
Turbine blade cascades are highly sensitive to mesh quality — get it wrong and you either waste compute time or trust a result you shouldn't. This project analyzes the flow over a linear cascade of a VKI (Von Kármán Institute) 1st-stage gas turbine blade, extracting the isentropic Mach number distribution, total pressure loss, and temperature/velocity behavior across the blade — while explicitly proving mesh independence before trusting any of those numbers.

## Approach
1. **Geometry & setup:** Built the 3D blade cascade geometry in SpaceClaim from VKI blade profile data points (true chord 80 mm, span 10–20 mm, stagger angle 38.5°, inlet blade angle 30°, inlet flow angle 30°, outlet flow angle 69.5°, pitch 113.5 mm). Boundary conditions: total inlet temperature 415 K, total inlet pressure 288 kPa, outlet static pressure 165.5 kPa, blade wall temperature 290 K, with translational periodicity applied on the casing, hub, and tip to represent an infinite cascade.
2. **Mesh independence study:** Ran the simulation across 9 mesh densities, from 21,972 to 61,366 nodes, using a hex-dominant mesh (chosen over tetrahedral for lower node count and faster solve time at similar accuracy) with an inflation layer (0.1 mm first-layer thickness, 10 layers) to resolve near-wall flow. Inlet/outlet Mach number and mass flow rate were compared across all 9 densities; results converged to within ~10⁻³ by 45,248 nodes, which was selected as the mesh-independent solution — avoiding both an under-resolved result and unnecessary compute cost from further refinement.
3. **Solver settings:** k-ε turbulence model (appropriate here since the focus was on flow behavior away from the immediate boundary layer, not near-wall separation detail), high-resolution advection and turbulence numerics, RMS residual target of 10⁻⁶, run to convergence within 2500 iterations.
4. **Post-processing:** Extracted isentropic Mach number along both the pressure and suction surfaces of the blade and plotted it against x/c (non-dimensional chord position) to characterize the acceleration and separation behavior over the blade.

## Key results

**Blade geometry and mesh:**

![VKI blade geometry](vki_project_images/fig02_blade_geometry.jpg)
![Optimized mesh](vki_project_images/fig04_optimized_mesh.jpg)

**Mesh independence proof** — inlet/outlet Mach number and mass flow rate compared across 9 mesh densities (21,972–61,366 nodes), converging by 45,248 nodes:

![Grid independence result](vki_project_images/fig12_grid_independence.png)

**Headline result — isentropic Mach number distribution over the blade:**

![Isentropic Mach number vs x/c](vki_project_images/fig13_isentropic_mach_vs_xc.png)

**Pressure contours** — showing the pressure differential driving flow acceleration and the separation region:

![Static pressure contour over cascade](vki_project_images/fig14_static_pressure_cascade.jpg)
![Total pressure contour over cascade](vki_project_images/fig16_total_pressure_cascade.jpg)

**Findings:**
- The suction surface accelerates the flow sharply, with isentropic Mach number peaking at **1.27 near x/c ≈ 0.625**, then dropping off toward the trailing edge — consistent with flow separation developing on the aft portion of the suction surface due to an adverse pressure gradient. The pressure surface stays comparatively flat (Mach ≈ 0.20–0.22) through mid-chord before rising toward the trailing edge.
- **Total pressure loss across the blade: 108.45 kPa** (285.98 kPa at the leading edge vs. 177.53 kPa at the trailing edge), directly attributable to the separation-driven wake region identified in the velocity and pressure contours.
- Static temperature dropped modestly across the cascade, from 403.6 K at inlet to 398.3 K at outlet, consistent with the total-to-static temperature relationship at these flow velocities.
- Mesh independence was confirmed at 45,248 nodes — inlet/outlet Mach number and mass flow rate all varied by less than 0.1% beyond this point across the remaining 3 finer meshes tested (up to 61,366 nodes), justifying the choice on both accuracy and compute-efficiency grounds.

## What I'd do differently / next steps
With more time, I'd validate the isentropic Mach and pressure-loss results directly against published VKI cascade experimental data rather than relying on internal convergence alone — internal mesh independence proves the simulation is self-consistent, not that it matches reality. I'd also test a transitional or k-ω SST turbulence model against the k-ε result used here, since k-ε is known to be less reliable specifically in the separated-flow region this project identified as the main loss mechanism — the model choice may be understating the separation's true extent. Finally, per the report's own conclusion, a logical next step would be to actually iterate on the blade's angle of attack to see how much of the 108 kPa pressure loss can be recovered by delaying separation.

## Files in this repo
- Full project report (PDF): geometry setup, mesh sensitivity tables, CFX-Pre/Post configuration, and all contour plots
- `vki_project_images/` — key figures referenced above (geometry, mesh, grid-independence graph, Mach vs. x/c plot, pressure contours)
- Mesh independence dataset (9-point sensitivity sweep: node count vs. inlet/outlet Mach and mass flow rate)
- Isentropic Mach number vs. x/c dataset (pressure and suction surface)

---
*Coursework project completed as part of the M.Eng Thermofluids program at Concordia University, submitted for MECH 6171 (Turbomachinery and Propulsion).*
