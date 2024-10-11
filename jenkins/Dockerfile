# Base image from jenkins/ssh-agent
FROM jenkins/ssh-agent:latest
LABEL authors="selim"

# Switch to root user to install packages
USER root

# Install curl, unzip and python (required for AWS CLI installation)
RUN apt-get update && apt-get install -y \
    curl \
    python3 \
    unzip && \
    rm -rf /var/lib/apt/lists/*

# Install AWS CLI v2 and remove the installation file
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf awscliv2.zip aws

# Verify AWS CLI installation
#RUN aws --version

# Switch back to jenkins user
# USER jenkins

# Dont use this command, it will not work with jenkins/ssh-agent
#ENTRYPOINT ["top", "-b"]