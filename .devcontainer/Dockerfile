FROM ubuntu:latest
WORKDIR /tmp
# We could consolidate these and improve provisioning performance
#   however for clarity if others try to find install commands I wrote distinct steps

# Need to apt update and upgrade anything not up to date in base image
RUN apt update && apt upgrade -y

# Install AWS CLI
RUN apt install curl zip -y
RUN case $(uname -m) in \
  x86_64) architecture="x86_64" ;; \
  aarch64)    architecture="aarch64" ;; \
  *)      exit 1;; \
esac; \
export DOWNLOAD_URL="https://awscli.amazonaws.com/awscli-exe-linux-${architecture}.zip"; \
echo AWS CLI:  ${DOWNLOAD_URL}; \
curl ${DOWNLOAD_URL} -o "awscliv2.zip"; \
unzip awscliv2.zip; \
./aws/install; \
rm /tmp/awscliv2.zip && rm -rf /tmp/aws

# Install kubectl
RUN case $(uname -m) in \
  x86_64) architecture="amd64" ;; \
  aarch64)    architecture="arm64" ;; \
  *)      exit 1;; \
esac; \
export DOWNLOAD_URL="https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/${architecture}/kubectl"; \
curl -LO ${DOWNLOAD_URL}
RUN install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl; \
kubectl version --client
RUN mkdir /etc/bash_completion.d; \
kubectl completion bash | tee /etc/bash_completion.d/kubectl > /dev/null; \
chmod a+r /etc/bash_completion.d/kubectl; \
rm /tmp/kubectl

# Install terraform cli
RUN apt install -y gnupg software-properties-common wget
RUN wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
RUN echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list
RUN apt update && apt install terraform -y

# EKS Blueprints for Terraform
RUN apt install -y jq gettext bash-completion moreutils

# Useful
RUN apt install git -y

# Make sure all is up to date
RUN apt update && apt upgrade -y
