🧱 Components Explained
1. Parameters
"parameters": {
  "vmName": { ... },
  "adminUsername": { ... },
  "adminPassword": { ... },
  "location": { ... }
}


vmName → Name of your Linux VM.

adminUsername / adminPassword → Login credentials for the VM. (Note: For production, you’d prefer SSH keys instead of a password.)

location → Region where resources are created. Defaults to the resource group’s location.

🔑 Parameters = inputs you give at deploy time → flexible, reusable template.

2. Variables
"variables": {
  "networkInterfaceName": "...",
  "networkSecurityGroupName": "...",
  "virtualNetworkName": "...",
  "subnetName": "default",
  "publicIpAddressName": "...",
  "osDiskName": "...",
  "vmSize": "Standard_B1s"
}


Convenience shortcuts to avoid repeating long names.

For example, instead of typing "linuxvm01-nic" everywhere, you define it once in variables.

vmSize is fixed to Standard_B1s (cheap, entry-level VM). You can swap it with bigger sizes.

3. Network Security Group (NSG)
{
  "type": "Microsoft.Network/networkSecurityGroups",
  ...
  "properties": {
    "securityRules": [
      {
        "name": "default-allow-ssh",
        "properties": {
          "priority": 1000,
          "protocol": "Tcp",
          "access": "Allow",
          "direction": "Inbound",
          "destinationPortRange": "22"
        }
      }
    ]
  }
}


Think of this as a firewall for the VM.

It allows inbound SSH (port 22) traffic from anywhere.

By default, everything else is blocked unless you add more rules (e.g., 80/443 for web servers).

4. Virtual Network (VNet) + Subnet
{
  "type": "Microsoft.Network/virtualNetworks",
  ...
  "properties": {
    "addressSpace": { "addressPrefixes": [ "10.0.0.0/16" ] },
    "subnets": [
      {
        "name": "default",
        "properties": {
          "addressPrefix": "10.0.0.0/24",
          "networkSecurityGroup": {
            "id": "[resourceId('Microsoft.Network/networkSecurityGroups', variables('networkSecurityGroupName'))]"
          }
        }
      }
    ]
  }
}


VNet = private network in Azure (like your LAN in the cloud).

Subnet = subdivision of the VNet.

The subnet is bound to the NSG above → ensures firewall rules apply to all VMs inside it.

5. Public IP Address
{
  "type": "Microsoft.Network/publicIPAddresses",
  ...
  "properties": { "publicIPAllocationMethod": "Dynamic" }
}


Gives your VM a public-facing IP so you can connect via SSH from outside.

Dynamic means Azure assigns it when VM starts. Use Static if you want it permanent.

6. Network Interface (NIC)
{
  "type": "Microsoft.Network/networkInterfaces",
  ...
  "properties": {
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": { "id": "..." },
          "publicIPAddress": { "id": "..." }
        }
      }
    ]
  }
}


The NIC is like a physical network card.

It ties together:

Subnet (private IP)

Public IP (internet access)

This NIC is later attached to the VM.

7. Virtual Machine
{
  "type": "Microsoft.Compute/virtualMachines",
  ...
  "properties": {
    "hardwareProfile": { "vmSize": "Standard_B1s" },
    "osProfile": {
      "computerName": "...",
      "adminUsername": "...",
      "adminPassword": "...",
      "linuxConfiguration": { "disablePasswordAuthentication": false }
    },
    "storageProfile": {
      "osDisk": { "createOption": "FromImage", "name": "..." },
      "imageReference": {
        "publisher": "Canonical",
        "offer": "0001-com-ubuntu-server-focal",
        "sku": "20_04-lts",
        "version": "latest"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        { "id": "[resourceId('Microsoft.Network/networkInterfaces', ...)]" }
      ]
    }
  }
}


The actual Linux VM resource.

hardwareProfile → VM size (CPU/RAM).

osProfile → hostname, admin credentials, Linux settings.

storageProfile → OS disk and OS image (Ubuntu 20.04 LTS in this case).

networkProfile → attaches NIC (so it can communicate).

8. Outputs
"outputs": {
  "adminUsername": { "type": "string", "value": "[parameters('adminUsername')]" },
  "publicIPAddress": { "type": "string", "value": "[reference(variables('publicIpAddressName')).ipAddress]" }
}


After deployment, Azure shows:

The VM’s admin username

The public IP address to connect via SSH

⚡ Summary Flow

NSG → define rules (SSH allowed).

VNet + Subnet → create a private network and apply NSG.

Public IP → give VM internet access.

NIC → attach subnet + public IP.

VM → boot Ubuntu using NIC + disk.

Outputs → show login details and IP.