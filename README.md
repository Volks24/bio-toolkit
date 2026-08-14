# bio-toolkit

A collection of small, standalone tools, datasets, and scripts I use across different structural biology / bioinformatics projects. Each item is meant to be self-contained (data file, script, or small utility) so it can be dropped into any project without extra dependencies on the rest of the repo.

## Contents

| File | Description |
|------|-------------|
| [`ligands_trash.json`](./ligands_trash.json) | Curated list of PDB heteroatom/ligand codes considered "trash" or non-relevant when parsing PDB structures — solvents, ions, buffers, detergents, standard amino acids, common cofactors (NAD/FAD families, nucleotides), and lipids. Useful for filtering out uninteresting HETATM records when scanning PDB files for real ligands of interest. |

## `ligands_trash.json`

### Structure

```json
{
  "metadata": {
    "description": "...",
    "categories": ["trash_mol", "peptide_residues", "nad_family", "fad_family", "nucleotides", "mol_junk", "lipids_and_detergents"],
    "total_unique_ligands": 479
  },
  "categories": {
    "trash_mol": ["..."],
    "peptide_residues": ["..."],
    "nad_family": ["..."],
    "fad_family": ["..."],
    "nucleotides": ["..."],
    "mol_junk": ["..."],
    "lipids_and_detergents": ["..."]
  },
  "trash_ligands": ["..."]
}
```

- **`metadata`** – short description, list of category names, and total count of unique codes.
- **`categories`** – the original groupings kept separate, in case you only want to filter out one type (e.g. only lipids, or only NAD-family cofactors).
- **`trash_ligands`** – all categories merged into a single deduplicated, sorted list. This is the one to use if you just want a blanket filter.

All codes are normalized (`strip().upper()`), deduplicated, and sorted alphabetically.

### Usage

**Python**

```python
import json

with open("ligands_trash.json") as f:
    data = json.load(f)

TRASH_LIGANDS = set(data["trash_ligands"])

# Example: filter HETATM residue names parsed from a PDB/mmCIF file
clean_ligands = [lig for lig in ligand_list if lig.upper() not in TRASH_LIGANDS]

# Or filter by a single category
lipids = set(data["categories"]["lipids_and_detergents"])
```

**R**

```r
library(jsonlite)
data <- fromJSON("ligands_trash.json")
trash_ligands <- data$trash_ligands
```

**Command line (jq)**

```bash
jq '.trash_ligands' ligands_trash.json
```

Being plain JSON, it can be loaded from any language or tool (Python, R, JavaScript, MATLAB, etc.) with no extra parsing logic.

## Philosophy

This repo is not meant to be a cohesive package — it's a personal toolbox. Each file/folder should:

- Work on its own, with minimal or no dependencies on other files in the repo.
- Include enough documentation (in this README or its own) to be usable without extra context.
- Be easy to copy-paste into another project.

## License

MIT — feel free to use, copy, and adapt any of these tools.
