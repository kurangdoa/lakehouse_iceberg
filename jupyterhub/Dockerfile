FROM jupyter/pyspark-notebook:latest
# ARG openjdk_version=11
# ARG spark_version=3.5.1
# ARG hadoop_version=3.3.4
# Replace `latest` with an image tag from to ensure reproducible builds:
# https://hub.docker.com/r/jupyter/minimal-notebook/tags/
# Inspect the Dockerfile at:
# https://github.com/jupyter/docker-stacks/tree/HEAD/minimal-notebook/Dockerfile
USER root
# Install java
RUN sudo apt-get update
RUN sudo apt-get install sudo
RUN sudo apt-get install -y openjdk-11-jdk

USER 1000
# install additional package...
ADD requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# set the default command of the image,
# if you want to launch more complex startup than the default `juptyerhub-singleuser`.
# To launch an image's custom CMD instead of the default `jupyterhub-singleuser`
# set `singleuser.cmd: null` in your config.yaml.