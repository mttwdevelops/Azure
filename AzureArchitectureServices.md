# Describe the Core Architectural Components of Azure
Many teams start exploring the cloud by moving existing applications to VMs in Azure.

Commands for Azure Cloud Shell (PowerShell)

```get-date```

```bash``` - enters bash mode instead of powershell

```az version```

```az upgrade```

```az interactive``` - makes terminal resemble IDE

```exit```

### Physical Infrastructure
Availability zones
- physically separate datacenters within an Azure region. Each AZ is made up of 1+ more datacenters, each with independent power, cooling, and networking. 
- each AZ is set up to be an insolation boundary, so if one goes down, another continues working.
- primarily for VMs, managed disks, load balancers, and SQL databases

Region pairs
- most Azure regions are paired with another region within the same geography at least 300 miles away. 
- helps with natural disasters, civil unrest, power outages, region network outages, etc.

Resource groups
- resources are placed into a resource group. Single resource can only be used in one resource group at a time.
- resource groups cannot be nested
- you can have multiple subscriptions per account, for instance, HR, IT, and dev teams can each have their own subscription for billing boundaries or access control
-   - this is an example of management groups, where resource groups are gathered into subscriptions
-   - 10,000 management groups can be supported in a single directory, with these groups supporting up to 6 levels of depth, does not include root level or subscriptions level
-   - each management group and sub can support only one parent.

### Describe Azure Compute and Networking Services
VMs are IaaS, as you run whatever OS, software, and hosting configs you want.

### Creating a Linux VM and Installing Nginx
```
az vm create \
  --resource-group learn-d3882c15-fd39-45f5-afce-97fce2e77dee \
  --name my-vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```
```
az vm extension set \
  --resource-group learn-d3882c15-fd39-45f5-afce-97fce2e77dee \
  --vm-name my-vm \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --version 2.1 \
  --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
  --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'
```

Command in depth: https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh

### Azure Containers
VMs are limited to a single OS per VM. If you want to run multiple instances of an app on a single host, containers are better.

### Serverless Computing (Azure Functions)
Responsibility of managing servers is handled for you. Only focus on development. 

3 main benefits:
1. No infrastructure managemnet like OS
2. Scalability
3. Only pay for what you use

### Application Hosting Options
VMs give max control and configuration settings.

Containers are able to isolate and individually manage different aspects of the hosting solution.

**Azure App Service** enales you to build and host web apps, background jobs, mobile back-ends, and APIs without managing infrastructure. 

# Azure Virtual Networking
Azure virtual networking supports both public and private endpoints for comms between external/internal resources.

Get VM's IP address and stores in Bash variable:
```
IPADDRESS="$(az vm list-ip-addresses \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --name my-vm \
  --query "[].virtualMachine.network.publicIpAddresses[*].ipAddress" \
  --output tsv)"
```

Downloads home page (this most likely doesn't work initially, we will set up later):
```
curl --connect-timeout 5 http://$IPADDRESS
```

List network security groups associated with VM:
```
az network nsg list \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --query '[].name' \
  --output tsv
```

List rules associated with the NSG named my-vmNSG
```
az network nsg rule list \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --nsg-name my-vmNSG
```

Running this command right after then tidies up the columns:
```
az network nsg rule list \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table
```

Create a rule called allow-http that allows inbound traffic from port 80:
```
az network nsg rule create \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --nsg-name my-vmNSG \
  --name allow-http \
  --protocol tcp \
  --priority 100 \
  --destination-port-range 80 \
  --access Allow
```

View updated list of rules:
```
az network nsg rule list \
  --resource-group learn-e5b93eb5-2e1b-4e05-9eff-69d4db5cb605 \
  --nsg-name my-vmNSG \
  --query '[].{Name:name, Priority:priority, Port:destinationPortRange, Access:access}' \
  --output table
```

From there, should be able to see your VM with:
```
curl --connect-timeout 5 http://$IPADDRESS
```

### Describe Azure Virtual Private Networks
Only 1 VPN gatewway can be deployed per virtual network, but a single gateweay can connect to multiple locations, such as other virtual networks or other on-premises datacenters.

2 different VPN types: policy-based or route-based. Both VPN gateways use a pre-shared key as the only method of authentication.
- **Policy-based** VPN gateways specify statically the IP address of packets that should be encrypted through each tunnel. 
- **Route-based** gateways have IPSec tunnels that are modeled as network interface or virtual tunnel interfaces. IP routing (static or dynamic routing protocols) decides which tunnel interfaces to use when sending packets. Preferred for on-premises devices, as they are more resilient to topology changes/creation of new subnets. 

### Describe Azure ExpressRoute
Extends on-premises networks into cloud over private connection.

Features include:
- global connectivity to MS services
- dynamic routing between network and Microsoft via Border Gateway Protocol (BGP)
- built in redundancy in every peering location

# Describe Azure Storage Devices
### Azure Storage Redundancy
Data in Azure storage account is always replicated 3 times in the primary region. 

Two options for how data is replicated in primary region:
1. **Locally redundant storage (LRS)** replicates data 3 times in single data center in primary region. Lowest cost and least durable compared to other options, as data is in 1 center.
2. **Zone redundant storage (ZRS)** replicates data 3 times across 3 Azure availability zones in primary region. Recommended for HA and also restricting replication of data within country/region for gov. reasons.

You can also copy data to secondary region. Azure offers **Geo-redundant storage (GRS)** and **Geo-zone-redundant storage(GZRS)**. GRS is running LRS in 2 regions, GZRS is running ZRS in main region and LRS in a second region.

### Azure Storage Services
- Blobs: massively scalable object store for text and binary data
- - ideal for images/docs to browser, storing files for distributed access, streaming audio/vid, backups and archives, data for analytics
- - Hot, cool, and archive level access
- Files: managed file shares
- - SMB or NFS protocols for file access.
- Queues: messaging store for reliable messaging between applications
- Disks: block-level storage for VMs

# Azure Identity, Access, and Security
