# KiCad CI Automation

This repository includes a GitHub Actions workflow that automatically validates and exports output files for a KiCad 9 project. Whenever schematic or PCB files change, the workflow runs ERC/DRC checks, generates fabrication outputs, and uploads them as downloadable artifacts.

---

## 📌 What This Workflow Does

The CI pipeline automatically performs the following tasks:

### 1. Detects Changes to KiCad Files  
The workflow only runs when one or more of these files change:

- `*.kicad_sch`
- `*.kicad_pcb`

This prevents unnecessary CI runs and saves time.

### 2. Schematic Processing  
The workflow:

- Runs **ERC (Electrical Rules Check)**  
- Generates a **schematic PDF**  
- Saves all schematic outputs inside:  
  ```
  outputs/schematic/
  ```

If ERC failures occur, the workflow fails so you know issues need to be resolved.

### 3. PCB Processing  
Before running PCB steps, the workflow checks whether the `.kicad_pcb` file includes copper layers.

#### If the PCB **has no copper layers**:
- DRC is skipped  
- PCB PDF export is skipped  
- Gerbers and Drill files are skipped  

#### If the PCB **has valid copper layers**:
The workflow generates:
- A PCB **PDF**
- A **DRC report**
- **Gerber** files
- **Drill** files
- A **Pick-and-Place** CSV
- A **BOM** (Bill of Materials) CSV

All PCB outputs are placed in:

```
outputs/pcb/
```

### 4. Artifact Upload  
After processing, the CI bundles all files inside `outputs/` and uploads them as a downloadable artifact.

You can find artifacts in:

**GitHub → Actions → Select the recent run → Artifacts → kicad-outputs**

---

## 🛠️ Required Setup: Setting Your Project Name

The workflow needs the name of your KiCad project so it knows which files to process.

### How to determine your project name

Find your main KiCad files:

```
my_project.kicad_sch
my_project.kicad_pcb
```

Your project name is:

```
my_project
```

### Where to set the project name

Open:

```
.github/workflows/kicad-ci.yml
```

Locate the environment variable:

```yaml
env:
  PROJECT_NAME: "CHANGE_ME"
```

Replace `"CHANGE_ME"` with your actual project name:

```yaml
env:
  PROJECT_NAME: "my_project"
```

This **must** match the file name (without the `.kicad_sch` or `.kicad_pcb` extension) or the workflow will not find your KiCad project.

---

## 📦 Output Files Generated

When the workflow completes, these files will be located in the `outputs/` directory:

### Schematic Outputs
- Schematic PDF  
- ERC report  

### PCB Outputs (only if copper layers exist)
- PCB PDF  
- DRC report  
- Gerber files  
- Drill files  
- BOM (CSV)  
- Pick-and-Place CSV  

All files are packaged and uploaded as a CI artifact.

---

## 📛 Optional Build Status Badge

To display a build badge in your README, add:

```md
![Build Status](https://img.shields.io/github/actions/workflow/status/your-username/your-repo/kicad-ci.yml)
```

Replace:

- `your-username`
- `your-repo`

with your GitHub details.

---

## 📝 Notes

- Designed for **KiCad 9**  
- Automatically detects empty/incomplete PCB designs  
- Does not run on unrelated file changes  
- Outputs are organized cleanly for fabrication, review, and documentation  

This CI system helps ensure your KiCad project stays validated and ready for manufacturing with every commit.
