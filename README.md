# Numerical Modelling of Laser Forming

A finite element based numerical study of laser forming — a thermal metal bending process — conducted as part of ME222 - Manufacturing Technology I. The project models a moving laser heat source, analyzes transient thermal response and deformation behavior, and validates results against theoretical expectations.

---

## What is Laser Forming

Laser forming is a thermal process where a metal plate is bent using localized heating from a laser beam without any external mechanical force. The laser creates a non-uniform temperature distribution across the plate thickness. This differential expansion generates thermal stresses causing permanent plastic deformation and bending.

---

## How the Simulation Works

```
Laser Heat Input
        ↓
Step-wise Heat Flux Applied over 2×2 Element Patch
        ↓
Patch Shifts Forward Each Time Step (Simulates Scan Speed)
        ↓
Transient Thermal Analysis (ANSYS)
→ Temperature Field
→ Temperature Gradient Through Thickness
        ↓
Thermal Field Imported into Structural Analysis
→ Thermal Strain
→ Thermal Stress
→ Bending Deformation
```

---

## Simulation Setup

| Parameter | Value |
|---|---|
| Plate Dimensions | 150 mm × 150 mm × 6 mm |
| Mesh | 30 × 30 structured hexahedral elements |
| Element Size | 5 mm × 5 mm |
| Laser Beam Diameter | 15 mm |
| Scan Speed | 15 mm/s |
| Laser Power | 1500 W |
| Heat Transfer Coefficient | 10 W/mm²·K |
| Software | ANSYS 2026 R1 |

---

## Key Concepts

1. **Temperature Gradient Mechanism (TGM)** — dominant deformation mechanism in thin plates; steep gradient causes top surface to expand more than bottom
2. **Buckling Mechanism** — significant when compressive stresses exceed critical limit in thinner sheets
3. **Upsetting Mechanism** — uniform through-thickness heating causing in-plane contraction
4. **Step-wise Heat Flux Approach** — discrete spatial activation of heat patch each time step to simulate continuous laser scanning

---

## Results

- Temperature–time response at mid-section shows clear **rise–peak–fall** behavior as laser passes
- Through-thickness temperature gradient confirmed — validates TGM as dominant mechanism
- Maximum deformation: **0.11961 mm** at the leading edge of scan path
- Deformation behavior consistent with theoretical expectations of laser forming
- Process parameters (laser power, scan speed, beam diameter) shown to significantly influence bending angle

---

## Process Parameters Effect

| Parameter | Effect on Deformation |
|---|---|
| Higher Laser Power | More heat input → larger temperature gradient → more bending |
| Lower Scan Speed | More heat per unit length → higher temperature rise → greater bending |
| Larger Beam Diameter | Wider heat zone, smoother gradients, less concentrated deformation |
| Greater Plate Thickness | Reduces temperature gradient → less bending |

---

## Project Media

| File | Description |
|---|---|
| `media/mesh_model.png` | FEM mesh — 30×30 structured hexahedral elements |
| `media/temperature_field.png` | Thermal contour showing peak temperature at laser location |
| `media/temperature_time_graph.png` | Rise-peak-fall response at mid-section probe |
| `media/deformation_result.png` | Total deformation contour from structural analysis |
| `media/demo_simulation.mp4` | ANSYS simulation running — laser scan animation |
| `media/demo_deformation.mp4` | Deformation result animation |
| `media/demo_mechanism.mp4` | Overview of full thermo-mechanical process |

---

## Repository Structure

```
LASER-FORMING-FEM/
    README.md
    media/
        mesh_model.png
        temperature_field.png
        temperature_time_graph.png
        deformation_result.png
        demo_simulation.mp4
        demo_deformation.mp4
        demo_mechanism.mp4
```


