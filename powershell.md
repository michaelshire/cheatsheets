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
