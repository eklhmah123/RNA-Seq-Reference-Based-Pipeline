# Reference-based RNA-Seq Data Analysis Pipeline

## 🧬 Project Overview

This repository contains a complete reference-based RNA-Seq data analysis pipeline performed in **Galaxy**. The analysis identifies differentially expressed genes between **Pasilla (PS) gene-depleted (treated)** and **untreated** samples in *Drosophila melanogaster*.

### 📊 Dataset Information

| Sample ID | Condition | Read Type | Source |
|-----------|-----------|-----------|--------|
| GSM461177 | Untreated | Paired-end (37bp) | Zenodo |
| GSM461180 | Treated (PS depleted) | Paired-end (37bp) | Zenodo |

**Reference genome:** *Drosophila melanogaster* dm3 (BDGP6.32)

---

## 🎯 Objectives

1. Perform quality control on raw FASTQ reads
2. Trim low-quality bases using Cutadapt
3. Map reads to reference genome using STAR spliced aligner
4. Visualize mapped reads in IGV, Sashimi, and JBrowse2
5. Generate strand-specific coverage plots (blue/red)
6. Count reads per gene using featureCounts
7. Perform PCA and sample-to-sample correlation analysis
8. Identify differentially expressed genes with DESeq2

---

## 🛠️ Tools Used

| Tool | Version | Purpose |
|------|---------|---------|
| Falco | 1.2.4+galaxy0 | Quality control |
| MultiQC | 1.27+galaxy4 | Aggregate QC reports |
| Cutadapt | 5.2+galaxy0 | Trimming |
| RNA STAR | 2.7.11b+galaxy0 | Spliced alignment |
| featureCounts | 2.1.1+galaxy0 | Read counting |
| DESeq2 | - | Differential expression |
| IGV | - | Visualization |
| JBrowse2 | 3.6.5+galaxy1 | Genome browser |

---

## 📋 Complete Workflow
┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ FASTQ │ → │ Falco │ → │ Cutadapt │ → │ STAR │
│ (Raw) │ │ (QC) │ │ (Trimming) │ │ (Mapping) │
└─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
│
▼
┌─────────────┐ ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
│ DESeq2 │ ← │ featureCounts│ ← │ IGV / │ ← │ BAM │
│ (DE) │ │ (Counting) │ │ JBrowse2 │ │ (Aligned) │
└─────────────┘ └─────────────┘ └─────────────┘ └─────────────┘
│
▼
┌─────────────┐ ┌─────────────┐
│ PCA │ │ Heatmap │
│ (Clustering)│ │ (Correlation)│
└─────────────┘ └─────────────┘

text

---

## 📊 Results

### 1. Quality Control (Falco + MultiQC)

| Metric | Untreated | Treated | Status |
|--------|-----------|---------|--------|
| Read length | 37 bp | 37 bp | ✓ |
| Forward read quality | Good | Good | ✓ |
| Reverse read quality | Good | Decrease at 3' end | ⚠️ Trimmed |
| Duplication level | High (RNA-Seq expected) | High | ✓ |


### 2. Read Trimming (Cutadapt)

| Sample | Pairs removed | BP trimmed (Forward) | BP trimmed (Reverse) |
|--------|---------------|---------------------|---------------------|
| Untreated (GSM461177) | 147,810 (1.4%) | 5,072,810 | 8,648,619 |
| Treated (GSM461180) | 1,101,875 (9%) | 10,224,537 | 51,746,850 |


### 3. STAR Alignment Statistics

| Sample | Uniquely Mapped | Multi-mapped | Unmapped (too short) |
|--------|----------------|--------------|---------------------|
| Untreated | >83% | <10% | ~5% |
| Treated | 79% | <10% | ~8% |



### 4. Quality Control Checks

| Quality Metric | Untreated | Treated | Status |
|----------------|-----------|---------|--------|
| Duplicate reads | 25.9% | 27.8% | ✓ Normal (<50%) |
| Exonic reads | >80% | >80% | ✓ Good |
| Intronic reads | ~2% | ~2% | ✓ Good |
| Intergenic reads | ~5% | ~5% | ✓ Good |
| Gene body coverage | Even | Even | ✓ No bias |



### 5. Visualization

#### IGV - Region: `chr4:540,000-560,000` (dm6)

| Feature | Interpretation |
|---------|----------------|
| Grey peaks | Read coverage depth |
| Dashed lines | Splice junctions |
| Arrow direction | Read strand orientation |

<img width="1139" height="823" alt="IGV" src="https://github.com/user-attachments/assets/fa60baf8-db96-4db4-a473-771800b72119" />




#### Sashimi Plot

| Element | Meaning |
|---------|---------|
| Red vertical bars | Coverage at each position |
| Arcs with numbers | Splice junctions (number = supporting reads) |
| Stacked blue boxes | Gene models from GTF |



#### JBrowse2

> **Figure 7:** JBrowse2 genome browser with both samples 

#### Strand-Specific Coverage

| Strand | Color | Coverage |
|--------|-------|----------|
| Strand 1 | Blue | Equal to Strand 2 |
| Strand 2 | Red | Equal to Strand 1 |

**Conclusion:** Library is **UNSTRANDED** (equal coverage on both strands)

<img width="2713" height="1051" alt="JBrowse" src="https://github.com/user-attachments/assets/4f22c225-9f0b-4e62-8718-5dad52390eed" />




### 6. Differential Expression (DESeq2)

| Analysis | Status |
|----------|--------|
| PCA clustering | Treated vs untreated separate along PC1 |
| Sample correlation | High within condition groups |
| Significant genes (padj < 0.05) | [Number] |
| Upregulated in treated | [Number] |
| Downregulated in treated | [Number] |

<img width="677" height="677" alt="DESEQ1" src="https://github.com/user-attachments/assets/cc9bc502-6b38-401f-bb26-eb15df5b060e" />
<img width="672" height="680" alt="DESEQ2" src="https://github.com/user-attachments/assets/09542afd-a511-4e0e-867a-7e08474207d3" />

#### Sample-to-Sample Correlation Heatmap

<img width="740" height="740" alt="heatmap" src="https://github.com/user-attachments/assets/86096b82-c4f3-4a9b-9ad5-745308bf36d6" />


*Figure: Heatmap showing sample-to-sample variability and clustering.*

**Interpretation:**
- **Darker red squares** indicate higher correlation between samples
- **GSM461179_treat_single, GSM461180_treat_paired, GSM461181_treat_paired** cluster together (Treated group)
- **GSM461177_untreat_paired, GSM461178_untreat_paired** cluster together (Untreated group)
- Clear separation between treated and untreated conditions validates experimental design
- Higher correlation within groups than between groups confirms treatment effect

### Gene Ontology (GO) Enrichment Analysis

<img width="680" height="672" alt="GO" src="https://github.com/user-attachments/assets/79a35eb1-dca5-4c63-835c-d4eec917d51b" />


*Figure: Top over-represented GO categories in Cellular Component (CC), Biological Process (BP), and Molecular Function (MF).*

**Top enriched categories:**

| Category | Term | Count | Adjusted P-value |
|----------|------|-------|------------------|
| CC | extracellular region | ~150 | ~4e-04 |
| CC | extracellular space | ~150 | ~4e-04 |
| CC | cell periphery | ~100 | ~6e-04 |
| CC | plasma membrane | ~100 | ~6e-04 |
| CC | integral component of membrane | ~100 | ~6e-04 |
| CC | intrinsic component of membrane | ~100 | ~6e-04 |
| MF | transmembrane transporter activity | ~100 | ~6e-04 |
| MF | transporter activity | ~100 | ~6e-04 |
| MF | oxidoreductase activity | ~100 | ~6e-04 |
| MF | serine-type endopeptidase activity | ~50 | ~8e-04 |

**Biological interpretation:**
- Enrichment in **extracellular region/space** suggests Pasilla depletion affects secreted proteins
- **Membrane-associated terms** (plasma membrane, integral membrane) indicate membrane protein regulation
- **Transporter activity** enrichment points to changes in cellular transport mechanisms
- **Oxidoreductase activity** suggests metabolic pathway alterations
- **Serine-type endopeptidase activity** indicates potential protease signaling changes

**Legend:**
- 🔵 Count: ~50 | Adjusted P-value: ~8e-04
- 🟠 Count: ~100 | Adjusted P-value: ~6e-04  
- 🟡 Count: ~150 | Adjusted P-value: ~4e-04
- 🟢 Adjusted P-value: ~2e-04


### KEGG Pathway Enrichment Analysis

<img width="721" height="995" alt="kegg pathway" src="https://github.com/user-attachments/assets/0c65f0ca-4f97-4e81-a489-a867d4bfafb3" />


*Figure: KEGG pathway enrichment analysis showing top affected metabolic pathways.*

**Top enriched KEGG pathways:**

| Pathway ID | Pathway Name | Affected Genes |
|------------|--------------|----------------|
| dme00010 | Glycolysis / Gluconeogenesis | ✓ |
| dme00500 | Starch and sucrose metabolism | ✓ |

**Biological interpretation:**
- **Glycolysis/Gluconeogenesis** pathway enrichment indicates Pasilla depletion affects central carbon metabolism
- **Starch and sucrose metabolism** suggests carbohydrate metabolic processes are altered
- α-D-Glucose appears as a central metabolite connecting both pathways
- Key enzymes involved: EC 5.1.3.10, 3.1.3.9, 3.1.3.19 (phosphatases/kinases)

**Implications:**
- Pasilla gene may regulate metabolic gene expression
- Energy metabolism pathways are responsive to PS depletion
- Suggests cross-talk between splicing regulation and metabolic control



---

## 📁 Repository Structure
RNA-Seq-Reference-Based-Pipeline/
├── README.md
│
├── reports/
│ ├── MultiQC_report.html
│ ├── STAR_logs/
│ │ ├── STAR_GSM461177_log.out
│ │ └── STAR_GSM461180_log.out
│ └── DESeq2_results/
│ ├── DESeq2_normalized_counts.csv
│ └── DESeq2_significant_genes.csv
│
├── screenshots/
│ ├── IGV_chr4_540000-560000.png
│ ├── JBrowse2_view.png
│ ├── heatmap.jpg
│ ├── GO.jpg
│ └── kegg_pathway.jpg
│
└── galaxy_workflow/
├── workflow.ga
└── parameters.json

text

---

## 🔧 How to Reproduce This Analysis

### Prerequisites

- Access to a Galaxy instance (usegalaxy.org)
- IGV installed locally (for visualization)
- ~5GB free space for subsampled data

### Steps

1. **Create new history** in Galaxy
2. **Upload subsampled FASTQ files** from Zenodo:
https://zenodo.org/record/6457007/files/GSM461177_1_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461177_2_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461180_1_subsampled.fastqsanger
https://zenodo.org/record/6457007/files/GSM461180_2_subsampled.fastqsanger

text
3. **Create paired collection** named `2 PE fastqs`
4. **Run Falco** → **MultiQC** for quality control
5. **Run Cutadapt** for trimming (quality cutoff: 20, min length: 20)
6. **Run STAR** with dm6 reference genome
7. **Visualize** BAM files in IGV/JBrowse2 at `chr4:540,000-560,000`
8. **Run featureCounts** for read counting
9. **Run DESeq2** for differential expression
10. **Generate PCA and heatmap** for sample variability

---

## 📖 Key Findings

| Finding | Value |
|---------|-------|
| Library type | Unstranded |
| Unique mapping rate | >79% |
| Read assignment to genes | ~63% |
| Duplicate reads | ~26-28% |
| Strand distribution | Equal (1:1 ratio) |

---

## ⚠️ Notes

- **Subsampled data** (5MB per file) was used for faster processing
- Full data (~1.5GB per file) would provide higher coverage but longer runtime
- Reference genome: dm6 (BDGP6.32) - *Drosophila melanogaster*
- Annotation file: BDGP6.32.109_UCSC.gtf

---


## 👩‍💻 Author

**AQBA**
