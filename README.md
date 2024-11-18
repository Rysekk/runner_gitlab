# GitLab Runner in Docker with Ansible

This project automates the deployment of a GitLab Runner container using Docker and Ansible on a home server. The configuration ensures that the GitLab Runner is correctly installed, registered, and ready to execute CI/CD pipelines.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Project Structure](#project-structure)
3. [Setup Instructions](#setup-instructions)
4. [Customizing the Deployment](#customizing-the-deployment)
5. [Usage](#usage)
6. [Troubleshooting](#troubleshooting)

---

## Prerequisites

- A home server accessible via SSH with:
  - Docker installed
  - Ansible installed on the control machine
- SSH key-based authentication or Ansible Vault for managing passwords
- GitLab account and a registration token for the runner

---

## Project Structure

```plaintext
.
├── ansible.cfg                # Ansible configuration file
├── inventory
│   ├── group_vars
│   │   └── all.yml            # Variables shared across hosts
│   └── hosts.yml              # Inventory file defining hosts
├── playbooks
│   └── install_gitlab_runner.yml # Main playbook for deployment
├── roles
│   └── gitlab_runner
│       ├── files
│       │   └── runner_docker-compose.yml # Docker Compose file for GitLab Runner
│       ├── tasks
│       │   └── main.yml       # Tasks for installing and configuring GitLab Runner
│       ├── templates
│       │   └── config.toml.j2 # Template for GitLab Runner configuration
│       └── vars
│           └── main.yml       # Role-specific variables
├── vars
│   └── vault.yml              # Encrypted sensitive variables (e.g., registration token)
└── README.md                  # Project documentation
```
## Setup Instructions

### 1. Clone the Repository
Clone the repository to your local machine:
```bash
git clone https://github.com/your-username/gitlab-runner-ansible.git
cd gitlab-runner-ansible
ansible-vault create vars/vault.yml
```
### 2. Configure Inventory
Edit the ```inventory/hosts.yml``` file to define your home server:
```yml
all:
  hosts:
    homeserver:
      ansible_host: <your_server_ip>
      ansible_user: <your_username>
```
### 3. Add Secrets to Vault
ansible-vault create vars/vault.yml
```bash
ansible-vault create vars/vault.yml
```
Inside the ```vault.yml``` file, add the following content:
```yml
gitlab_runner_registration_token: "your-gitlab-token"
gitlab_runner_coordinator_url: "https://gitlab.com/"
```
### 4. Deploy the Runner
```bash
ansible-playbook -i inventory/hosts.yml playbooks/install_gitlab_runner.yml \
  --extra-vars "@vars/vault.yml" \
  --private-key ~/.ssh/your_private_key
```

## Customizing the Deployment

### Change Docker Compose Configuration
Modify the ```roles/gitlab_runner/files/runner_docker-compose.yml``` file to customize the Docker Compose configuration.

### Customize Runner Tags
Update ```roles/gitlab_runner/vars/main.yml``` to set tags or executor type:
```yml
gitlab_runner_tags: "docker,home"
gitlab_runner_executor: "docker"
```

## Usage

- **Start Runner**: The runner starts automatically after deployment.
- **Check Runner Status**:
    ```bash
    docker ps --filter "name=gitlab-runner"
    ```
- **Update Configuration**: Modify ```config.toml``` in ```/srv/gitlab-runner/config```

## Troubleshooting

### Common Issues

1. **Missing Docker or Docker Compose**:
    - Ensure Docker and Docker Compose are installed using the playbook's pre-check tasks.
2. **Permission Denied Errors**:
    - Ensure the user running the playbook has sudo access.

### Debugging Playbook
Add ```-vvv``` for verbose output:
```bash
ansible-playbook -vvv -i inventory/hosts.yml playbooks/install_gitlab_runner.yml
```
## Contributing

Feel free to open issues or submit pull requests for improvements.

## License
This project is licensed under the MIT License. See the ```LICENSE``` file for details.