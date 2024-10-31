# IconServer Deployment Automation

This project automates the deployment of the [IconServer](https://hub.docker.com/r/matthiasluedtke/iconserver) service using Docker Compose and Ansible.

## Description

The project configures containers for IconServer using Docker Compose, while the server auto-configuration is handled by Ansible. The service responds on HTTPS port 443 using self-signed certificates and will be available at `https://IP_ADDRESS:443`

## Compatibility

The project has been tested with **ansible-core==2.13.9** and on the following operating systems:

- Ubuntu 22.04
- Debian 12
- AlmaLinux 8.10
- CentOS 9

## Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/mtaktashov/iconserver.git
   cd iconserver/
   ```
2. **Install Ansible and the necessary collections:**
    ```bash
    pip install ansible-core==2.13.9
    ansible-galaxy collection install -r requirements.yaml
    ```
3. **Configure the inventory:**
    Edit the *inventory/hosts.ini* file, adding your target hosts. Example content:
    ```ini
    [all]
    your_host ansible_host=192.168.1.10 ansible_user=your_user
    ```
4. **(Optinal) Generate self-signed certificates:**
    ```bash
    openssl genpkey -algorithm RSA -out roles/app-install/files/nginx/certs/server.key -pkeyopt rsa_keygen_bits:2048
    openssl req -new -key roles/app-install/files/nginx/certs/server.key -out roles/app-install/files/nginx/certs/server.csr
    openssl x509 -req -days 365 -in roles/app-install/files/nginx/certs/server.csr -signkey roles/app-install/files/nginx/certs/server.key -out roles/app-install/files/nginx/certs/server.crt
    ```
5. **Run the Ansible Playbook:**
    ```bash
    ansible-playbook -i inventory/hosts.ini --private-key ~/.ssh/id_rsa -D playbook.yaml

    # If Docker is already installed, you can skip this step
    ansible-playbook -i inventory/hosts.ini --private-key ~/.ssh/id_rsa --skip-tags docker -D playbook.yaml
    ```