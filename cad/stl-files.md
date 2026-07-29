# 3D Printing & CAD Design Files

This directory contains all 3D print model files (`.stl`) and CAD source files (`.ipt`) for the **Smart Automatic Medication Dispenser**.

---

## File Directory & Recommended Slicer Settings

### General Print Settings
- **Filament Material:** PETG
- **Layer Height:** `0.2 mm`
- **Walls / Perimeters:** 3–4 wall loops

### File List & Component Breakdown

| File Name | Color / Filament | Infill Density | Description / Function |
| :--- | :--- | :---: | :--- |
| **`Bánh-răng-lớn.stl`** | Grey | 50% | Driven gear attached to the rotating tray (7.5:1 ratio) |
| **`Bánh-răng-nhỏ.stl`** | Grey | 50% | Pinion gear attached to the 28BYJ-48 stepper motor shaft |
| **`Trục-trung-tâm.stl`** | Grey | 50% | Central alignment axle / shaft |
| **`Khay-hứng-thuốc.stl`** | Grey | 50% | Receiving tray / output chute where dispensed pills drop |
| **`Base.stl`** | Grey | 50% | Main structural housing for components & motor mount |
| **`Đế.stl`** | Light Blue | 60% | Bottom base plate and support structure |
| **`Khay-đựng.stl`** | White | 80% | 15-compartment rotating medication tray |
| **`Nắp.stl`** | Semi-transparent | 60% | Top protective cover / lid |

---

## Customizing Tray Labels (`.ipt` Source File)

The CAD source file **`Khay-đựng-Custom.ipt`** (Autodesk Inventor) is provided to edit or customize the day/time labels engraved or raised on the tray (e.g., changing from AM/PM to custom languages or symbols).

### Editing Steps:
1. Open **`Khay-đựng-Custom.ipt`** in **Autodesk Inventor** (2020 or newer).
2. In the Model Browser tree (left panel), locate and right-click on **`Sketch_Labels`** / **`Extrude_Text`**.
3. Select **Edit Sketch** to modify the text strings, fonts, or embossing depth.
4. Export the updated model: Go to `File` ➔ `Export` ➔ `CAD Format` and select `.stl`.
5. Import the newly exported `.stl` into your Slicer (Cura, PrusaSlicer, Bambu Studio) and slice using **80% Infill** with **PETG**.
