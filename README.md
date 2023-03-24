# Description

This is a repository for Ansible playbooks that will install and configure Docker on servers.

# Initial Setup Instructions

1. Add the file path for your SSH Ansible Key (from your Ansible controller node) in `ansible.cfg`

```
private_key_file = "~/.ssh/example_key"
```

2. Add the target IPs to the `inventory` file that you want Ansible to change.

3. Copy the example `example.yml` file located in `~/ansible-docker_playbooks/host_vars` and add your specific values for your target node you want to update.

```
cd ~/ansible-docker_playbook/host_vars
```
```
cp -av example.yml <destination_IP>.yml
```
- Example:
```
target_user: username
```

4. Run `ping.yml` playbook to ping all hosts to test connection

```
ansible-playbook playbooks/ping.yml
```

5. Run `docker_install.yml` to install Docker on your target hosts.

```
ansible-playbook playbooks/docker_install.yml
```

6. (Optional) You can run `user-modify_docker.yml` to create a Docker group and add your admin user (on target) to it. This is to avoid the need of running `sudo` every time you use Docker CLI.

```
ansible-playbook playbooks/user-modify_docker.yml
```

# Usage Example

- NOTE: Run `ansible-playbooks` command from the root directory of the project. All playbooks will be found in `ansible-docker_playbooks/playbooks`

```
ansible-playbook [playbooks/<file.yml>]
```
```
jonathan@dockerhost-01:~/ansible-docker_playbooks$ ansible-playbook --ask-become-pass playbooks/docker_install.yml
BECOME password:

PLAY [all] ***************************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [192.168.30.23]

TASK [Update repo cache (Ubuntu)] ****************************************************************
ok: [192.168.30.23]

PLAY [all] ***************************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [192.168.30.23]

TASK [Install required system packages] **********************************************************
ok: [192.168.30.23]

TASK [Add GPG apt Key for Docker] ****************************************************************
ok: [192.168.30.23]

TASK [Add Docker Repository] *********************************************************************
ok: [192.168.30.23]

TASK [Install Docker Engine, containerd, and Docker Compose] *************************************
ok: [192.168.30.23]

PLAY RECAP ***************************************************************************************
192.168.30.23              : ok=7    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

# Notes

- At the time of this writing, these playbooks will only install Docker on Ubuntu 22.04. Eventually it will be updated to run on RHEL derivatives like AlmaLinux and Rocky Linux.

- The `remote_user` variable in the host variable file will be the user that Ansible will be using to run commands. So make sure that this user already has `sudo` privileges.
    - This will also be the same user the `user-modify_docker.yml` will modify. So be sure that this user is the one that you want added to the Docker group.

- At this time, these will only install the Docker Engine, Containerd, and Docker Compose. It will NOT install and configure a Kubernetes cluster. 