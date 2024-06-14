# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG REGISTRY=quay.io
ARG OWNER=jupyter
ARG BASE_CONTAINER=$REGISTRY/$OWNER/scipy-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Fix: https://github.com/hadolint/hadolint/wiki/DL4006
# Fix: https://github.com/koalaman/shellcheck/wiki/SC3014
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

USER root

# Spark dependencies
# Default values can be overridden at build time
# (ARGS are in lowercase to distinguish them from ENV)
ARG openjdk_version="17"

RUN apt-get update --yes && \
    apt-get install --yes --no-install-recommends \
    "openjdk-${openjdk_version}-jre-headless" \
    ca-certificates-java && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# If spark_version is not set, latest stable Spark will be installed
ARG spark_version
ARG hadoop_version="3"
# If scala_version is not set, Spark without Scala will be installed
ARG scala_version
# URL to use for Spark downloads
# You need to use https://archive.apache.org/dist/spark/ website if you want to download old Spark versions
# But it seems to be slower, that's why we use the recommended site for download
ARG spark_download_url="https://dlcdn.apache.org/spark/"

# ENV SPARK_HOME=/usr/local/spark
ENV SPARK_HOME=/opt/spark
ENV PATH="${PATH}:${SPARK_HOME}/bin"
ENV SPARK_OPTS="--driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info"

COPY setup_spark.py /opt/setup-scripts/

# Setup Spark
RUN /opt/setup-scripts/setup_spark.py \
    --spark-version="${spark_version}" \
    --hadoop-version="${hadoop_version}" \
    --scala-version="${scala_version}" \
    --spark-download-url="${spark_download_url}"

# Configure IPython system-wide
COPY ipython_kernel_config.py "/etc/ipython/"
RUN fix-permissions "/etc/ipython/"

USER ${NB_UID}

# additional

# create folder
RUN mkdir ${HOME}/datasaku
RUN chown -R ${NB_UID} ${HOME}/datasaku
RUN chmod -R 777 ${HOME}/datasaku
USER root
RUN chown -R ${NB_UID} ${SPARK_HOME}
RUN chmod -R 777 ${SPARK_HOME}
USER ${NB_UID}

ENV SPARK_VERSION=3.5.1
ENV HADOOP_VERSION=3.3.4
ENV AWS_SDK_VERSION=1.12.262

RUN wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/${HADOOP_VERSION}/hadoop-aws-${HADOOP_VERSION}.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/${AWS_SDK_VERSION}/aws-java-sdk-bundle-${AWS_SDK_VERSION}.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-3.5_2.12/1.5.0/iceberg-spark-runtime-3.5_2.12-1.5.0.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/org/postgresql/postgresql/42.6.2/postgresql-42.6.2.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo.maven.apache.org/maven2/org/projectnessie/nessie-integrations/nessie-spark-extensions-3.5_2.12/0.82.0/nessie-spark-extensions-3.5_2.12-0.82.0.jar -P ${SPARK_HOME}/jars
ADD spark-defaults.conf ${SPARK_HOME}/conf/spark-defaults.conf

# Install pyarrow
# NOTE: It's important to ensure compatibility between Pandas versions.
# The pandas version in this Dockerfile should match the version
# on which the Pandas API for Spark is built.
# To find the right version:
# 1. Check out the Spark branch you are on: <https://github.com/apache/spark>
# 2. Find the pandas version in the file `dev/infra/Dockerfile`.
RUN mamba install --yes \
    'grpcio-status' \
    'grpcio' \
    'pandas=2.0.3' \
    'pyarrow' && \
    mamba clean --all -f -y && \
    fix-permissions "${CONDA_DIR}" && \
    fix-permissions "/home/${NB_USER}"

ADD requirements.txt .
RUN pip install -r requirements.txt

USER ${NB_UID}

WORKDIR "${HOME}"
EXPOSE 4040

