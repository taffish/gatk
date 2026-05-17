# taf-gatk

TAFFISH wrapper for [GATK](https://gatk.broadinstitute.org/), the Genome
Analysis Toolkit 4 from the Broad Institute.

This repository packages upstream GATK 4.6.2.0 as a TAFFISH tool app. It uses
the official `broadinstitute/gatk:4.6.2.0` Docker image as the runtime base and
exposes the upstream `gatk` launcher through the versioned `taf-gatk` command.

## Installation

Install from the public TAFFISH Hub index:

```sh
taf update
taf install gatk
```

Install the exact release:

```sh
taf install gatk 4.6.2.0-r1
```

For local testing before the app is published to the public index:

```sh
taf install --from .
```

## Usage

Show TAFFISH app help:

```sh
taf-gatk --help
```

Show the TAFFISH package version:

```sh
taf-gatk --version
```

Show the upstream GATK version:

```sh
taf-gatk gatk --version
taf-gatk -- --version
```

List upstream GATK tools:

```sh
taf-gatk gatk --list
taf-gatk -- --list
```

Show help for an upstream GATK tool:

```sh
taf-gatk gatk HaplotypeCaller --help
taf-gatk gatk Mutect2 --help
```

Run a common germline variant-calling step:

```sh
taf-gatk gatk HaplotypeCaller \
  -R ref.fa \
  -I sample.bam \
  -O sample.g.vcf.gz \
  -ERC GVCF
```

Create a sequence dictionary:

```sh
taf-gatk gatk CreateSequenceDictionary -R ref.fa -O ref.dict
```

Index a VCF:

```sh
taf-gatk gatk IndexFeatureFile -I calls.vcf.gz
```

Pass JVM options to GATK:

```sh
taf-gatk gatk --java-options "-Xmx8g" HaplotypeCaller \
  -R ref.fa \
  -I sample.bam \
  -O sample.g.vcf.gz \
  -ERC GVCF
```

Run a local Spark tool:

```sh
taf-gatk gatk PrintReadsSpark \
  -I input.bam \
  -O output.bam \
  -- \
  --spark-runner LOCAL \
  --spark-master 'local[4]'
```

Because this is a command-mode TAFFISH tool, the first non-option argument is
the in-container command. GATK tool names such as `HaplotypeCaller`, `Mutect2`,
and `GenotypeGVCFs` are subcommands of the upstream `gatk` launcher, not
standalone executables. The clearest and recommended form is therefore:

```sh
taf-gatk gatk HaplotypeCaller ...
taf-gatk gatk Mutect2 ...
taf-gatk gatk GenotypeGVCFs ...
```

The `--` separator is useful for option-leading arguments sent to the default
`gatk` command:

```sh
taf-gatk -- --help
taf-gatk -- --version
taf-gatk -- --list
```

The official image also exposes helper executables that Broad includes in the
GATK Docker runtime:

```sh
taf-gatk samtools --version
taf-gatk bcftools --version
taf-gatk bedtools --version
taf-gatk tabix --help
```

For normal TAFFISH workflows, prefer the dedicated TAFFISH apps for those tools
when they are separate workflow steps. They are kept inside this image because
they are part of Broad's official GATK Docker runtime and are useful for GATK
adjacent operations.

This README lists common usage patterns, not the full upstream manual. Use
upstream help for the complete tool list and option surface:

```sh
taf-gatk gatk --list
taf-gatk gatk ToolName --help
```

## Package

```text
name: gatk
command: taf-gatk
version: 4.6.2.0-r1
kind: tool
image: ghcr.io/taffish/gatk:4.6.2.0-r1
```

## Container

The container image is built from `docker/Dockerfile` using the official
`broadinstitute/gatk:4.6.2.0` image as the base.

This app intentionally keeps the official GATK runtime instead of rebuilding a
minimal Java-only image. GATK 4.6.2.0 requires Java 17, uses Python for the
`gatk` frontend and Python-based tools, and includes a Broad-maintained conda
environment with Python and R packages used by selected GATK tools and plotting
paths. The official image also includes `samtools`, `bcftools`, `bedtools`, and
`tabix`.

Those bundled tools make the image large, but removing them would create a
lighter image with a less faithful GATK command surface. A future `gatk-lite`
or workflow-specific app could be useful for a narrow Java-only subset, but
this `gatk` package is intended to track the official upstream Docker runtime.

The official upstream Docker tag is a single-architecture image. This TAFFISH
release therefore declares native support for:

```text
linux/amd64
```

On Apple Silicon or other arm64 hosts, Docker or Podman may still run this
image through amd64 emulation. That is a compatibility mode, not native arm64
support:

```sh
TAFFISH_CONTAINER_BACKEND=docker \
TAFFISH_DOCKER_RUN_ARGS="--platform linux/amd64" \
taf-gatk gatk --version
```

```sh
TAFFISH_CONTAINER_BACKEND=podman \
TAFFISH_PODMAN_RUN_ARGS="--platform linux/amd64" \
taf-gatk gatk --version
```

Apptainer behavior depends on the host and available architecture support.

The TAFFISH metadata declares a Docker smoke check:

```text
exist: gatk, java, python, python3, R, Rscript, samtools, bcftools, bedtools, tabix
test:  gatk reports upstream version 4.6.2.0
test:  gatk --list includes representative tools such as HaplotypeCaller and Mutect2
test:  HaplotypeCaller help is available
test:  CreateSequenceDictionary works on a tiny reference FASTA
test:  IndexFeatureFile works on a tiny VCF
test:  PrintReads can round-trip a tiny synthetic BAM
test:  HaplotypeCaller can run on a tiny synthetic BAM and emit VCF
```

These smoke checks verify the container runtime and representative local GATK
paths. They do not download reference bundles, validate Best Practices
scientific output, run cloud authentication paths, or exercise external Spark,
Dataproc, or large cohort workflows.

Each smoke command is self-contained because the public index runs every
`[smoke].test` entry in a fresh temporary container. No smoke entry depends on
files created by an earlier entry.

## Upstream

- Source: <https://github.com/broadinstitute/gatk>
- Documentation: <https://gatk.broadinstitute.org/>
- Docker image: <https://hub.docker.com/r/broadinstitute/gatk>
- Release: <https://github.com/broadinstitute/gatk/releases/tag/4.6.2.0>
- Upstream license: Apache-2.0
- Citation: McKenna et al. 2010
- DOI: `10.1101/gr.107524.110`
- PMID: `20644199`
