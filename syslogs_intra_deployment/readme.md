# Syslogs Deployment with Ansible

This project sets up and configures **Promtail**, **Rsyslog**, and **Keepalived** using Ansible playbooks, with configuration files that can be customized to suit your infrastructure.

## Prerequisites

Before you can run the playbook, ensure the following are installed on your system:

- **Ansible**: Used to automate the installation and configuration steps.
- **Git**: To clone the repository.

### Installation of Dependencies

#### 1. Install Ansible
If Ansible is not installed, run the following commands to install it:

For **Debian/Ubuntu**:
```bash
sudo apt update
sudo apt install ansible -y
```

For **RedHat/CentOS**:
```bash
sudo yum install epel-release -y
sudo yum install ansible -y
```

#### 2. Install Git
If you don't have `git` installed, use the following commands:

For **Debian/Ubuntu**:
```bash
sudo apt install git -y
```

For **RedHat/CentOS**:
```bash
sudo yum install git -y
```

### Clone the Repository
Once you have the prerequisites installed, clone this repository:

```bash
git clone https://github.com/your-username/syslogs-deployment.git
cd syslogs-deployment
```

## Configuration

The configuration is driven by variables that need to be customized to match your environment. The main configuration file is located in `vars/global_vars.yml`.

### Variables to Edit

Open `vars/global_vars.yml` and update the following variables:

```yaml
# Keepalived Configuration
vrrp_interface: eth0          # The network interface for VRRP
virtual_router_id: 51          # The router ID for VRRP (should be unique)
unicast_src_ip: 192.168.1.1    # Your server's main IP
unicast_peer_ip: 192.168.1.2   # Peer server IP (for redundancy)
virtual_ipaddress: 192.168.1.100 # The virtual IP address that Keepalived will manage

# Promtail Configuration
node_name: your_node_name_here  # Name of the node for logging
region: your_region_here        # e.g., asiapacific, europe, etc.
provider: onprem                # Your infrastructure provider, e.g., on-prem or cloud
app_name: your_app_name_here     # Name of the application (e.g., syslog-collector)

# Add sensitive information (make sure to use correct values)
client_id: your_client_id
client_secret: your_client_secret
token_url: your_token_url
```
## Running the Playbook

Once you have edited the `global_vars.yml` file, you can run the Ansible playbook to install and configure the services.

### 1. Run the Playbook

```bash
ansible-playbook playbook_syslogs_deployment.yaml
```

This will install and configure:

- **Promtail**: Collect log and forward it to MOP.
- **Rsyslog**: Forward logs.
- **Keepalived**: Manages failover and high availability using VRRP.

### 2. Validate Services

After the playbook runs successfully, you can verify that the services are up and running:

- **Promtail**: 
  ```bash
  sudo systemctl status promtail
  ```

- **Rsyslog**:
  ```bash
  sudo systemctl status rsyslog
  ```

- **Keepalived**:
  ```bash
  sudo systemctl status keepalived
  ```

Ensure all services are `active (running)`.

## Updating the Playbook

If any changes are required, for example, updating IP addresses, node names, or other configuration variables, simply:

1. Edit the variables in `vars/global_vars.yml`.
2. Re-run the playbook:
   ```bash
   ansible-playbook playbook_syslogs_deployment.yaml
   ```

## Troubleshooting
- **Keepalived or Promtail Service Not Starting**:
   - Verify the IP addresses and ensure there are no conflicts.
   - Check the logs for the specific service to find more details:
     ```bash
     sudo journalctl -u keepalived
     sudo journalctl -u promtail
     ```

## Customizing the Deployment

If you need to make changes to the configuration templates:

- **Keepalived Template**: Located at `roles/keepalived/templates/keepalived.conf.j2`.
- **Promtail Config**: Located at `roles/promtail/templates/promtail-config.j2`.
- **Promtail Service**: Located at `roles/promtail/templates/promtail-service.j2`.
- **Rsyslog Config**: Located at `roles/rsyslog/templates/rsyslog-config.j2`.

Update the templates, then re-run the playbook to apply changes.

## Uninstalling

To uninstall the services:

1. **Stop and Disable the Services**:
   ```bash
   sudo systemctl stop promtail rsyslog keepalived
   sudo systemctl disable promtail rsyslog keepalived
   ```

2. **Remove Installed Files** (optional):
   ```bash
   sudo rm /etc/promtail/config-promtail.yaml
   sudo rm /etc/systemd/system/promtail.service
   sudo rm /etc/rsyslog.d/00-promtail-relay.conf
   sudo rm /etc/keepalived/keepalived.conf
   ```
