# Genomics / Bioinformatics

Use for: sequence data processing, variant calling pipelines, genomic database queries, and
bioinformatics tooling in general.

## When to use

- Working with FASTA/FASTQ (sequences), BAM/SAM (alignments), or VCF (variants) files.
- Building or debugging a pipeline step (QC → alignment → variant calling → annotation).
- Querying genomic coordinate systems or cross-referencing gene/variant databases.

## Core concepts

- **Coordinate systems differ by convention.** BED files are 0-based, half-open; VCF/GFF/SAM
  are 1-based. A huge class of off-by-one bugs in genomics comes from converting between these
  without tracking which convention a given format uses.
- **Reference genome versions matter.** Coordinates are meaningless without knowing the
  reference build (e.g. GRCh37 vs. GRCh38) — never compare positions across pipelines that
  might use different builds without re-mapping (liftover) first.
- **File size reality.** Raw sequencing data and alignment files are commonly tens to hundreds
  of GB. Favor streaming/indexed access (`samtools`/`pysam` with `.bai`/`.tbi` indexes) over
  loading whole files into memory.

## Typical toolchain (Python)

- Sequence I/O and manipulation: `Biopython`
- Alignment/variant files: `pysam` (BAM/SAM/VCF, requires an index for random access)
- Variant-level analysis: standard tools are `bcftools`/`GATK` from the shell, called out via
  subprocess for the heavy lifting rather than reimplemented in Python
- Pathway/ontology lookups: dedicated ontology or pathway-enrichment APIs rather than
  hand-rolled gene-name matching (gene symbols are notoriously ambiguous/aliased)

## Workflow

1. Confirm the reference genome build and coordinate convention of every input file before
   writing comparison logic.
2. Use indexed, streaming access for anything beyond toy-sized files — don't read a multi-GB
   BAM into memory.
3. Validate a pipeline step on a small known-truth subset (e.g. a well-characterized sample)
   before running it at scale.
4. When reporting variants or genes, always include the reference build and, for variants, the
   normalized (left-aligned, parsimonious) representation — the same variant can be written
   multiple equivalent ways in VCF.

## Pitfalls

- Treating gene symbols as stable identifiers — use a stable ID (Ensembl/Entrez) internally and
  only surface the symbol for display.
- Ignoring strand orientation when extracting sequence by coordinate.
- Silent sample mix-ups from filename-based sample tracking instead of explicit sample IDs
  carried through every pipeline step.

## Quick API reference (Biopython / pysam)

```python
from Bio import SeqIO
for record in SeqIO.parse("sequences.fasta", "fasta"):
    print(record.id, len(record.seq))

import pysam
# BAM/VCF access requires an index (.bai/.tbi) for random access — don't load whole files
bam = pysam.AlignmentFile("sample.bam", "rb")
for read in bam.fetch("chr1", 1000, 2000):
    pass

vcf = pysam.VariantFile("variants.vcf.gz")
for rec in vcf.fetch("chr1", 1000, 2000):
    print(rec.chrom, rec.pos, rec.ref, rec.alts)   # VCF is 1-based
```

## Escalating beyond this file

This covers coordinate/build/sample-tracking judgment calls, not full pipeline orchestration
(aligner/variant-caller invocation, pathway/ontology lookups, or any vendor-specific sequence
model API). If a dedicated genomics/bioinformatics skill is installed and available — including
any sequence-model API with its own task catalogue and auth — defer to it for that work rather
than improvising against undocumented endpoints; see `escalation-and-scope.md`.
