# Azure CLI Notes

## 1. Create a resource group 
> az group create --name \<name> --location \<location>

Example
> az group create --name "holi" --location "east us"

Location options (Americas):
- Central US	
- East US 2	
- East US	
- North Central US	
- South Central US	
- West US 2	
- West Central US	
- West US	
- Canada Central	
- Canada East	
- Brazil South
- Mexico Central

List your resource groups
> az group list --output table

        Name                 Location    Status
        -------------------  ----------  ---------
        dani-resource-group  eastus      Succeeded
        NetworkWatcherRG     eastus      Succeeded
        vmrg                 eastus      Succeeded
        holi                 eastus      Succeeded



To filter the output to a specific resouorce gruop

> az group list --query "[?name == '$RESOURCE_GROUP']"

> az group list --query "[?name == 'holi']"

Learn more about the query language [here](http://jmespath.org/.)

