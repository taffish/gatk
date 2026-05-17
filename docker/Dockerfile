FROM broadinstitute/gatk:4.6.2.0

LABEL maintainer="taffish@163.com"

WORKDIR /gatk

RUN set -eux; \
    gatk --version 2>&1 | grep -F "4.6.2.0" >/dev/null; \
    gatk --list 2>&1 | grep -F "HaplotypeCaller" >/dev/null; \
    java -version; \
    python --version; \
    python3 --version; \
    R --version; \
    Rscript --version; \
    samtools --version >/dev/null; \
    bcftools --version >/dev/null; \
    bedtools --version; \
    tabix --help >/dev/null

ENV TAFFISH_ENV=TAFFISH
ENV TAFFISH_NAME=gatk
