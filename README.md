# **Walrus Ansible Deployment**

This repository contains Ansible playbooks for deploying a **Walrus node** on **Ubuntu**, including the following components:

- **Storage**
- **Aggregator**
- **Publisher**

These playbooks automate the deployment of a Walrus node across multiple environments, including **testnet**.

---

## **Prerequisites**

### **1. Generate Storage & Publisher Wallets**
To generate a **Walrus Storage & Publisher wallet** locally, run the following commands:

```bash
mkdir -p storage publisher
walrus generate-sui-wallet --path storage
walrus generate-sui-wallet --path publisher
```

This will generate the following key files in each folder: `sui.aliases` and `sui.keystore`.

> **Important:** Save these keys in **cold storage** and keep them secure.

You will use the generated wallet addresses in the **Modify Configuration** section to set the following environment variables:
- `wallet_storage`
- `wallet_publisher`

### **2. Modify Configuration**

Edit the configuration values inside:

- `chains/chains_testnet/walrus.yaml` for **testnet**
- `chains/chains_mainnet/walrus.yaml` for **mainnet**

#### Example Configuration:

```yaml
# Storage node binary and version
node:
    binary_url: https://example.com/path-to-binary
    binary_version: v1.12.0-56e2fd6-ubuntu-x86_64

# Aggregator & Publisher node binary and version
aggregator_publisher:
    binary_url: https://example.com/path-to-binary
    binary_version: v1.12.0-56e2fd6-ubuntu-x86_64

# Client configuration
imperator:
    node_1:
        # Target host (must match inventory.ini)
        host: walrus-testnet
        # DNS settings
        dns_storage: storage.example.com
        dns_aggregator: aggregator.example.com
        dns_publisher: publisher.example.com
        # Wallet addresses
        wallet_storage: "0x123456789..."
        wallet_publisher: "0x987654321..."
        # Node metadata
        node_name: Imperator.co
        node_capacity: 53000000000000
        # Ports
        port_storage: 9185
        port_storage_metrics: 9184
        port_aggregator: 9000
        port_aggregator_metrics: 9020
        port_publisher: 9001
        port_publisher_metrics: 9010
```

---

## **How to Deploy**

### **1. Configure the Inventory File**
Modify `inventory.ini` to specify your server details:

```ini
[walrus_nodes]
walrus-testnet ansible_host=your-ssh-host
```

Replace `your-ssh-host` with the actual server IP or hostname.

---

### **2. Run the Ansible Playbook**
To deploy a **Walrus node**, use the following command (where `client=Imperator`, `node=1`, and `env=testnet`):

```bash
ansible-playbook install_walrus.yml -e "target=walrus-testnet chain_name=walrus client=imperator number=1 env_node=testnet"
```

This process will:
- Install necessary dependencies
- Deploy the Walrus **Storage, Aggregator, and Publisher** nodes

---

### **3. Deploy the Generated Keys**
After generating the keys in **Step 1**, move them to the appropriate location on the target host:

- **Storage keys** (`sui.aliases` & `sui.keystore`) →
  ```
  .walrus_testnet-node_1/volumes/config
  ```

- **Publisher keys** (`sui.aliases` & `sui.keystore`) →
  ```
  .walrus_testnet-node_1/volumes/config/publisher
  ```

---

## **How to Upgrade**

### **1. Update the Binary URL/Version**

Edit the binary URL/version in the configuration files:

- `chains/chains_testnet/walrus.yaml` for **testnet**
- `chains/chains_mainnet/walrus.yaml` for **mainnet**

Example:

```yaml
node:
    binary_url: https://example.com/new-binary
    binary_version: v1.13.0-abcdef-ubuntu-x86_64
aggregator_publisher:
    binary_url: https://example.com/new-binary
    binary_version: v1.13.0-abcdef-ubuntu-x86_64
```

Then, rerun the **Ansible Playbook** as described in **Step 2**.

---

## **Customization**

### **Modify Docker Image & Compose Configuration**
If the deployment requires a different Docker image, update the following files accordingly:

- `roles/install_walrus/templates/docker-compose.yaml`
- `roles/install_walrus/templates/Dockerfile`

Make sure to use the correct **Docker image URL** and version for your setup.

---

This README provides a structured and detailed guide to deploying **Walrus nodes** using Ansible. Ensure all configurations are correctly set up before running the playbooks.