# Build arguments
ARG AZURE_CLI_VERSION=2.32.0
ARG TERRAFORM_VERSION=1.1.4
ARG DEBIAN_VERSION=stable-slim
ARG PYTHON_MAJOR_VERSION=3.9

# Download Terraform binary
FROM debian:stable-slim as terraform-cli
ARG TERRAFORM_VERSION
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get install -y git
RUN apt-get install -y ssh
RUN apt-get install -y tar
RUN apt-get install -y gzip
RUN apt-get install -y ca-certificates
RUN apt-get install -y unzip
WORKDIR /workspace
RUN curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS
RUN curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip
RUN curl -Os https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_SHA256SUMS.sig
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN grep terraform_${TERRAFORM_VERSION}_linux_amd64.zip terraform_${TERRAFORM_VERSION}_SHA256SUMS | sha256sum -c -
RUN unzip -j terraform_${TERRAFORM_VERSION}_linux_amd64.zip

# Install az CLI using PIP
FROM debian:${DEBIAN_VERSION} as azure-cli
ARG AZURE_CLI_VERSION
ARG PYTHON_MAJOR_VERSION
RUN apt-get update
RUN apt-get install -y --no-install-recommends python3=${PYTHON_MAJOR_VERSION}.2-3
RUN apt-get install -y --no-install-recommends python3-pip=20.3.4-4
RUN pip3 install --no-cache-dir setuptools
RUN pip3 install --no-cache-dir azure-cli==${AZURE_CLI_VERSION}

# Build final image
FROM debian:${DEBIAN_VERSION}
LABEL com.circleci.preserve-entrypoint=true
ARG PYTHON_MAJOR_VERSION
RUN apt-get update \
  && apt-get install -y \
    ca-certificates\
    git \
    python3=${PYTHON_MAJOR_VERSION}.2-3 \
    python3-distutils=${PYTHON_MAJOR_VERSION}.2-1 \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*
WORKDIR /workspace
COPY --from=terraform-cli /workspace/terraform /usr/local/bin/terraform
COPY --from=azure-cli /usr/local/bin/az* /usr/local/bin/
COPY --from=azure-cli /usr/local/lib/python${PYTHON_MAJOR_VERSION}/dist-packages /usr/local/lib/python${PYTHON_MAJOR_VERSION}/dist-packages
COPY --from=azure-cli /usr/lib/python3/dist-packages /usr/lib/python3/dist-packages 

RUN groupadd --gid 1001 nonroot \
  # user needs a home folder to store azure credentials
  && useradd --gid nonroot --create-home --uid 1001 nonroot \
  && chown nonroot:nonroot /workspace
USER nonroot

ENTRYPOINT ["bash"]