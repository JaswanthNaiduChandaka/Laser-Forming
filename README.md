# Numerical Modelling of Laser Forming (ANSYS FEM)

ME 222 – Manufacturing Technology I
Jaswanth Naidu Chandaka (240103057) · Jaswanth (24010348)

**Project files, report, simulation videos and screenshots:**
https://drive.google.com/drive/folders/17HlCbLeVkKrvSc6m2VvJVGLFgO06mKVz

---

## 1. What is laser forming?

Laser forming bends a metal plate using only the heat of a laser beam. There is no die, no punch and no external force, and no material is removed, so it is **not laser cutting**.

The beam heats the top surface quickly while the bottom stays cool. The hot top layer tries to expand but is held back by the cooler material below. This builds thermal stress and bends the plate. This is called the **Temperature Gradient Mechanism (TGM)**.

## 2. Project flow

1. **Understand the process:** laser forming mechanisms (temperature gradient, buckling, upsetting) and the governing physics (heat conduction, thermal strain, thermal stress).
2. **Define the problem:** a real laser moves continuously along the plate, but ANSYS Workbench has no ready-made moving heat source.
3. **Our approximation:** a step-wise moving heat flux. A patch of surface elements as wide as the beam is loaded for one time step, then removed, and the next adjacent patch is loaded for the following step.
4. **Build the model:** 150 × 150 × 6 mm steel plate, structured hexahedral mesh (30 × 30 elements in-plane, 5 mm each).
5. **Run the analysis:** Transient Thermal, then Transient Structural, with the temperature history passed to the structural analysis (one-way coupling).
6. **Study the results:** temperature history, temperature gradient through the thickness, and the bending of the plate.
7. **State the limits:** what the model does not include (see section 6).

## 3. The key idea: the step-wise moving heat source

```
patch width  d  = number of elements x element size   (equals the beam diameter)
scan speed   v  = d / dt                              (so step time dt = d / v)
heat flux    q  = P / d^2
steps           = scan length / d
```

| Case | Patch | Step time | Speed |
|---|---|---|---|
| First trial | 2 × 2 elements (10 × 10 mm) | 1 s | 10 mm/s |
| Case study | 3 × 3 elements (15 × 15 mm) | 1 s | 15 mm/s |

For the case study: 1500 W over 15 × 15 mm gives about 6.7 W/mm², and the 150 mm scan takes 10 steps.

The average speed is exact, but the real beam moves continuously, so this is a **staircase approximation**. Smaller patches and time steps converge towards the continuous source.

## 4. Case study

| Parameter | Value |
|---|---|
| Beam diameter | 15 mm |
| Scan speed | 15 mm/s |
| Laser power | 1500 W (taken as fully absorbed) |
| Scan length | 150 mm |
| Cooling | Natural convection to ambient |
| Structural condition | One end held so the free part can bend |

## 5. Results and observations

- Temperature at every point goes through **rise, peak and fall** as the heat patch arrives and moves on, confirming the moving-source logic works.
- The surface heats first and highest. Deeper points rise later and less. The temperature difference through the 6 mm thickness is roughly 250 °C, which is what drives the bending.
- Peak temperature at the probe is about 446 °C near t = 6 s; the highest temperature in the plate view is about 531 °C.
- The plate bends because the hot top expands more than the cooler bottom. The maximum total deformation is about 0.12 mm.
- The deformation is the transient elastic response of the model, so it shows the bending tendency, not a final permanent bend angle.
- Results are consistent with the temperature gradient mechanism and with trends reported in laser forming literature.

## 6. Influence of process parameters

Each parameter has a direct input in the model, so its influence can be explored by changing one input at a time.

| Parameter | How it is set in the model | Expected effect (theory) |
|---|---|---|
| Laser power | Heat flux q = P / d² | Higher power: higher peak temperature and gradient, more bending |
| Scan speed | Step time dt = d / v | Lower speed: more heat per unit length, higher temperature, more bending |
| Beam diameter | Patch size (n × n elements) | Larger beam: wider heated zone, smoother gradient; smaller beam: sharper gradient |
| Number of irradiations | Repeat the patch sequence over the same path | Bending accumulates with each pass |
| Plate thickness | Thickness of the geometry | Thicker plate: smaller curvature |
| Material | Thermal and mechanical property inputs | Higher thermal expansion bends more; higher conductivity flattens the gradient |

## 7. Assumptions and limitations

- Laser power applied as a surface flux, fully absorbed (no absorptivity factor).
- Square uniform flux per patch that switches on and off each step, not a smooth Gaussian continuous beam.
- Constant, temperature-independent steel properties.
- One-way thermal to structural coupling.
- Linear-elastic structural behaviour: no plasticity, so no permanent bend angle or residual stress.
- Natural convection only, no radiation, no phase transformation.
- Coarse 5 mm mesh, with no mesh-convergence or time-step study.
- No direct comparison with measured experimental data.

The model is a qualitative, learning-level demonstration of how a temperature gradient bends a plate. It is not a calibrated predictor of exact bend angles.

## 8. Future work

1. Add plasticity and full cooling to get the permanent bend angle and residual stress.
2. Use temperature-dependent steel properties and an absorptivity factor.
3. Use a continuous moving Gaussian source (APDL or user-defined load) with a finer mesh and time step.
4. Run a systematic study of power, speed, beam size, thickness and number of passes.
5. Compare against published data or experiments.

## 9. References

- Hyung-Chul Jung, *A Study on Laser Forming Processes with Finite Element Analysis*.
- Vollertsen, F., "Mechanisms and models for laser forming", Journal of Materials Processing Technology, 1994.
- Cheng, J. and Yao, Y.L., "Laser forming of metallic components: modeling and experiments", Journal of Manufacturing Science and Engineering (ASME), 2001.
- Yau, C.H. and Yang, Y.C., "Finite element analysis of laser forming process", International Journal of Machine Tools and Manufacture, 1998.
- Sheng, Z.Q. and Yao, Y.L., "Thermo-mechanical modeling of laser forming process", Journal of Manufacturing Processes, 2002.
- ANSYS Inc., ANSYS Mechanical APDL Theory Reference (thermal and structural analysis).
