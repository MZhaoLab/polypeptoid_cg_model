    
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
- ✅ Residue-level mapping (~19 residues)  
- ⚠️ Documentation incomplete  
- ⚠️ Workflow still being standardized  
- ⚠️ Naming conventions may evolve  

---

## 📦 Repository Structure

```
.
├── force_field/
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
│
└── README.md
```

---

## ⚙️ Requirements

- GROMACS (≥ 2022 recommended)  
- martinize2 (vermouth-martinize)  
  https://github.com/marrink-lab/vermouth-martinize  
- Python (for martinize2 dependencies)

---

## 🚀 Preliminary Workflow (AA → CG → Simulation)

> ⚠️ This workflow is functional but may change.

---

### Step 1: Generate All-Atom Peptoid Structure

We strongly recommend generating structures using:

MoSiC-CGenFF-NTOID  
https://github.com/UWPRG/mftoid-rev-residues  

**Why this is important:**

- Ensures consistent residue naming  
- Matches parameterization reference systems  
- Avoids mapping errors during CG conversion  

---

### Input Requirements

Your `.pdb` file should:

- Use residue names consistent with `mappings/`
- Be chemically complete (no missing atoms)
- Be properly capped if needed

---

### Step 2: Generate CG Model with martinize2

Use martinize2 to convert the all-atom structure:

```bash
martinize2 \
  -f input_structure.pdb \
  -o topol.top \
  -x cg_structure.pdb \
  -ff martini3001 \
  -map mappings/ \
  -dssp none
```

**Notes:**

- The `-map` flag is required  
- This repository provides residue-specific mapping files  
- martinize2 does not natively support peptoids — this repo extends it  

---

### Step 3: Include Peptoid Force Field

Add the force field to your topology file:

```cpp
#include "martini_v3.0.0.itp"
#include "force_field/polypeptoid_cg.ff/forcefield.itp"
```

> ⚠️ Exact inclusion structure may be refined in future updates

---

### Step 4: Run Simulation

```bash
gmx grompp -f mdp.mdp -c cg_structure.pdb -p topol.top -o topol.tpr
gmx mdrun -deffnm md
```

---

### Recommended Settings

- `nrexcl = 3`

---

## 🔗 How This Works

- martinize2:
  - Converts all-atom → coarse-grained  
  - Generates topology  

- This repository:
  - Provides mapping rules  
  - Provides bonded interaction parameters  

- MARTINI 3:
  - Provides nonbonded interaction parameters  

---

## ⚠️ Known Limitations

- Mapping scheme still being refined  
- Some residues require further validation  
- Limited benchmarking for long chains  
- Documentation incomplete  

---

## 🧪 Validation (Ongoing)

Initial validation shows good agreement with all-atom simulations in:

- Structural properties  
- Thermodynamic trends  
- Aggregation behavior  

Further benchmarking is ongoing.

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

Mingfei Zhao  
Assistant Professor  
University of Alabama  
mingfei.zhao@ua.edu
---

## 📖 Reference

If you use this work, please cite:

Wang, J.; Yu, Z.; Zhao, M.  
*Extending the MARTINI 3 Coarse-Grained Force Field to Polypeptoids* (submitted)

---

## 🔮 Planned Improvements

- Complete residue documentation  
- Standardized naming conventions  
- Expanded examples  
- Improved martinize2 integration  
- Automated validation workflows  
