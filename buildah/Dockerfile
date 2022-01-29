FROM centos:latest
LABEL com.circleci.preserve-entrypoint=true

RUN yum update -y \
  && yum install -y \
  podman \
  buildah \
  git \
  tar \
  gzip \
  ca-certificates \
  && yum clean all

ENTRYPOINT ["/bin/bash"]