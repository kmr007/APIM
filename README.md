# Automating Azure API management using powershell script
Automating the backup of Azure APIM using Azure powershell

# Pre-Requisites 
•	Azure PowerShell
•	Azure Subscription access with Owner privillege
•	Azure Automation Account
•	Azure Storage Account
•	Azure APIM

# Step 1 
Create a storage Account for storing the backup file of APIM.

# Step 2
Execute the below powershell script for automating the APIM backup using Azure Automation Account

$connectionName = "AzureRunAsConnection"
try
{
    # Get the connection "AzureRunAsConnection "
    $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

    "Logging in to Azure..."
    Add-AzureRmAccount `
        -ServicePrincipal `
        -TenantId $servicePrincipalConnection.TenantId `
        -ApplicationId $servicePrincipalConnection.ApplicationId `
        -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
		
}
catch {
    if (!$servicePrincipalConnection)
    {
        $ErrorMessage = "Connection $connectionName not found."
        throw $ErrorMessage
    } else{
        Write-Error -Message $_.Exception
        throw $_.Exception
    }
}
Select-AzureRmSubscription “Subscription ID where the APIM resides”
# Select the APIM instance to backup
$destinationApim = Get-AzureRmApiManagement | Where-Object {$_.Name -eq "APIM resource group name"}
$sourceApim = Get-AzureRmApiManagement | Where-Object {$_.Name -eq "APIM name"}
# Select the storage account to hold the backup
$storagecontext = New-AzureStorageContext -StorageAccountKey 'enter the storage account key' -StorageAccountName 'enter the storage account name'
#Get todays date as a string for the backup filename
$todaysdate = get-date -Format "yyyyMMdd-HHmm"
Select-AzureRmSubscription 'enter the subscription id'

# Perform APIM config backup
$containername="apibackup"
$blobname="myapimservice$todaysdate.apimbackup"
Backup-AzureRmApiManagement -ResourceGroupName 'apim resource group'	-Name 'apiname' -StorageContext $StorageContext1 -TargetContainerName $containername -TargetBlobName $blobname

# Steps Summary
•	Create Storage Account for taking backup
•	Backup APIM into storage Account using powershell script





