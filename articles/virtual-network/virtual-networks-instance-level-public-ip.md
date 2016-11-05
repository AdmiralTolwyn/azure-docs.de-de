---
title: Öffentliche IP-Adresse auf Instanzebene (ILPIP) | Microsoft Docs
description: Grundlegendes zu ILPIP (PIP) und deren Verwaltung
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: jdial

---
# Übersicht über die öffentliche IP-Adresse auf Instanzebene
Eine öffentliche IP-Adresse auf Instanzebene (Instance-Level Public IP, ILPIP) ist eine öffentliche IP-Adresse, die Sie anstelle des Clouddiensts, in dem sich die VM- oder Rolleninstanz befindet, Ihrer VM- oder Rolleninstanz direkt zuweisen können. Sie tritt nicht an die Stelle der VIP (Virtual IP), die dem Clouddienst zugeordnet ist. Es ist vielmehr eine zusätzliche IP-Adresse, mit der Sie direkt eine Verbindung mit der VM oder Rolleninstanz herstellen können.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Erfahren Sie, wie Sie [diese Schritte mit dem Resource Manager-Modell ausführen](virtual-network-ip-addresses-overview-arm.md).

Stellen Sie sicher, dass Sie die Funktionsweise von [IP-Adressen](virtual-network-ip-addresses-overview-classic.md) in Azure verstehen.

> [!NOTE]
> In der Vergangenheit wurde die ILPIP als PIP bezeichnet, was für Public IP bzw. öffentliche IP-Adresse steht.
> 
> 

![Unterscheidung zwischen ILPIP und VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Wie in Abbildung 1 dargestellt, erfolgt der Zugriff auf den Clouddienst über eine VIP-Adresse, während auf die einzelnen virtuellen Computer normalerweise unter Angabe von VIP:&lt;Portnummer&gt; zugegriffen wird. Die Zuweisung einer ILPIP zu einem bestimmten virtuellen Computer ermöglicht es, über diese IP-Adresse direkt auf diesen virtuellen Computer zuzugreifen.

Bei der Erstellung eines Clouddiensts in Azure werden automatisch entsprechende DNS A-Datensätze für den Zugriff auf den Dienst über einen vollständig qualifizierten Domänennamen (FQDN) anstelle der tatsächlichen VIP-Adresse erstellt. Dasselbe geschieht für die ILPIP, um den Zugriff auf die VM- oder Rolleninstanz über den FQDN statt der ILPIP zu ermöglichen. Wenn Sie beispielsweise einen Clouddienst namens *contosoadservice* erstellen und eine Webrolle namens *contosoweb* mit zwei Instanzen konfigurieren, registriert Azure die folgenden A-Datensätze für die Instanzen:

* contosoweb\_IN\_0.contosoadservice.cloudapp.net
* contosoweb\_IN\_1.contosoadservice.cloudapp.net

> [!NOTE]
> Sie können einer VM- oder Rolleninstanz jeweils nur eine ILPIP zuweisen. Sie können bis zu 5 ILPIPs pro Abonnement verwenden. Derzeit wird die ILPIP nicht für virtuelle Computer mit mehreren Netzwerkkarten unterstützt.
> 
> 

## Warum sollte ich ein ILPIP anfordern?
Wenn Sie über eine direkt zugewiesene IP-Adresse eine Verbindung mit Ihrer VM- oder Rolleninstanz herstellen möchten, anstatt VIP:&lt;Portnummer&gt; des Clouddiensts zu verwenden, fordern Sie eine ILPIP für die VM- oder Rolleninstanz an.

* **Passives FTP:** Wenn dem virtuellen Computer eine ILPIP zugewiesen ist, können Sie an fast allen Ports Daten empfangen und müssen keinen Endpunkt für das Empfangen von Daten öffnen. Dadurch werden Szenarios wie passives FTP möglich, in denen die Ports dynamisch ausgewählt werden.
* **Ausgehende IP:** Der vom virtuellen Computer ausgehende Datenverkehr wird mit der ILPIP als Quelle gesendet, und dadurch wird der virtuelle Computer gegenüber externen Entitäten eindeutig identifiziert.

## Anfordern einer ILPIP während der Erstellung des virtuellen Computers
Das folgende PowerShell-Skript erstellt einen neuen Clouddienst namens *FTPService*, ruft dann ein Image aus Azure ab und erstellt mit dem abgerufenen Image einen virtuellen Computer namens *FTPInstance*, konfiguriert den virtuellen Computer für die Verwendung einer ILPIP, und fügt den virtuellen Computer dem neuen Dienst hinzu:

    New-AzureService -ServiceName FTPService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

## Abrufen von ILPIP-Informationen für einen virtuellen Computer
Zum Anzeigen der ILPIP-Informationen für den mit dem obigen Beispielskript erstellten virtuellen Computer führen Sie den folgenden PowerShell-Befehl aus, und beobachten Sie die Werte für *PublicIPAddress* und *PublicIPName*:

    Get-AzureVM -Name FTPInstance -ServiceName FTPService

    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

## Entfernen einer ILPIP von einem virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, um die ILPIP zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Remove-AzurePublicIP `
    | Update-AzureVM

## Hinzufügen einer ILPIP zu einem vorhandenen virtuellen Computer
Führen Sie den folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine ILPIP hinzuzufügen:

    Get-AzureVM -ServiceName FTPService -Name FTPInstance `
    | Set-AzurePublicIP -PublicIPName ftpip2 `
    | Update-AzureVM

## Zuordnen einer ILPIP zu einem virtuellen Computer mithilfe einer Dienstkonfigurationsdatei
Eine ILPIP kann auch mithilfe einer Dienstkonfigurationsdatei einem virtuellen Computer zugeordnet werden. Im folgenden XML-Beispiel wird gezeigt, wie Sie einen Clouddienst so konfigurieren, dass eine ILPIP mit dem Namen *MyPublicIP* für eine Rolleninstanz verwendet wird:

    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <VirtualNetworkSite name="VNet"/>
        <AddressAssignments>
          <InstanceAddress roleName="VMRolePersisted">
            <Subnets>
              <Subnet name="Subnet1"/>
              <Subnet name="Subnet2"/>
            </Subnets>
            <PublicIPs>
              <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>

## Nächste Schritte
* Informieren Sie sich über die Funktionsweise der [IP-Adressierung](virtual-network-ip-addresses-overview-classic.md) im klassischen Bereitstellungsmodell.
* Informieren Sie sich über [reservierte IPs](virtual-networks-reserved-public-ip.md).

<!---HONumber=AcomDC_0810_2016-->