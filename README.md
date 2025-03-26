# SGLang and VLLM Baremetal Deployment

This repository contains Ansible playbooks and configuration files to deploy SGLang and VLLM clusters on a multi-node setup. The playbooks are designed to run on a head node and worker nodes, ensuring that the necessary Docker containers are started with the required configurations.

## Prerequisites

- **Ansible**: Ensure Ansible is installed on your local machine.
- **Docker**: Ensure Docker is installed on all target nodes (head and workers).
- **SSH Access**: Ensure you have SSH access to all target nodes with the necessary credentials.

## Inventory Configuration

The inventory file `inventory.ini` contains the IP addresses and SSH credentials for the head and worker nodes. You can customize this file to match your environment.

### Example Inventory

```ini file: inventory.ini
[head]
192.168.1.10 ansible_user=root ansible_ssh_pass=MockPassword123

[workers]
192.168.1.11 ansible_user=root ansible_ssh_pass=MockPassword123

[all:vars]
ansible_ssh_common_args='-o StrictHostKeyChecking=no -o PreferredAuthentications=password'
ansible_connection=ssh
```

## Playbooks

### SGLang Cluster Deployment

To deploy the SGLang cluster, run the following command:

```sh
ansible-playbook -i inventory.ini sglang.yml
```

#### Playbook Explanation (`sglang.yml`)

- **Ensure no existing container with name "sglang_multinode" is running**:
  - This task stops and removes any existing Docker container with the name "sglang_multinode" to ensure a clean deployment.
- **Start SGLang on head node**:
  - This task runs a Docker container on the head node with the necessary environment variables and command to launch the SGLang server. The server listens on a specified port and is configured to manage the SGLang cluster.
- **Start SGLang on worker node**:
  - This task runs a Docker container on the worker node with the necessary environment variables and command to join the SGLang cluster. The worker nodes connect to the head node to form a distributed cluster.

### VLLM Cluster Deployment

To deploy the VLLM cluster, run the following command:

```sh
ansible-playbook -i inventory.ini vllm_cluster.yml
```

#### Playbook Explanation (`vllm_cluster.yml`)

- **Create `run_cluster.sh` script**:
  - This task creates a shell script `run_cluster.sh` on the target nodes. The script is used to start the Ray session and VLLM server. It ensures that the necessary environment variables are set and the required commands are executed.
- **Ensure no existing container with name "node" is running**:
  - This task stops and removes any existing Docker container with the name "node" to ensure a clean deployment.
- **Start Ray session**:
  - This task runs the `run_cluster.sh` script to start the Ray session on the head and worker nodes. Ray is a distributed computing framework that is used to manage the VLLM cluster.
- **Start VLLM on head node**:
  - This task runs a Docker container on the head node with the necessary environment variables and command to launch the VLLM server. The server listens on a specified port and is configured to manage the VLLM cluster.

## Customization

- **Inventory File**: Modify the `inventory.ini` file to include the IP addresses and SSH credentials of your head and worker nodes.
- **Environment Variables**: Adjust the environment variables in the playbooks to match your specific requirements.

## Troubleshooting

- **SSH Connection Issues**: Ensure that SSH access is configured correctly and that the SSH keys or passwords are correct.
- **Docker Issues**: Verify that Docker is installed and running on all target nodes.
- **Playbook Errors**: Check the Ansible output for any errors and ensure that all dependencies are met.