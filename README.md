# Terraform-Azure-CLI Docker Image

Repository with the Dockerfile definition and a CircleCI workflow configuration for building a Docker image based on debian:stable-slim with this components:
   - Terraform 1.1.14
   - Azure CLI 2.32.0
   - Python 3.9

Circle CI reads the terraform-azure directory and build the image terraform-azure/Dockerfile and push it in this docker hub repository : "DOCKERHUB_USER/terraform-azure-cli:TAG"

The variables DOCKERHUB_USER and DOCKERHUB_PASSWORD need to be added in the project configuration in CircleCI.
The variable TAG is generated in the CircleCI workflow ( TAG=0.1.$CIRCLE_BUILD_NUM )