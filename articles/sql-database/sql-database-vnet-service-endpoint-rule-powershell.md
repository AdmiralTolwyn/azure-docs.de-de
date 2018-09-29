---
title: PowerShell für virtuelle Dienstendpunkte und -regeln in Azure SQL | Microsoft-Dokumentation
description: Stellt PowerShell-Skripts zum Erstellen und Verwalten von virtuellen Dienstendpunkten für Ihre Instanzen von Azure SQL-Datenbank und Azure SQL Data Warehouse bereit.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: DhruvMsft
ms.author: dmalik
ms.reviewer: genemi, vanto
manager: craigg
ms.date: 06/14/2018
ms.openlocfilehash: 50e88dd11b8a883a4d2999ad2d0419cbf7176078
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161147"
---
# <a name="use-powershell-to-create-a-virtual-service-endpoint-and-rule-for-azure-sql-database-and-sql-data-warehouse"></a>Verwenden von PowerShell zum Erstellen eines virtuellen Dienstendpunkts und einer Regel für Azure SQL-Datenbank und Azure SQL Data Warehouse

Sowohl [Azure SQL-Datenbank](sql-database-technical-overview.md) als auch [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) unterstützen virtuelle Dienstendpunkte. 

> [!NOTE]
> Dieses Thema gilt für Azure SQL-Server sowie für Datenbanken von SQL-Datenbank und SQL Data Warehouse, die auf dem Azure SQL-Server erstellt werden. Der Einfachheit halber wird nur SQL-Datenbank verwendet, wenn sowohl SQL-Datenbank als auch SQL Data Warehouse gemeint sind.

Dieser Artikel stellt ein PowerShell-Skript bereit, das folgende Vorgänge ausführt, und erläutert dieses:

1. Erstellt einen *virtuellen Dienstendpunkt* von Microsoft Azure in Ihrem Subnetz.
2. Fügt den Endpunkt zur Firewall Ihres Azure SQL-Datenbankservers hinzu, um eine *Regel für ein virtuelles Netzwerk* zu erstellen.

Die Gründe für das Erstellen einer Regel werden in [Virtual Service endpoints for Azure SQL Database (Virtuelle Dienstendpunkte für Azure SQL-Datenbank)][sql-db-vnet-service-endpoint-rule-overview-735r] erläutert.

> [!TIP]
> Wenn Sie nur den *Typnamen* des virtuellen Dienstendpunkts für SQL-Datenbank bewerten oder zu Ihrem Subnetz hinzufügen müssen, fahren Sie mit dem [direkteren PowerShell-Skript](#a-verify-subnet-is-endpoint-ps-100) fort.

#### <a name="major-cmdlets"></a>Wichtige Cmdlets

In diesem Artikel wird das Cmdlet mit **New-AzureRmSqlServerVirtualNetworkRule** erläutert, das den Subnetzendpunkt zur Zugriffssteuerungsliste (access control list, ACL) Ihres Azure SQL-Datenbankservers hinzufügt und dadurch eine Regel erstellt.

Die folgende Liste zeigt die Abfolge von anderen *wichtigen* Cmdlets, die Sie ausführen müssen, um den Aufruf von **New-AzureRmSqlServerVirtualNetworkRule** vorzubereiten. In diesem Artikel werden diese Aufrufe in [script 3 "Virtual network rule" (Skript 3: virtuelle Netzwerkregel)](#a-script-30) ausgeführt.

1. [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig): Erstellt ein Subnetzobjekt.

2. [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork): Erstellt Ihr virtuelles Netzwerk und weist diesem das Subnetz zu.

3. [Set-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetworkSubnetConfig): Weist Ihrem Subnetz einen virtuellen Dienstendpunkt zu.

4. [Set-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork): Behält Updates bei, die für Ihr virtuelles Netzwerk durchgeführt wurden.

5. [New-AzureRmSqlServerVirtualNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqlservervirtualnetworkrule): Sobald Ihr Subnetz ein Endpunkt ist, wird dieses als virtuelle Netzwerkregel in die ACL Ihres Azure SQL-Datenbankservers hinzugefügt.
    - Der Parameter **-IgnoreMissingVnetServiceEndpoint** wird ab AzureRM PowerShell-Modul Version 5.1.1 angeboten.

#### <a name="prerequisites-for-running-powershell"></a>Voraussetzungen für das Ausführen von PowerShell

- Sie können sich bereits bei Azure anmelden, z.B. über das [Azure-Portal][http-azure-portal-link-ref-477t].
- Sie können bereits PowerShell-Skripts ausführen.

> [!NOTE]
> Vergewissern Sie sich, dass Dienstendpunkte für das VNET/Subnetz aktiviert sind, das Sie dem Server hinzufügen möchten, da andernfalls die VNET-Firewallregel nicht erstellt werden kann.

#### <a name="one-script-divided-into-four-chunks"></a>Ein in vier Blöcke unterteiltes Skript

Das PowerShell-Beispielskript ist in eine Abfolge von kleineren Skripts unterteilt. Die Aufteilung erleichtert das Lernen und bietet Flexibilität. Die Skripts müssen in ihrer angegebenen Reihenfolge ausgeführt werden. Wenn Sie keine Zeit für das Ausführen der Skripts haben: Die tatsächliche Testausgabe wird nach Skript 4 angezeigt.






<a name="a-script-10" />

## <a name="script-1-variables"></a>Skript 1: Variablen

Dieses erste PowerShell-Skript weist Variablen Werte zu. Die nachfolgenden Skripts hängen von diesen Variablen ab.

> [!IMPORTANT]
> Bevor Sie dieses Skript ausführen, können Sie die Werte bei Bedarf bearbeiten. Wenn Sie beispielsweise bereits über eine Ressourcengruppe verfügen, sollten Sie den Namen Ihrer Ressourcengruppe dem zugewiesenen Wert entsprechend bearbeiten.
>
>  Ihr Abonnementname sollte in das Skript eingefügt werden.

#### <a name="powershell-script-1-source-code"></a>Quellcode von PowerShell-Skript 1

```powershell
######### Script 1 ########################################
##   LOG into to your Azure account.                     ##
##   (Needed only one time per powershell.exe session.)  ##
###########################################################

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

###########################################################
##  Assignments to variables used by the later scripts.  ##
###########################################################

# You can edit these values, if necessary.

$SubscriptionName = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName $SubscriptionName;

$ResourceGroupName = 'RG-YourNameHere';
$Region            = 'westcentralus';

$VNetName            = 'myVNet';
$SubnetName          = 'mySubnet';
$VNetAddressPrefix   = '10.1.0.0/16';
$SubnetAddressPrefix = '10.1.1.0/24';
$VNetRuleName        = 'myFirstVNetRule-ForAcl';

$SqlDbServerName         = 'mysqldbserver-forvnet';
$SqlDbAdminLoginName     = 'ServerAdmin';
$SqlDbAdminLoginPassword = 'ChangeYourAdminPassword1';

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Official type name.

Write-Host 'Completed script 1, the "Variables".';
```





<a name="a-script-20" />

## <a name="script-2-prerequisites"></a>Skript 2: Voraussetzungen

Dieses Skript bereitet Sie auf den nächsten Schritt vor, in dem die Endpunktaktion stattfindet. Dieses Skript erstellt die im Folgenden aufgelisteten Elemente für Sie, jedoch nur, wenn diese nicht bereits vorhanden sind. Sie können Skript 2 überspringen, wenn Sie sicher sind, dass diese Elemente bereits vorhanden sind:

- Azure-Ressourcengruppe
- Azure SQL-Datenbankserver

#### <a name="powershell-script-2-source-code"></a>Quellcode von PowerShell-Skript 2

```powershell
######### Script 2 ########################################
##   Ensure your Resource Group already exists.          ##
###########################################################

Write-Host "Check whether your Resource Group already exists.";

$gottenResourceGroup = $null;

$gottenResourceGroup = Get-AzureRmResourceGroup `
  -Name        $ResourceGroupName `
  -ErrorAction SilentlyContinue;

if ($null -eq $gottenResourceGroup)
{
    Write-Host "Creating your missing Resource Group - $ResourceGroupName.";

    $gottenResourceGroup = New-AzureRmResourceGroup `
      -Name $ResourceGroupName `
      -Location $Region;

    $gottenResourceGroup;
}
else { Write-Host "Good, your Resource Group already exists - $ResourceGroupName."; }

$gottenResourceGroup = $null;

###########################################################
## Ensure your Azure SQL Database server already exists. ##
###########################################################

Write-Host "Check whether your Azure SQL Database server already exists.";

$sqlDbServer = $null;

$sqlDbServer = Get-AzureRmSqlServer `
  -ResourceGroupName $ResourceGroupName `
  -ServerName        $SqlDbServerName `
  -ErrorAction       SilentlyContinue;

if ($null -eq $sqlDbServer)
{
    Write-Host "Creating the missing Azure SQL Database server - $SqlDbServerName.";

    Write-Host "Gather the credentials necessary to next create an Azure SQL Database server.";

    $sqlAdministratorCredentials = New-Object `
      -TypeName     System.Management.Automation.PSCredential `
      -ArgumentList `
        $SqlDbAdminLoginName, `
        $(ConvertTo-SecureString `
            -String      $SqlDbAdminLoginPassword `
            -AsPlainText `
            -Force `
         );

    if ($null -eq $sqlAdministratorCredentials)
    {
        Write-Host "ERROR, unable to create SQL administrator credentials.  Now ending.";
        return;
    }

    Write-Host "Create your Azure SQL Database server.";

    $sqlDbServer = New-AzureRmSqlServer `
      -ResourceGroupName $ResourceGroupName `
      -ServerName        $SqlDbServerName `
      -Location          $Region `
      -SqlAdministratorCredentials $sqlAdministratorCredentials;

    $sqlDbServer;
}
else { Write-Host "Good, your Azure SQL Database server already exists - $SqlDbServerName."; }

$sqlAdministratorCredentials = $null;
$sqlDbServer                 = $null;

Write-Host 'Completed script 2, the "Prerequisites".';
```






<a name="a-script-30" />

## <a name="script-3-create-an-endpoint-and-a-rule"></a>Skript 3: Erstellen eines Endpunkts und einer Regel

Dieses Skript erstellt ein virtuelles Netzwerk mit einem Subnetz. Das Skript weist Ihrem Subnetz dann den Endpunkttyp **Microsoft.Sql** zu. Schließlich fügt das Skript Ihr Subnetz zu der Zugriffssteuerungsliste (ACL) Ihres SQL-Datenbankservers hinzu und erstellt dadurch eine Regel.

#### <a name="powershell-script-3-source-code"></a>Quellcode von PowerShell-Skript 3

```powershell
######### Script 3 ########################################
##   Create your virtual network, and give it a subnet.  ##
###########################################################

Write-Host "Define a subnet '$SubnetName', to be given soon to a virtual network.";

$subnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Create a virtual network '$VNetName'." `
  "  Give the subnet to the virtual network that we created.";

$vnet = New-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -AddressPrefix     $VNetAddressPrefix `
  -Subnet            $subnet `
  -ResourceGroupName $ResourceGroupName `
  -Location          $Region;

###########################################################
##   Create a Virtual Service endpoint on the subnet.    ##
###########################################################

Write-Host "Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.";

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

Write-Host "Persist the updates made to the virtual network > subnet.";

$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

$vnet.Subnets[0].ServiceEndpoints;  # Display the first endpoint.

###########################################################
##   Add the Virtual Service endpoint Id as a rule,      ##
##   into SQL Database ACLs.                             ##
###########################################################

Write-Host "Get the subnet object.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

$subnet = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name           $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.";

$vnetRuleObject1 = New-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -VirtualNetworkSubnetId $subnet.Id;

$vnetRuleObject1;

Write-Host "Verify that the rule is in the SQL DB ACL.";

$vnetRuleObject2 = Get-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName;

$vnetRuleObject2;

Write-Host 'Completed script 3, the "Virtual-Netowrk-Rule".';
```






<a name="a-script-40" />

## <a name="script-4-clean-up"></a>Skript 4: Bereinigung

Dieses abschließende Skript löscht die Ressourcen, die das vorherige Skript zur Veranschaulichung erstellt hat. Das Skript fordert Sie jedoch zur Bestätigung auf, bevor Folgendes gelöscht wird:

- Azure SQL-Datenbankserver
- Azure-Ressourcengruppe

Sie können Skript 4 jederzeit nach dem Abschluss von Skript 1 ausführen.

#### <a name="powershell-script-4-source-code"></a>Quellcode von PowerShell-Skript 4

```powershell
######### Script 4 ########################################
##   Clean-up phase A:  Unconditional deletes.           ##
##                                                       ##
##   1. The test rule is deleted from SQL DB ACL.        ##
##   2. The test endpoint is deleted from the subnet.    ##
##   3. The test virtual network is deleted.             ##
###########################################################

Write-Host "Delete the rule from the SQL DB ACL.";

Remove-AzureRmSqlServerVirtualNetworkRule `
  -ResourceGroupName      $ResourceGroupName `
  -ServerName             $SqlDbServerName `
  -VirtualNetworkRuleName $VNetRuleName `
  -ErrorAction            SilentlyContinue;

Write-Host "Delete the endpoint from the subnet.";

$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

Remove-AzureRmVirtualNetworkSubnetConfig `
  -Name $SubnetName `
  -VirtualNetwork $vnet;

Write-Host "Delete the virtual network (thus also deletes the subnet).";

Remove-AzureRmVirtualNetwork `
  -Name              $VNetName `
  -ResourceGroupName $ResourceGroupName `
  -ErrorAction       SilentlyContinue;

###########################################################
##   Clean-up phase B:  Conditional deletes.             ##
##                                                       ##
##   These might have already existed, so user might     ##
##   want to keep.                                       ##
##                                                       ##
##   1. Azure SQL Database server                        ##
##   2. Azure resource group                             ##
###########################################################

$yesno = Read-Host 'CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]';
if ('yes' -eq $yesno)
{
    Write-Host "Remove the Azure SQL DB server.";
    
    Remove-AzureRmSqlServer `
      -ServerName        $SqlDbServerName `
      -ResourceGroupName $ResourceGroupName `
      -ErrorAction       SilentlyContinue;
    
    Write-Host "Remove the Azure Resource Group.";
    
    Remove-AzureRmResourceGroup `
      -Name        $ResourceGroupName `
      -ErrorAction SilentlyContinue;
}
else
{
    Write-Host "Skipped over the DELETE of SQL Database and resource group.";
}

Write-Host 'Completed script 4, the "Clean-Up".';
```






<a name="a-actual-output" />

## <a name="actual-output-from-scripts-1-through-4"></a>Tatsächliche Ausgabe aus Skript 1 bis 4

Als nächstes wird die Ausgabe des Testlaufs in abgekürztem Format angezeigt. Die Ausgabe kann hilfreich sein, falls Sie die PowerShell-Skripts jetzt nicht ausführen möchten.

```
[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s1-variables.ps1
Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: yes


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s2-prerequisites.ps1
Check whether your Resource Group already exists.
Creating your missing Resource Group - RG-YourNameHere.


ResourceGroupName : RG-YourNameHere
Location          : westcentralus
ProvisioningState : Succeeded
Tags              : 
ResourceId        : /subscriptions/22222222-2222-2222-2222-222222222222/resourceGroups/RG-YourNameHere

Check whether your Azure SQL Database server already exists.
Creating the missing Azure SQL Database server - mysqldbserver-forvnet.
Gather the credentials necessary to next create an Azure SQL Database server.
Create your Azure SQL Database server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Completed script 2, the "Prerequisites".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s3-vnet-rule.ps1
Define a subnet 'mySubnet', to be given soon to a virtual network.
Create a virtual network 'myVNet'.   Give the subnet to the virtual network that we created.
WARNING: The output object type of this cmdlet will be modified in a future release.
Assign a Virtual Service endpoint 'Microsoft.Sql' to the subnet.
Persist the updates made to the virtual network > subnet.

Get the subnet object.
Add the subnet .Id as a rule, into the ACLs for your Azure SQL Database server.
ProvisioningState Service       Locations      
----------------- -------       ---------      
Succeeded         Microsoft.Sql {westcentralus}
                                               
Verify that the rule is in the SQL DB ACL.
                                               
Completed script 3, the "Virtual-Network-Rule".



[C:\WINDOWS\system32\]
0 >> C:\Demo\PowerShell\sql-database-vnet-service-endpoint-powershell-s4-clean-up.ps1
Delete the rule from the SQL DB ACL.

Delete the endpoint from the subnet.


Delete the virtual network (thus also deletes the subnet).
CAUTION !: Do you want to DELETE your Azure SQL Database server AND your Resource Group?  [yes/no]: yes
Remove the Azure SQL DB server.

ResourceGroupName        : RG-YourNameHere
ServerName               : mysqldbserver-forvnet
Location                 : westcentralus
SqlAdministratorLogin    : ServerAdmin
SqlAdministratorPassword : 
ServerVersion            : 12.0
Tags                     : 
Identity                 : 

Remove the Azure Resource Group.
True
Completed script 4, the "Clean-Up".
```

Hier endet das PowerShell-Hauptskript.





<a name="a-verify-subnet-is-endpoint-ps-100" />

## <a name="verify-your-subnet-is-an-endpoint"></a>Überprüfen, dass Ihr Subnetz ein Endpunkt ist

Wenn Ihr Subnetz bereits zum Typnamen **Microsoft.Sql** zugewiesen wurde, ist dieses bereits ein virtueller Dienstendpunkt. Sie können das [Azure-Portal][http-azure-portal-link-ref-477t] verwenden, um eine virtuelle Netzwerkregel für den Endpunkt zu erstellen.

Falls Sie nicht sicher sind, ob Ihr Subnetz über den Typnamen **Microsoft.Sql** verfügt, führen Sie folgendes PowerShell-Skript aus, um diese Aktionen durchzuführen:

1. Ermitteln, ob das Subnetz über den Typnamen **Microsoft.Sql** verfügt.
2. Zuweisen des Typnamens, wenn dieser nicht vorhanden ist (optional).
    - Das Skript fordert Sie zur *Bestätigung* auf, bevor der fehlende Typname angewendet wird.

#### <a name="phases-of-the-script"></a>Phasen des Skripts

Im Folgenden finden Sie die Phasen des PowerShell-Skripts:

1. Melden Sie sich bei Ihrem Azure-Konto an. Dies ist nur einmal pro PowerShell-Sitzung nötig.  Weisen Sie die Variablen zu.
2. Suchen Sie Ihr virtuelles Netzwerk und dann Ihr Subnetz.
3. Ist Ihr Subnetz als **Microsoft.Sql**-Endpunktservertyp markiert?
4. Fügen Sie Ihrem Subnetz einen virtuellen Dienstendpunkt des Typnamens **Microsoft.Sql** zu.

> [!IMPORTANT]
> Bevor Sie dieses Skript ausführen, müssen Sie die Werte bearbeiten, die den $-Variablen am Anfang des Skripts zugewiesen sind.

#### <a name="direct-powershell-source-code"></a>Direkter PowerShell-Quellcode

In diesem PowerShell-Skript werden keine Aktualisierungen vorgenommen, es sei denn, Sie antworten mit „Ja“, wenn Sie zur Bestätigung aufgefordert werden. Das Skript kann den Typnamen **Microsoft.Sql** zu Ihrem Subnetz hinzufügen. Das Skript versucht dies jedoch nur, wenn das Subnetz keinen Typnamen besitzt.

```powershell
### 1. LOG into to your Azure account, needed only once per PS session.  Assign variables.

$yesno = Read-Host 'Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]';
if ('yes' -eq $yesno) { Connect-AzureRmAccount; }

# Assignments to variables used by the later scripts.
# You can EDIT these values, if necessary.

$SubscriptionName  = 'yourSubscriptionName';
Select-AzureRmSubscription -SubscriptionName "$SubscriptionName";

$ResourceGroupName   = 'yourRGName';
$VNetName            = 'yourVNetName';
$SubnetName          = 'yourSubnetName';
$SubnetAddressPrefix = 'Obtain this value from the Azure portal.'; # Looks roughly like: '10.0.0.0/24'

$ServiceEndpointTypeName_SqlDb = 'Microsoft.Sql';  # Do NOT edit. Is official value.

### 2. Search for your virtual network, and then for your subnet.

# Search for the virtual network.
$vnet = $null;
$vnet = Get-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Name              $VNetName;

if ($vnet -eq $null)
{
    Write-Host "Caution: No virtual network found by the name '$VNetName'.";
    Return;
}

$subnet = $null;
for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{
    $subnet = $vnet.Subnets[$nn];
    if ($subnet.Name -eq $SubnetName)
    { break; }
    $subnet = $null;
}

if ($subnet -eq $null)
{
    Write-Host "Caution: No subnet found by the name '$SubnetName'";
    Return;
}

### 3. Is your subnet tagged as 'Microsoft.Sql' endpoint server type?

$endpointMsSql = $null;
for ($nn=0; $nn -lt $subnet.ServiceEndpoints.Count; $nn++)
{
    $endpointMsSql = $subnet.ServiceEndpoints[$nn];
    if ($endpointMsSql.Service -eq $ServiceEndpointTypeName_SqlDb)
    {
        $endpointMsSql;
        break;
    }
    $endpointMsSql = $null;
}

if ($endpointMsSql -ne $null)
{
    Write-Host "Good: Subnet found, and is already tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";
    Return;
}
else
{
    Write-Host "Caution: Subnet found, but not yet tagged as an endpoint of type '$ServiceEndpointTypeName_SqlDb'.";

    # Ask the user for confirmation.
    $yesno = Read-Host 'Do you want the PS script to apply the endpoint type name to your subnet?  [yes/no]';
    if ('no' -eq $yesno) { Return; }
}

### 4. Add a Virtual Service endpoint of type name 'Microsoft.Sql', on your subnet.

$vnet = Set-AzureRmVirtualNetworkSubnetConfig `
  -Name            $SubnetName `
  -AddressPrefix   $SubnetAddressPrefix `
  -VirtualNetwork  $vnet `
  -ServiceEndpoint $ServiceEndpointTypeName_SqlDb;

# Persist the subnet update.
$vnet = Set-AzureRmVirtualNetwork `
  -VirtualNetwork $vnet;

for ($nn=0; $nn -lt $vnet.Subnets.Count; $nn++)
{ $vnet.Subnets[0].ServiceEndpoints; }  # Display.
```

#### <a name="actual-output"></a>Tatsächliche Ausgabe

Der folgende Block zeigt das tatsächliche Feedback (mit Schönheitskorrekturen) an.

```powershell
<# Our output example (with cosmetic edits), when the subnet was already tagged:

Do you need to log into Azure (only one time per powershell.exe session)?  [yes/no]: no


Environment           : AzureCloud
Account               : xx@microsoft.com
TenantId              : 11111111-1111-1111-1111-111111111111
SubscriptionId        : 22222222-2222-2222-2222-222222222222
SubscriptionName      : MySubscriptionName
CurrentStorageAccount : 


ProvisioningState : Succeeded
Service           : Microsoft.Sql
Locations         : {westcentralus}

Good: Subnet found, and is already tagged as an endpoint of type 'Microsoft.Sql'.
#>
```




<!-- Link references: -->

[sql-db-vnet-service-endpoint-rule-overview-735r]: sql-database-vnet-service-endpoint-rule-overview.md

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

