# Snippy Cluster Pipeline

A Snakemake workflow for bacterial genomics that processes short-read sequencing data and genome assemblies to generate SNP-based distance matrices and interactive network visualizations for outbreak analysis.

## Overview

This pipeline uses **snippy-ng** for variant calling against a reference genome, performs core SNP filtering, calculates pairwise distances, and generates an interactive network visualization for outbreak analysis and genomic epidemiology.

## Features

- **Dual Input Support**: Handles both paired-end FASTQ files and genome assemblies (FASTA)
- **Automated Sample Detection**: Can automatically discover samples from directory structure
- **Core SNP Analysis**: Filters variants to identify core genome SNPs
- **Distance Analysis**: Calculates SNP-based pairwise distances and optional phylogenetic tree
- **Interactive Visualization**: Web-based network analysis with clustering and metadata integration
- **Responsive Design**: Mobile-friendly interface for network exploration

## Requirements

- **Pixi** or **Conda/Mamba** for environment management
- **Snakemake** ≥ 7.0
- Required tools (automatically installed via conda environments):
  - snippy-ng
  - seqkit
  - coresnpfilter
  - distle
  - FastTree

## Installation

### Using Snk (Recommended)

1. Clone the repository:
```bash
snk install Wytamma/snippy-ng-cluster-pipeline
```

## Usage

### Input Data Formats

#### Option 1: CSV Sample List
Create a CSV file with sample information:
```csv
sample_name,R1_path,R2_path
ERR1102348,data/ERR1102348_1.fastq.gz,data/ERR1102348_2.fastq.gz
ERR1102353,data/ERR1102353_1.fastq.gz,data/ERR1102353_2.fastq.gz
GCA_assembly,data/assembly.fna,
```

#### Option 2: Directory Structure
Organize samples in directories:
```
samples/
├── ERR1102348/
│   ├── ERR1102348_1.fastq.gz
│   └── ERR1102348_2.fastq.gz
├── ERR1102353/
│   ├── ERR1102353_1.fastq.gz
│   └── ERR1102353_2.fastq.gz
└── assemblies/
    └── genome.fna
```

### Running the Pipeline

```
snippy-ng-cluster-pipeline run --cores 40 --snippy-threads 2 --samples samples_dir --reference Hospital_outbreak_reference.fa --metadata Hospital_outbreak_metadata.csv --core 0.9
```

## Outputs

### Main Results
- `results/tree.nwk` - Phylogenetic tree in Newick format
- `results/cluster_network.html` - Interactive network visualization
- `results/filter.aln` - Core SNP alignment
- `results/distance_matrix.phy` - Pairwise SNP distance matrix

## Pipeline Components

1. **Variant Calling** (`snippy-ng`)
   - Short reads: Maps reads and calls variants
   - Assemblies: Aligns contigs and identifies differences

2. **Sequence Processing** (`seqkit`)
   - Concatenates pseudo-genomes
   - Joins multi-contig sequences

3. **Core SNP Filtering** (`coresnpfilter`)
   - Removes sites with excessive missing data
   - Focuses on core genome positions

4. **Distance Calculation** (`distle`)
   - Computes pairwise SNP distances
   - Generates PHYLIP format matrix

6. **Visualization**
   - D3.js-powered network analysis
   - Force-directed layout with clustering

## Configuration Options

| Parameter | Default | Description |
|-----------|---------|-------------|
| `reference` | Required | Reference genome (FASTA) |
| `reads_list` | `sample_list.csv` | Sample CSV file |
| `samples` | - | Sample directory for auto-detection |
| `downsample` | 10 | Coverage downsampling factor |
| `core` | 0.9 | Core genome threshold (0-1) |
| `cluster_threshold` | 20 | Default clustering threshold |
| `metadata` | - | Optional metadata file |

