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

# Describe Azure Compute and Networking Services
VMs are IaaS, as you run whatever OS, software, and hosting configs you want.

### Creating a Linux VM and Installing Nginx
az vm create \
  --resource-group learn-d3882c15-fd39-45f5-afce-97fce2e77dee \
  --name my-vm \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys

az vm extension set \
  --resource-group learn-d3882c15-fd39-45f5-afce-97fce2e77dee \
  --vm-name my-vm \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --version 2.1 \
  --settings '{"fileUris":["https://raw.githubusercontent.com/MicrosoftDocs/mslearn-welcome-to-azure/master/configure-nginx.sh"]}' \
  --protected-settings '{"commandToExecute": "./configure-nginx.sh"}'

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

## Azure Virtual Networking
