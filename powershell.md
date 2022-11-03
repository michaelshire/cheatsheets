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

## Get the OS of the computer from AD Computer properties
```
get-adcomputer -filter {Name -eq "<computername>"} -Properties * | Select Name, OperatingSystem
```

## Get the computers with OS like "Windows Server" that have not logged on for 30 days
```
get-adcomputer -filter {OperatingSystem -like '*Windows Server*'} -properties Name,OperatingSystem,LastLogonDate | Where {($_.LastLogonDate -lt (Get-Date).AddDays(-30)) -and ($_.LastLogonDate -ne $NULL)} | Select Name,LastLogonDate,OperatingSystem | Export-CSV .\WinServerIdle30days.csv
```
