# **Walrus Ansible Deployment**

This repository contains Ansible playbooks for deploying a **Walrus node** on **Ubuntu**, which includes the following components:

- **Storage**
- **Aggregator**
- **Publisher**

These playbooks help automate the deployment of a Walrus node across multiple environments, including **testnet**.
---

## **Prerequisites**

### **1. Generate Storage & Publisher wallet**
Generate a **Walrus Storage & Publisher wallet** locally on your machine, run:

```bash
mkdir -p storage publisher
walrus generate-sui-wallet --path storage
walrus generate-sui-wallet --path publisher
```
This will generate the following keys in each folder: `sui.aliases` and `sui.keystore` (SAVE THEM IN COLD STORAGE AND KEEP THEM SAFE)
You can use the address generated in the `Modify Configuration` section to set the environment variables:
 - `wallet_storage`
 - `wallet_publisher`

## **How to Deploy**

### **1. Configure the Inventory File**
Modify `inventory.ini` to specify your server details:

```ini
[walrus_nodes]
walrus-testnet ansible_host=your-ssh-host
```

Replace:
- `your-ssh-host` with the target server IP address.
---

### **2. Run the Ansible Playbook**
To deploy a **Walrus node**, run for example here client = `Imperator`, number node = `1` and env for deploy is `testnet`:

```bash
ansible-playbook install_walrus.yml -e "target=walrus-testnet chain_name=walrus client=imperator number=1 env_node=testnet"
```

This will:
- Install necessary dependencies
- Deploy the Walrus Storage, Aggregator, and Publisher

### **3. Deploy the keys generated**
In part **1.**, we create keys. You now need to move them to the host as follows:

`storage` with `sui.aliases` and `sui.keystore` under: 
```
.walrus_testnet-node_1/volumes/config
```

`publisher` with `sui.aliases` and `sui.keystore` under: 
```
.walrus_testnet-node_1/volumes/config/publisher
```

## **Customization**

### **Modify Configuration**
You can edit the configuration files inside:

- `chains/chains_testnet/walrus.yaml` for testnet
- `chains/chains_mainnet/walrus.yaml` for mainnet

For example, modify `walrus`:

```yaml
# Storage node binary and version
node:
    binary_url: https://xxxxxxxxx
    binary_version: v1.12.0-56e2fd6-ubuntu-x86_64
# Aggregator & publisher node binary and version
aggregator_publisher:
    binary_url: https://xxxxxxxxx
    binary_version: v1.12.0-56e2fd6-ubuntu-x86_64

# Client name
imperator:
    # node number
    node_1:
        # target host = must match inventory.ini
        host: walrus-testnet
        # dns for storage node
        dns_storage: xxxxxxxxxx.imperator.co
        # dns for aggregator node
        dns_aggregator: xxxxxxxxxx.imperator.co
        # dns for storage node
        dns_publisher: xxxxxxxxxx.imperator.co
        # wallet sui for storage node
        wallet_storage: "0xxxxxxxxxxx"
        # wallet sui for publisher node
        wallet_publisher: "0xxxxxxxxxxx"
        # node name define
        node_name: Imperator.co
        # node capacity registration setup
        node_capacity: 53000000000000
        port_storage: 9185
        port_storage_metrics: 9184
        port_aggregator: 9000
        port_aggregator_metrics: 9020
        port_publisher: 9001
        port_publisher_metrics: 9010

```

### Change Docker Image & Compose
If the deployment uses diffrent docker image/url, update the file to pull a different Walrus image :
- `roles/install_walrus/templates/docker-compose.yaml`
- `roles/install_walrus/templates/Dockerfile`
---
