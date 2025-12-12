# Cisco FMC Ansible Execution Environment

This repository contains the configuration files to build an Ansible Execution Environment (EE) for managing Cisco Firepower Management Center (FMC) devices.

## Overview

This Execution Environment includes:
- Cisco FMC Ansible Collection (`cisco.fmcansible`)
- Required networking collections (`ansible.netcommon`, `ansible.utils`)
- All necessary Python dependencies
- System-level dependencies

## Prerequisites

- [ansible-builder](https://github.com/ansible/ansible-builder) (v3.0.0 or higher recommended)
- [Podman](https://podman.io/) or [Docker](https://www.docker.com/)

## Installation

Install ansible-builder:

```bash
pip install ansible-builder
```

## Building the Execution Environment

To build the execution environment image:

```bash
ansible-builder build --tag cisco-fmc-ee:latest
```

### Build Options

You can customize the build with additional options:

```bash
# Build with a specific tag
ansible-builder build --tag cisco-fmc-ee:1.0.0

# Build with verbose output
ansible-builder build --tag cisco-fmc-ee:latest --verbosity 3

# Build using Docker instead of Podman
ansible-builder build --tag cisco-fmc-ee:latest --container-runtime docker
```

## Using the Execution Environment

### With ansible-navigator

Create an `ansible-navigator.yml` configuration file:

```yaml
---
ansible-navigator:
  execution-environment:
    image: cisco-fmc-ee:latest
    pull:
      policy: missing
  playbook-artifact:
    enable: false
```

Then run your playbooks:

```bash
ansible-navigator run playbook.yml
```

### With ansible-runner

```bash
ansible-runner run /path/to/project \
  --container-image cisco-fmc-ee:latest \
  --playbook playbook.yml
```

### With AWX/Automation Controller

1. Push the built image to a container registry:
   ```bash
   podman tag cisco-fmc-ee:latest registry.example.com/cisco-fmc-ee:latest
   podman push registry.example.com/cisco-fmc-ee:latest
   ```

2. In AWX/Automation Controller, create a new Execution Environment pointing to your image URL

## Included Collections

- **cisco.fmcansible** (>=0.2.2): Cisco Firepower Management Center collection
- **ansible.netcommon** (>=5.0.0): Common networking utilities
- **ansible.utils** (>=2.0.0): Utility plugins for Ansible

## Included Python Packages

- ansible-core (>=2.16.13)
- requests (>=2.28.0)
- jinja2 (>=3.1.0)

## Customization

You can customize this execution environment by modifying:

- `execution-environment.yml`: Main EE configuration
- `requirements.yml`: Ansible collections to include
- `requirements.txt`: Python packages to install
- `bindep.txt`: System-level dependencies

After making changes, rebuild the image using `ansible-builder build`.

## Example Playbook

Here's a simple example of using the Cisco FMC collection:

```yaml
---
- name: Manage Cisco FMC
  hosts: localhost
  connection: local
  collections:
    - cisco.fmcansible
  vars:
    fmc_host: "192.168.1.100"
    fmc_username: "admin"
    fmc_password: "{{ lookup('env', 'FMC_PASSWORD') }}"
  
  tasks:
    - name: Get network objects
      cisco.fmcansible.fmc_configuration:
        operation: getAllNetworkObject
        fmc_host: "{{ fmc_host }}"
        fmc_username: "{{ fmc_username }}"
        fmc_password: "{{ fmc_password }}"
        register_as: network_objects
```

## Contributing

Feel free to submit issues or pull requests for improvements.

## License

This project is provided as-is for building Ansible Execution Environments for Cisco FMC management.

## References

- [Cisco FMC Ansible Collection](https://galaxy.ansible.com/cisco/fmcansible)
- [Ansible Builder Documentation](https://ansible-builder.readthedocs.io/)
- [Ansible Execution Environments](https://docs.ansible.com/automation-controller/latest/html/userguide/execution_environments.html)