    
# 🚧 MARTINI 3 Coarse-Grained Force Field for Polypeptoids

> ⚠️ This repository is under active development.  
> Documentation, workflows, and file structure may change.

---

## 🧬 Overview

This repository provides a **MARTINI 3–compatible coarse-grained (CG) force field for polypeptoids**, including:

- A MARTINI-style force field directory (`polypeptoid_cg.ff`)
- Residue-specific mapping files for CG conversion
- Compatibility with automated CG model generation using martinize2

The model is developed using a **bottom-up parameterization strategy** and validated against all-atom simulations.

---

## ⚠️ Current Status

- ✅ Core force field implemented  
- ✅ Residue-level mapping 
- ✅ Preliminary `martinize2` workflow available
- ✅ Example input structure planned/provided for testing
- ⚠️ Documentation incomplete  
- ⚠️ Workflow still being standardized  
- ⚠️ Naming conventions may evolve  

---

## 📦 Repository Structure

```
.
├── ff/
│   └── polypeptoid_cg.ff
│
├── mappings/
│   ├── ALAp.map
│   ├── ARGp.map
│   ├── ASNp.map
│   ├── ASPd.map
│   ├── CYSd.map
│   ├── GLNp.map
│   ├── GLUd.map
│   ├── HSDp.map
│   ├── ILEp.map
│   ├── LEUp.map
│   ├── LYSp.map
│   ├── METd.map
│   ├── NAEk.map
│   ├── PHEp.map
│   ├── SERp.map
│   ├── THRp.map
│   ├── TRPp.map
│   ├── TYRp.map
│   └── VALp.map
|
├── examples/
│       ├── force_fields/
│       │       └── polypeptoid_cg.ff
│       ├── mappings/
│       │       └── example.map
│       ├── example_all_atom.pdb
│       └── README.md
│
└── README.md
```
> Note: The `examples/` directory is intended to provide a simple all-atom PDB input and example commands so users can test the workflow before preparing their own structures.
---

## ⚙️ Requirements

- GROMACS (≥ 2022 recommended)  
- martinize2 (vermouth-martinize)  
  https://github.com/marrink-lab/vermouth-martinize  
- Python (for martinize2 dependencies)

---

## 🚀 Preliminary Workflow (AA → CG → Simulation)

> ⚠️ This workflow is functional but may be refined in future updates.

---

### Step 1: Generate All-Atom Peptoid Structure

We strongly recommend generating structures using:

**MoSiC-CGenFF-NTOID**  
https://github.com/UWPRG/mftoid-rev-residues  

**Why this is important:**

- Ensures consistent residue naming  
- Matches parameterization reference systems  
- Avoids mapping errors during CG conversion  
- Provides chemically complete peptoid structures suitable for conversion

---

### Input Requirements

Your `.pdb` file should:

- Use residue names consistent with `mappings/`
- Contain all atoms required by the mapping files
- Be chemically complete (no missing atoms)
- Be properly capped if needed

---

## Step 2: Install the Polypeptoid CG Files into `martinize2`

`martinize2` stores force-field and mapping data inside the `vermouth` package directory. You can locate this directory using:

```bash
python -c "import vermouth, os; print(os.path.dirname(vermouth.__file__))"
```

This will print a path similar to:

```text
/path/to/python/site-packages/vermouth
```

In the instructions below, replace `/path/to/python/site-packages/vermouth` with the path printed on your system.

---

### 2.1 Copy the Polypeptoid Force Field

Copy the force-field directory into the `vermouth` force-field data directory:

```bash
cp -r ff/polypeptoid_cg.ff \
/path/to/python/site-packages/vermouth/data/force_fields/
```

After copying, you should have:


```text
/path/to/python/site-packages/vermouth/data/force_fields/polypeptoid_cg.ff
```

---

### 2.2 Copy the Mapping Files

Copy the peptoid mapping files into the `vermouth` mapping directory:

```bash
cp mappings/*.map \
/path/to/python/site-packages/vermouth/data/mappings/
```

---

### 2.3 Check All-Atom Residue Definitions

For all-atom input structures generated using MoSiC-CGenFF-NTOID, users should ensure that the corresponding peptoid residue definitions are available and that residue names are consistent with the mapping files provided here.

If `martinize2` cannot recognize a residue, first check that:

- The residue name in the PDB matches the mapping file name.
- The atom names in the PDB match the atom names expected by the mapping file.
- The peptoid RTP/residue definition from MoSiC-CGenFF-NTOID is available if needed.

---

## Step 3: Test the Workflow Using the Example PDB

Before applying the workflow to your own system, we recommend testing the installation using the example provided in:

```text
examples/example_all_atom.pdb
```

Example command:

```bash
martinize2 \
  -f examples/example_peptoid/example_all_atom.pdb \
  -o topol.top \
  -x cg_structure.pdb \
  -ff polypeptoid_cg \
  -map martini3001 \
  -dssp none
```
Martinize2 installation:
https://cgmartini.nl/docs/downloads/tools/topology-structure-generation.html

This example is intended to help users verify that the force-field files, mapping files, and residue naming are correctly installed before preparing their own systems.
---

## Step 4: Generate a CG Model from Your Own Peptoid Structure

Once the example works, users can convert their own all-atom peptoid structure:

```bash
martinize2 \
  -f input_structure.pdb \
  -o topol.top \
  -x cg_structure.pdb \
  -ff-dir force_fields \
  -map-dir mappings/ \
  -dssp none
```
Note:
This workflow converts peptoid molecules into MARTINI CG representations.
Bulk solvent (water) is typically added directly using MARTINI CG water models rather than atomistically coarse-grained.
---

## Step 5: Check the Generated Topology

The generated topology should include the relevant MARTINI 3 and polypeptoid force-field files. Depending on your local installation and workflow, the include section may look similar to:

```cpp
#include "martini_v3.0.0.itp"
#include "forcefield.itp"
```

For this model, we recommend using:

```text
nrexcl = 3
```

---

> ⚠️ Exact inclusion structure may be refined in future updates

---

---

## Step 6: Run a CG Simulation

Prepare the simulation input:

```bash
gmx grompp -f mdp.mdp -c cg_structure.pdb -p topol.top -o topol.tpr
```

Run the simulation:

```bash
gmx mdrun -deffnm md
```

---


## 🔗 How This Works

- martinize2:
  - Converts all-atom → coarse-grained  
  - Generates topology 
  - Uses the mapping and force-field files provided here


- This repository:
  - Provides peptoid-specific mapping rules
  - Provides MARTINI 3-compatible bonded parameters
  - Extends `martinize2`-based workflows to polypeptoids  

- MARTINI 3:
  - Provides standard bead types and nonbonded interaction parameters
  - Provides the general framework for CG simulation
---

## ⚠️ Known Limitations

- This repository is still under active development.
- Some residue types require further validation.
- Long-chain behavior remains under additional benchmarking.
- Charged side-chain chemistries may require further refinement.
- Documentation and examples are still being expanded.
- The workflow currently assumes careful consistency between PDB residue/atom names and the mapping files.

---

## 🧪 Validation (Ongoing)

Initial validation shows agreement with all-atom simulations in several properties, including:

- Local bonded distributions
- Chain-size trends
- Dimerization behavior
- Early-stage aggregation behavior

Further benchmarking is ongoing, especially for longer chains and charged side-chain chemistries.

---

## 💡 Notes for Early Users

- Expect breaking changes  
- Start with small systems  
- Verify CG structures before production runs  

---

## 🤝 Feedback

We welcome feedback during development:

- Report issues  
- Suggest improvements  
- Share use cases  

---

## 📬 Contact

Mingfei Zhao, PhD  
University of Alabama  
mingfei.zhao@ua.edu
---

## 📖 Reference

If you use this work, please cite:

Wang, J.; Yu, Z.; Zhao, M.  
*Extending the MARTINI 3 Coarse-Grained Force Field to Polypeptoids* https://www.biorxiv.org/content/10.64898/2026.04.10.717689v1

---

## 🔮 Planned Improvements

- Complete residue documentation
- Expanded example systems
- Standardized naming conventions
- Improved `martinize2` integration
- Automated validation workflows
- Additional testing for charged and polar residues
- Clearer troubleshooting guide 
