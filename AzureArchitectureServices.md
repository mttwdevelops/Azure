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
