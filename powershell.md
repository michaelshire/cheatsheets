# Powershell Cheat Sheet
## Encode a string into Base64
```
$Text = "This is a secret and should be hidden"
$Bytes = [System.Text.Encoding]::Unicode.GetBytes($Text)
$EncodedText =[Convert]::ToBase64String($Bytes)
$EncodedText
```

## Decode a string from Base64
```
$EncodedText = "VABoAGkAcwAgAGkAcwAgAGEAIABzAGUAYwByAGUAdAAgAGEAbgBkACAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA="
$DecodedText = [System.Text.Encoding]::Unicode.GetString([System.Convert]::FromBase64String($EncodedText))
$DecodedText
```

## Foreach loop against lines in a CSV to get AD User properties
```
foreach($email in Get-Content .\file.csv) {Get-ADUser -Filter {emailaddress -eq $email} -Property EmailAddress, Division | Select EmailAddress, Enabled, Division | Export-CSV .\outfile.csv -Append}
```

## Get the AzureAD properties for a user with specific email address
```
Get-AzureaDUser -Filter "mail eq '<email>'" | Select mail, userprincipalname, enabled, accountenabled, department
```
## Same as above, but with a loop to input from file.csv and output to a outfile.csv
```
foreach($email in Get-Content .\file.csv) {Get-AzureaDUser -Filter "mail eq '$email'" |Select mail, userprincipalname, accountenabled, department| Export-CSV .\outfile.csv -Append}
```

## Get the OS of the computer from AD Computer properties
```
get-adcomputer -filter {Name -eq "<computername>"} -Properties * | Select Name, OperatingSystem
```

## Get the computers with OS like "Windows Server"
```
get-adcomputer -filter {OperatingSystem -like '*Windows Server*'} -properties Name,OperatingSystem,LastLogonDate | Select Name,LastLogonDate,OperatingSystem | Export-CSV .\WinServer.csv
```

## Get the computers with OS like "Windows Server" that have not logged on for 90 days
```
get-adcomputer -filter {OperatingSystem -like '*Windows Server*'} -properties Name,OperatingSystem,LastLogonDate | Where {($_.LastLogonDate -lt (Get-Date).AddDays(-90)) -and ($_.LastLogonDate -ne $NULL)} | Select Name,LastLogonDate,OperatingSystem | Export-CSV .\WinServerIdle90days.csv
```

## Get the full DN for a user in AD
```
Get-ADUser -Filter 'Name -eq "<Firstname> <Lastname>"' -Properties * | Select-Object "Name", "EmailAddress", "distinguishedName"
```

# Create a spreadsheet that shows all Azure AD Groups and their Owners
```
$array = @()
$Properties=@{}
$Properties.add("GroupDisplayName","1")
$Properties.add("OwnerObjectId","2")
$Properties.add("OwnerObjectType","3")
$Properties.add("OwnerUserType","4")
$Properties.add("OwnerUserPrincipalName","5")
$groups = Get-AzureADGroup -All $true
Foreach($group in $groups){
     
     $Owners = Get-AzureADGroupOwner -ObjectId $group.ObjectId -All $true
     $Properties.GroupDisplayName=$group.DisplayName
            
     if($Owners -ne $null){
       # group has owner
        Foreach($Owner in $Owners){
    
                $Properties.OwnerObjectId=$Owner.ObjectId
                $Properties.OwnerObjectType=$Owner.ObjectType
                $Properties.OwnerUserType=$Owner.UserType
                $Properties.OwnerUserPrincipalName=$Owner.UserPrincipalName
                $obj=New-Object PSObject -Property $Properties
                $array +=$obj 
    
    
        }
     }
     else{
                #group has no owner
                $Properties.OwnerObjectId=$null
                $Properties.OwnerObjectType=$null
                $Properties.OwnerUserType=$null
                $Properties.OwnerUserPrincipalName=$null
                $obj=New-Object PSObject -Property $Properties
                $array +=$obj  
 
 
 
     }

}
$array | export-csv -Path .\test123.csv -NoTypeInformation -Encoding UTF8
```
Source: https://stackoverflow.com/questions/58870231/export-all-azure-ad-groups-and-their-owner-to-a-csv-file
