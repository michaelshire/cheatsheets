# AZ Cheat Sheet

## Use the --query to get a single item from the response and -o tsv to get it without quotes
```
az aro show --name <resourcename> --resource-group <resourceGroup> --query clusterProfile.domain -o tsv
```

## Use the --query to get properties in an array (not yet working!)
```
az aro show --name <resourcename> --resource-group <resourceGroup> --query '[].{Name:name}' -o json
```

## Apply `tags` to all resources in a `resourceGroup` in the `subscription`
## Where `tags` is in the format [tag1=value1 tag2=value2 tag3=value3 ...]
```
az login
az account set -s <subscription>
for /F "usebackq delims==" %t in (`az resource list -g <resourceGroup> --query [].id --output tsv`) do az resource tag --tags <tags> --id %t
```

## Create a multi-line secret key in Azure Key Vault with contents in `filename`
```
az keyvault secret set --name <secret name> --vault-name <vault-name> --subscription <subscription> --description <description> -f <filename>
```
  
## retrive a key and put it into `filename`
```
az keyvault secret show -o tsv --name <secretname> --vault-name <vaultname> --subscription <subscription> --query value >> <filename>
```

## View the details of an Enterprise Application
```
az ad app list --app-id <AppID>
```
