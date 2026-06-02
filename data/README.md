---
noteId: "2890a7204e4311f1a2570378ad05fce2"
tags: []

---

# Data

This folder contains sample data and instructions for obtaining the datasets used in the paper.

## Cancer signaling network

The cancer signaling network used in the paper was constructed from:

1. **Cancer Gene Census** (https://cancer.sanger.ac.uk/census)
   - Curated list of cancer-associated genes
   - Contains 379 proteins after filtering

2. **STRING database** (https://string-db.org)
   - Protein-protein interaction scores
   - Filtered for medium-confidence interactions (combined score > 400, i.e., > 0.4)
   - Results in 3,498 interactions

## Downloading the data

### Option 1: Use provided scripts

```bash
# Download and process network data
python scripts/download_data.py
```

### Option 2: Manual download

1. Download cancer genes from Cancer Gene Census
2. Query STRING API for interactions:
   ```python
   import requests

   proteins = ["TP53", "BRCA1", "EGFR", ...]  # Your protein list
   url = "https://string-db.org/api/tsv/network"
   params = {
       "identifiers": "%0d".join(proteins),
       "species": 9606,  # Human
       "required_score": 400
   }
   response = requests.get(url, params=params)
   ```

3. Save as edgelist format:
   ```
   PROTEIN1 PROTEIN2
   TP53 MDM2
   BRCA1 BARD1
   ...
   ```

## Data format

### `protein_network.edgelist`
```
PROTEIN1 PROTEIN2
TP53 MDM2
BRCA1 BARD1
...
```

### `protein_annotations.csv`
```csv
protein,category,category_id
TP53,Cell cycle,0
BRCA1,DNA repair,2
...
```

## Document corpus

The document corpus used in the paper is **NOT** retrieved from PubMed. Instead, it consists of 1,895 synthetic mechanistic-annotation templates generated programmatically from the 14 functional pathway categories.

Templates are defined inline in `examples/learnable_cancer_network.py` (see the `templates = {...}` dictionary). For each protein, a set of category-specific template strings is used to construct short documents describing the protein's hypothesized mechanistic role within its assigned functional category.

This synthetic corpus enables controlled study of the contribution of retrieval augmentation independent of the quality of an external biomedical text encoder. A natural extension is to replace these templates with retrieved PubMed abstracts encoded via pretrained biomedical language models (BioBERT, PubMedBERT) — see the "Future directions" section of the paper.

## License

- STRING data: CC BY 4.0
- Cancer Gene Census: Academic use only
- Synthetic mechanistic-annotation document corpus: MIT (same as repository)
