taf-gatk 4.6.2.0-r1

TAFFISH wrapper for GATK, the Genome Analysis Toolkit 4 from the Broad
Institute.

Usage:
  taf-gatk [TAF-APP-OPTION]
  taf-gatk [IN-CONTAINER-CMD] [ARGS...]
  taf-gatk [GATK-OPTION] [GATK-ARGS...]

TAF app options:
  -h, --help       Show this help text
  -v, --version    Show package and command version
  --compile        Print generated shell code instead of running it
  --               Stop parsing TAFFISH wrapper options

Upstream option calls:
  taf-gatk -- --version
  taf-gatk -- --help
  taf-gatk -- --list

Recommended GATK calls:
  taf-gatk gatk --version
  taf-gatk gatk --list
  taf-gatk gatk HaplotypeCaller --help
  taf-gatk gatk Mutect2 --help
  taf-gatk gatk CreateSequenceDictionary -R ref.fa -O ref.dict
  taf-gatk gatk HaplotypeCaller -R ref.fa -I sample.bam -O sample.g.vcf.gz -ERC GVCF
  taf-gatk gatk GenotypeGVCFs -R ref.fa -V cohort.g.vcf.gz -O cohort.vcf.gz
  taf-gatk gatk Mutect2 -R ref.fa -I tumor.bam -I normal.bam -normal normal -O somatic.vcf.gz
  taf-gatk gatk FilterMutectCalls -R ref.fa -V somatic.vcf.gz -O somatic.filtered.vcf.gz

JVM options:
  taf-gatk gatk --java-options "-Xmx8g" HaplotypeCaller -R ref.fa -I sample.bam -O out.g.vcf.gz -ERC GVCF

Local Spark example:
  taf-gatk gatk PrintReadsSpark -I input.bam -O output.bam -- --spark-runner LOCAL --spark-master 'local[4]'

Included upstream/container commands:
  gatk       Main GATK launcher
  java       Java runtime used by GATK
  python     Python runtime from the official GATK conda environment
  R, Rscript R runtime used by selected plotting paths
  samtools   Included in Broad's official GATK Docker runtime
  bcftools   Included in Broad's official GATK Docker runtime
  bedtools   Included in Broad's official GATK Docker runtime
  tabix      Included in Broad's official GATK Docker runtime

Notes:
  - This command runs GATK inside the TAFFISH container image.
  - The clearest command-mode form is taf-gatk gatk ToolName ...
  - GATK tool names such as HaplotypeCaller, Mutect2, GenotypeGVCFs, and
    CreateSequenceDictionary are subcommands of gatk, not standalone
    executables. Use taf-gatk gatk HaplotypeCaller ..., not taf-gatk
    HaplotypeCaller ...
  - taf-gatk --help and taf-gatk --version are handled by the TAFFISH command
    wrapper. Use taf-gatk gatk --version or taf-gatk -- --version for the
    upstream GATK version.
  - Use taf-gatk gatk --list to see the upstream tool list.
  - Use taf-gatk gatk ToolName --help for complete upstream tool options.
  - Inputs and outputs should be accessible from the current working directory
    or from mounted user paths.
  - Most real analyses require reference FASTA indexes, sequence dictionaries,
    BAM indexes, known-sites resources, germline resources, panel-of-normals,
    or other external reference data. This app does not bundle those datasets.
  - Cloud storage, external Spark clusters, Dataproc, and Google authentication
    paths are upstream GATK features but are not covered by the local smoke
    tests.
  - The official Broad image is large because it includes Java, the GATK jar,
    the GATK conda environment, Python/R packages, and common genomics helper
    tools. This TAFFISH app keeps that official runtime intact.

Platform:
  native platform: linux/amd64
  Docker arm64 host compatibility:
    TAFFISH_CONTAINER_BACKEND=docker TAFFISH_DOCKER_RUN_ARGS="--platform linux/amd64" taf-gatk gatk --version
  Podman arm64 host compatibility:
    TAFFISH_CONTAINER_BACKEND=podman TAFFISH_PODMAN_RUN_ARGS="--platform linux/amd64" taf-gatk gatk --version

Container:
  image: ghcr.io/taffish/gatk:4.6.2.0-r1
  upstream base: broadinstitute/gatk:4.6.2.0
  supported backends: apptainer, podman, docker

Upstream:
  project: GATK
  source:  https://github.com/broadinstitute/gatk
  docs:    https://gatk.broadinstitute.org/
  docker:  https://hub.docker.com/r/broadinstitute/gatk
  release: https://github.com/broadinstitute/gatk/releases/tag/4.6.2.0
  license: Apache-2.0
  citation: McKenna et al. 2010
  doi: 10.1101/gr.107524.110
  pmid: 20644199
