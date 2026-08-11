# 🖨️ 3D Printing & CAD Files

<img width="1010" height="651" alt="AutoDose CAD model" src="https://github.com/user-attachments/assets/dd6e16b9-1008-42a6-9518-8bdded67291b" />

This folder contains the 3D-printable `.stl` files I used to build AutoDose, along with the original Autodesk Inventor `.ipt` files.

I designed the parts in **Autodesk Inventor** and printed them in **PETG**. The settings below are what I used for my prints, but you may need to adjust them depending on your printer and filament.

---

## 🖨️ Print Settings

These are the general settings I used:

* **Material:** PETG
* **Layer height:** `0.2 mm`
* **Walls / Perimeters:** `3–4`
* **Infill:** Depends on the part — see the table below

The parts don't all need the same amount of infill. The gears and rotating tray, for example, need to be fairly solid, while some of the structural parts can get away with less.

---

## 📁 What's in the Folder?

| File                  | Filament         | Infill | What it does                                    |
| --------------------- | ---------------- | :----: | ----------------------------------------------- |
| `Bánh-răng-lớn.stl`   | Grey             |   50%  | Large gear connected to the rotating tray       |
| `Bánh-răng-nhỏ.stl`   | Grey             |   50%  | Small gear connected to the stepper motor       |
| `Trục-trung-tâm.stl`  | Grey             |   50%  | Keeps the rotating tray aligned                 |
| `Khay-hứng-thuốc.stl` | Grey             |   50%  | Tray where the pill lands after being dispensed |
| `Base.stl`            | Grey             |   50%  | Main housing and motor support                  |
| `Đế.stl`              | Light Blue       |   60%  | Bottom plate and support                        |
| `Khay-đựng.stl`       | White            |   80%  | 15-compartment rotating medication tray         |
| `Nắp.stl`             | Semi-transparent |   60%  | Top cover                                       |

The Vietnamese filenames are just the names I used while designing the parts. Feel free to rename them if you prefer.

---

## ⚙️ A Note About the Gears

The two gears make up the **7.5:1 reduction system** used to rotate the medication tray.

The small gear is connected to the **28BYJ-48 stepper motor**, while the larger gear is connected to the tray.

This reduction is important because it gives me exactly **2,048 motor steps per compartment**, which helps keep the tray aligned.

---

## ✏️ Changing the Tray Labels

I included the original Autodesk Inventor file for the rotating tray:

```text
Khay-đựng.ipt
```

I originally made the labels in Vietnamese because this version of AutoDose is for use at home. If you're building your own version, you can change the labels to another language, different abbreviations, or whatever works for your setup.

### How to edit them

1. Open `Khay-đựng.ipt` in **Autodesk Inventor**.
2. Find the sketch/feature containing the tray labels in the Model Browser.
3. Edit the sketch and change the text.
4. Keep the text within the available space on each compartment.
5. Finish the sketch and update the model.
6. Export the updated part as an `.stl` file.
7. Open the new `.stl` in your slicer.
8. Slice it using your preferred PETG settings.

I used **Bambu Studio** for slicing, but the files should also work with other slicers such as Cura or PrusaSlicer.

For the tray, I used around **80% infill**.

---

## 🔧 A Few Things to Keep in Mind

The parts were designed around the specific components used in my build, so dimensions may need small adjustments if you're using different hardware.

In particular, check:

* The fit between the stepper motor and small gear
* The fit of the center shaft
* The spacing between the rotating tray and the base
* The clearance around the pill drop area
* The fit of the top cover

**PETG can also shrink or warp slightly depending on your printer**, so don't be surprised if you need to adjust tolerances or reprint a part.

This is still a hackathon project, so the CAD files aren't meant to be a perfect, production-ready design. They're here so you can see how I built it and hopefully make your own version.
