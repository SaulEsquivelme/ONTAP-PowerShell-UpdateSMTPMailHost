#PowerShell script to update ONTAP AutoSupport SMTP mailhost

$localpath = "D:\Documents\Script\SetSMTPMailHost\"
$filepath = "D:\\Documents\Script\"
$File = Import-Csv -Path $filepath"Storage_devices.csv" -Delimiter ";"
$Q = $File | where {$_.Class -eq "NetApp"} | where {$_.OS -eq "ONTAP"} 

foreach ($item in $Q){
$user = $item.Login
$pass = $item.Pass
$ip = $item.IP
$cluster = $item.DevName
$password = ConvertTo-SecureString -AsPlainText -Force $pass
$credential = new-object management.automation.pscredential $user, $password
Write-Host $cluster

try{
Connect-NcController $ip -Credential $credential -ErrorAction Stop | Out-Null
Get-NcNode | Set-NcAutoSupportConfig -MailHosts new.smtp.fqdn.com -To mail@domain.com | Out-Null
$mailhost = Get-NcAutoSupportConfig | Select-Object -Property MailHosts, To | select -unique | Format-Table -HideTableHeaders | Out-String
$mailhost = $mailhost -replace '[{}]',''
$mailhost = $mailhost -replace '[\n]',''
$row = "$cluster,$mailhost"
Add-Content -Path $localpath"NewSMTPvalidation.txt" $row
}
catch [NetApp.Ontapi.NaConnectionTimeoutException]{
Write-Host "Error on cluster: "$cluster
} 
}

#Complementary cmdlet: Test-NcAutoSupport | where {$_.CheckCategory -eq "smtp"} | where {$_.CheckType -eq "mail_server"} | Out-String
