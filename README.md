# Numerical Modelling of Laser Forming (ANSYS FEM)

Transient thermal → structural finite element model of laser forming on a steel plate, with the moving laser represented by a **step-wise heat-flux patch** method.

> **Scope in one line:** a learning-level, assumption-based model that shows *how* a laser's temperature gradient bends a plate. It reproduces trends, not exact bend angles, and has not been validated against experiments. Limits are listed [below](#assumptions-and-limitations).

**ME 222 – Manufacturing Technology I** · Jaswanth Naidu Chandaka (240103057) · Jaswanth (24010348)

---

## 1. What is laser forming?

Laser forming bends sheet metal using only the heat of a laser. There is **no die, punch or external force**, and no material is removed, so it is *not* laser cutting.

The beam heats the top surface quickly while the bottom stays cool. The hot top layer tries to expand but is held back by the cooler material below, so thermal stresses build up and the plate bends. This is the **Temperature Gradient Mechanism (TGM)**. See [`mechanisms_and_physics.md`](mechanisms_and_physics.md).

## 2. The modelling problem and our approach

A real laser moves continuously along the plate, but ANSYS Workbench has no ready-made moving heat source. We approximate the scan with **sequential heat-flux patches**:

1. A patch of surface elements, as wide as the beam, is loaded with heat flux for one time step.
2. The flux is removed, and the next adjacent patch is loaded for the following step.
3. The step time sets the scan speed:

```
patch width  d  = n x element size          (= beam diameter)
scan speed   v  = d / dt                    (so dt = d / v)
heat flux    q  = P / d^2
steps           = scan length / d
```

| Case | Patch | Step time | Speed | Flux (for 1500 W) |
|---|---|---|---|---|
| First trial | 2 × 2 elements = 10 × 10 mm | 1 s | 10 mm/s | 15 W/mm² |
| **Case study** | **3 × 3 elements = 15 × 15 mm** | **1 s** | **15 mm/s** | **6.67 W/mm²** |

The average speed is exact, but the real beam moves continuously, so this is a **staircase approximation**. Smaller patches and time steps converge towards the continuous source. Details: [`moving_heat_source.md`](moving_heat_source.md). The helper [`patch_schedule.py`](patch_schedule.py) prints the patch layout and timing for any beam, speed, power and number of passes.

## 3. Model summary

| Item | Choice |
|---|---|
| Geometry | Plate 150 × 150 × 6 mm |
| Mesh | Structured hexahedral; 30 × 30 elements in-plane (5 × 5 mm), extruded through the thickness |
| Material | Structural steel (Workbench default, constant properties) |
| Analyses | Transient Thermal → Transient Structural (one-way coupled) |
| Heat input | Surface heat flux on a moving square patch (see above) |
| Cooling | Natural convection to ambient, no radiation |
| Structural BC | One end held so the free part can bend |
| Case study | Beam 15 mm · speed 15 mm/s · power 1500 W (taken as fully absorbed) · 10 steps of 1 s |

Full set-up and the equations behind it: [`model_setup.md`](model_setup.md).

## 4. Results (case study)

| | |
|---|---|
| ![Mesh](01_mesh.png) | ![Heat patch](02_heat_flux_patch.png) |
| Mesh of the plate | Heat-flux patch at the start of the scan |
| ![Temperature contour](03_temperature_contour.png) | ![Temperature vs time](04_temperature_vs_time.png) |
| Temperature field during the scan (max ≈ 531 °C in view at t = 6.9 s) | Temperature history at three points through the thickness (peak ≈ 446 °C near t = 6 s) |
| ![Through-thickness gradient](05_through_thickness_gradient.png) | ![Deformation](06_total_deformation.png) |
| Temperature through the thickness: ≈ 427 °C at the top falling to ≈ 177 °C near 5 mm depth | Total deformation at t = 7.37 s, maximum ≈ 0.12 mm (plate held at the blue end) |

What this shows:

- Every point goes through **rise → peak → fall** as the patch arrives and moves on, so the moving-source logic works.
- The surface heats first and highest; deeper points rise later and less. The through-thickness difference is roughly **250 °C**, the driver of TGM bending.
- The plate curves because the hot top expands more than the bottom, consistent with the temperature-gradient mechanism.
- The deformation is the **transient elastic response** of the model. It shows the bending tendency, not a final permanent bend angle.

## 5. Influence of process parameters

Each laser-forming parameter maps to a model input, so the effect of each can be studied by changing one input at a time:

| Parameter | Set in the model by | Expected effect (theory) |
|---|---|---|
| Beam diameter | Patch size (n × n elements) | Larger beam: wider heated zone, smoother gradient. Smaller: sharper gradient |
| Scan speed | Step time dt = d / v | Lower speed: more heat per unit length, higher temperature, more bending |
| Laser power | Heat flux q = P / d² | Higher power: higher peak temperature and gradient, more bending |
| Number of irradiations | Repeat the patch sequence over the same path | Bending accumulates with each pass |
| Plate thickness | Extrusion thickness of the geometry | Thicker plate: smaller curvature (κ ≈ αΔT/t) |
| Material | Thermal and mechanical property inputs | Higher α bends more; higher k flattens the gradient |

The case study is one parameter set; the table gives the trend expected from theory for each change. Results of any additional runs are logged in [`process_parameters.md`](process_parameters.md).

## Assumptions and limitations

- Laser power applied as a surface flux, fully absorbed (no reflectivity or absorptivity factor), so heat input is an upper bound.
- Square, uniform flux per patch that switches on and off each step, not a smooth Gaussian continuous beam.
- Constant, temperature-independent steel properties.
- One-way thermal → structural coupling (small deformation barely changes heat flow).
- Linear-elastic structural behaviour: **no plasticity**, so no permanent bend angle and no residual stress.
- Natural convection only; no radiation; no phase transformation in the steel.
- Coarse 5 mm in-plane mesh; no mesh-convergence or time-step study.
- No direct comparison with measured experimental data; agreement is with trends reported in the literature.

## Future work

1. Add plasticity and full cooling to obtain the permanent bend angle and residual stress.
2. Temperature-dependent steel properties and an absorptivity factor.
3. Continuous moving Gaussian source (APDL or a user-defined load) with a refined mesh and time step.
4. Systematic parametric study of power, speed, beam size, thickness and number of passes.
5. Comparison with published data or experiments; phase-transformation effects for steels.

## Repository files

```
README.md
Laser_Forming_Presentation.pdf   slides (18 pages)
model_setup.md                   geometry, mesh, materials, boundary conditions, solver chain
moving_heat_source.md            the step-wise heat-flux method in detail
mechanisms_and_physics.md        TGM / buckling / upsetting and the governing equations
process_parameters.md            parameter study notes and run log
patch_schedule.py                patch size, step time and flux calculator
01_mesh.png ... 06_total_deformation.png   simulation screenshots
```

## Demo videos

<!-- Add links here, for example:
- Temperature field animation: <link>
- Bending / deformation animation: <link>
-->

## References

- Hyung-Chul Jung, *A Study on Laser Forming Processes with Finite Element Analysis*.
- Vollertsen, F., "Mechanisms and models for laser forming", Journal of Materials Processing Technology, 1994.
- Cheng, J. and Yao, Y.L., "Laser forming of metallic components: modeling and experiments", Journal of Manufacturing Science and Engineering (ASME), 2001.
- Yau, C.H. and Yang, Y.C., "Finite element analysis of laser forming process", International Journal of Machine Tools and Manufacture, 1998.
- Sheng, Z.Q. and Yao, Y.L., "Thermo-mechanical modeling of laser forming process", Journal of Manufacturing Processes, 2002.
- ANSYS Inc., ANSYS Mechanical APDL Theory Reference (thermal and structural analysis).
