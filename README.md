# Ansible Execution Environment (EE) for cisco.fmc

This repository contains the minimal files required to build an Ansible Execution Environment (EE)
that includes the cisco.fmc Ansible collection using ansible-builder.

Files added:
- execution-environment.yml : EE build definition
- requirements.yml : Ansible Galaxy collections to install (cisco.fmc)
- requirements.txt : Python packages to install into the EE (optional)
- bindep.txt : System packages to install during build (optional)
- .github/workflows/build-and-push.yml : GitHub Actions workflow to build and push to Docker Hub

Build locally
-------------
Prerequisites:
- ansible-builder (pip install ansible-builder)
- podman or docker

Build the EE image:

ansible-builder build -t docker.io/lutpiero/cisco-fmc-ee:latest --container-engine docker

Push to Docker Hub (after docker login):

docker push docker.io/lutpiero/cisco-fmc-ee:latest

GitHub Actions CI
-----------------
The included workflow builds the EE and pushes it to Docker Hub. It expects the following repository secrets to exist:
- DOCKERHUB_USERNAME: Docker Hub username (should be 'lutpiero')
- DOCKERHUB_TOKEN: Docker Hub access token or password

The workflow is triggered on pushes to main and on manual dispatch.

Notes
-----
- Inspect the cisco.fmc collection page on Ansible Galaxy (https://galaxy.ansible.com/cisco/fmc)
  for collection-specific Python or system package requirements and add them to requirements.txt / bindep.txt before building.
- You may wish to pin the collection version in requirements.yml for reproducible builds.