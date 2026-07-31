FROM jenkins/jenkins:lts

USER root

# Install required packages
RUN apt-get update && \
    apt-get install -y \
    docker.io \
    git \
    curl \
    nodejs \
    npm && \
    apt-get clean

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/v1.31.0/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl

# Create a group with the same GID as the host Docker socket
# and add the Jenkins user to it.
RUN groupadd -g 971 docker-host || true && \
    usermod -aG docker-host jenkins

USER jenkins
