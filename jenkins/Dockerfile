FROM jenkins/jenkins:lts

USER root

RUN apt-get update && \
    apt-get install -y \
    docker.io \
    git \
    curl \
    nodejs \
    npm && \
    apt-get clean

ARG DOCKER_GID=998
RUN groupmod -g ${DOCKER_GID} docker || groupadd -g ${DOCKER_GID} docker
RUN usermod -aG docker jenkins

USER jenkins
