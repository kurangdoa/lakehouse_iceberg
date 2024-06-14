FROM python:3.11.9-slim-bullseye

ARG openjdk_version="11"

RUN echo "root:root" | chpasswd 
RUN useradd -ms /bin/bash  datasaku
RUN echo "datasaku:pass" | chpasswd

# part of java instalation taken from 
# jupyter installation

RUN apt-get update --yes && \
    apt-get install -y software-properties-common && \
    add-apt-repository ppa:ubuntugis/ppa && \
    apt-get install --yes --no-install-recommends \
        sudo \
        wget \
        curl \
        vim \
        unzip \
        rsync \
        openjdk-11-jdk \
        build-essential \
        software-properties-common \
        ssh \
        git \
        libgdal-dev \
        gdal-bin \
        ca-certificates-java && \
    apt-get clean && rm -rf /var/lib/apt/lists/*

# install vs-code extension
RUN curl -fsSL https://code-server.dev/install.sh | /bin/sh


# define spark and hadoop versions
ENV SPARK_VERSION=3.5.1
ENV HADOOP_VERSION=3.3.4
ENV AWS_SDK_VERSION=1.12.262

ENV SPARK_HOME=/opt/spark
ENV PATH="${PATH}:${SPARK_HOME}/bin"
ENV SPARK_OPTS="--driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info"

# download and install hadoop
# RUN mkdir -p /opt && \
#     cd /opt && \
#     curl -L http://archive.apache.org/dist/hadoop/common/hadoop-${HADOOP_VERSION}/hadoop-${HADOOP_VERSION}.tar.gz | \
#         tar -zx hadoop-${HADOOP_VERSION}/lib/native && \
#     ln -s hadoop-${HADOOP_VERSION} hadoop && \
#     echo Hadoop ${HADOOP_VERSION} native libraries installed in /opt/hadoop/lib/native

# download and install spark
RUN mkdir -p /opt && \
    cd /opt && \
    curl -L http://archive.apache.org/dist/spark/spark-${SPARK_VERSION}/spark-${SPARK_VERSION}-bin-hadoop3.tgz | \
        tar -zx && \
    ln -s spark-${SPARK_VERSION}-bin-hadoop3 spark && \
    echo Spark ${SPARK_VERSION} installed in /opt


USER datasaku
ENV HOME_DATASAKU=/home/datasaku
ENV SPARK_HOME=/opt/spark
WORKDIR ${HOME_DATASAKU}

# download and install hadoop-aws
RUN wget https://repo1.maven.org/maven2/org/apache/hadoop/hadoop-aws/${HADOOP_VERSION}/hadoop-aws-${HADOOP_VERSION}.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/com/amazonaws/aws-java-sdk-bundle/${AWS_SDK_VERSION}/aws-java-sdk-bundle-${AWS_SDK_VERSION}.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/org/apache/iceberg/iceberg-spark-runtime-3.5_2.12/1.5.0/iceberg-spark-runtime-3.5_2.12-1.5.0.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo1.maven.org/maven2/org/postgresql/postgresql/42.6.2/postgresql-42.6.2.jar -P ${SPARK_HOME}/jars/
RUN wget https://repo.maven.apache.org/maven2/org/projectnessie/nessie-integrations/nessie-spark-extensions-3.5_2.12/0.82.0/nessie-spark-extensions-3.5_2.12-0.82.0.jar -P ${SPARK_HOME}/jars


# add scripts and update spark default config
# ADD common.sh spark-master spark-worker /
ADD spark-defaults.conf ${SPARK_HOME}/conf/spark-defaults.conf
ENV PATH $PATH:${SPARK_HOME}/bin

# copy requirement
ADD requirements.txt .
RUN python -m venv ${HOME_DATASAKU}/.venv
RUN ${HOME_DATASAKU}/.venv/bin/pip install -r requirements.txt
RUN mkdir ${HOME_DATASAKU}/.vscode-server

RUN code-server --extensions-dir ~/.vscode-server/extensions \ 
                --install-extension ms-python.python \
                --install-extension ms-toolsai.jupyter

EXPOSE 4040