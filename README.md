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

### Using Pixi (Recommended)

1. Clone the repository:
```bash
git clone <repository-url>
cd snippy-phylo-pipeline
```

2. Install dependencies:
```bash
pixi install
```

3. Activate environment:
```bash
pixi shell
```

### Using Conda/Mamba

```bash
# Install snakemake
conda install -c bioconda snakemake

# Clone repository
git clone <repository-url>
cd snippy-phylo-pipeline
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

### Configuration

Create or modify `snk.yaml`:

```yaml
# Reference genome
reference: "data/reference.fa"

# Input method 1: CSV file
reads_list: "samples.csv"

# Input method 2: Directory scanning
# samples: "data/samples"

# Optional parameters
downsample: 10                # Coverage downsampling
core: 0.9                    # Core genome threshold (90%)
cluster_threshold: 20        # SNP distance threshold for clustering
metadata: "metadata.csv"    # Optional metadata for visualization
```

### Running the Pipeline

#### Complete Analysis
```bash
snakemake --configfile snk.yaml --use-conda --cores 8
```

#### Specific Targets
```bash
# Generate phylogenetic tree only
snakemake --configfile snk.yaml --use-conda results/tree.nwk

# Generate network visualization only
snakemake --configfile snk.yaml --use-conda results/cluster_network.html

# Process specific sample
snakemake --configfile snk.yaml --use-conda results/snippy/SAMPLE_NAME/snps.pseudo.fna
```

#### Dry Run
```bash
snakemake --configfile snk.yaml --use-conda --dry-run
```

## Outputs

### Main Results
- `results/tree.nwk` - Phylogenetic tree in Newick format
- `results/cluster_network.html` - Interactive network visualization
- `results/filter.aln` - Core SNP alignment
- `results/distance_matrix.phy` - Pairwise SNP distance matrix

### Per-Sample Results
- `results/snippy/{sample}/` - Individual snippy outputs
  - `snps.pseudo.fna` - Pseudo-genome sequence
  - `snps.vcf` - Variant calls
  - `snps.bam` - Read alignments
  - `snps.stats.tsv` - Summary statistics

## Interactive Network Visualization

The `cluster_network.html` provides:

- **Dynamic Clustering**: Adjust SNP distance threshold to form clusters
- **Metadata Integration**: Color and shape nodes by sample attributes
- **Responsive Design**: Works on desktop and mobile devices
- **Boundary Constraints**: Nodes stay within viewport bounds
- **Drag & Drop**: Upload metadata files directly in browser

### Metadata Format
Optional CSV/TSV with sample attributes:
```csv
id,Region,Host,Collection_Date
ERR1102348,Europe,Human,2020-01-15
ERR1102353,Asia,Bovine,2020-02-20
GCA_assembly,Americas,Environmental,2020-03-10
```

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

5. **Tree Building** (`FastTree`)
   - Maximum-likelihood phylogeny
   - GTR nucleotide substitution model

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

## Troubleshooting

### Common Issues

1. **Sample Detection Problems**
   - Ensure FASTQ files follow naming conventions (`*_1.fastq.gz`, `*_2.fastq.gz`)
   - Check CSV format matches expected columns
   - Verify file paths are correct

2. **Memory Issues**
   - Reduce `--cores` parameter
   - Check available system memory
   - Consider downsampling coverage

3. **Missing Dependencies**
   - Ensure conda environments are properly created
   - Check internet connection for package downloads

### Debug Mode
Enable debug output:
```bash
snakemake --configfile snk.yaml --use-conda --verbose
```

## Citation

If you use this pipeline, please cite:

- **Snippy-NG**: [Citation needed]
- **Snakemake**: Köster, Johannes and Rahmann, Sven. "Snakemake - A scalable bioinformatics workflow engine". Bioinformatics 2012.
- **FastTree**: Price, M.N., Dehal, P.S., and Arkin, A.P. (2010) FastTree 2 -- Approximately Maximum-Likelihood Trees for Large Alignments. PLoS ONE, 5(3):e9490.

## License

[Specify license here]

## Contributing

Contributions welcome! Please submit issues and pull requests on the project repository.

## Support

For questions and support:
- Create an issue on the GitHub repository
- Check the documentation and troubleshooting guide
- Review Snakemake documentation for workflow questions
