---
title: Ausführen eines Azure Service Fabric-Diensts unter einem gruppenverwalteten Dienstkonto
description: Hier erfahren Sie, wie Sie einen Dienst als gruppenverwaltetes Dienstkonto auf einem eigenständigen, Windows-basierten Service Fabric-Cluster ausführen.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: 99d8089bd12d05e46f91e55c933d58d50baa92f5
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75464261"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Ausführen eines Diensts als gruppenverwaltetes Dienstkonto
Auf einem eigenständigen Windows Server-Cluster können Sie einen Dienst unter Verwendung einer RunAs-Richtlinie als gruppenverwaltetes Dienstkonto (group Managed Service Account, gMSA) ausführen.  Standardmäßig werden Service Fabric-Anwendungen unter dem Konto ausgeführt, unter dem der Prozess „Fabric.exe“ ausgeführt wird. Die Ausführung von Anwendungen unter verschiedenen Konten sorgt dafür, dass die Anwendungen besser voreinander geschützt sind – sogar in einer gehosteten Umgebung mit gemeinsamer Nutzung. Hinweis: Hierbei wird Active Directory lokal in Ihrer Domäne verwendet, nicht Azure Active Directory (Azure AD). Bei Verwendung eines gruppenverwalteten Dienstkontos wird im Anwendungsmanifest kein Kennwort oder verschlüsseltes Kennwort gespeichert.  Sie können einen Dienst auch als [Active Directory-Benutzer oder -Gruppe](service-fabric-run-service-as-ad-user-or-group.md) ausführen.

Im folgende Beispiel wird gezeigt, wie ein gruppenverwaltetes Dienstkonto namens *svc-Test$* erstellt, dieses verwaltete Dienstkonto auf Clusterknoten bereitgestellt und der Benutzerprinzipal konfiguriert wird.

Voraussetzungen:
- Die Domäne benötigt einen KDS-Stammschlüssel.
- Es muss mindestens ein Windows Server 2012-Domänencontroller (oder ein R2-DC) in der Domäne vorhanden sein.

1. Bitten Sie einen Active Directory-Domänenadministrator, mit dem Cmdlet `New-ADServiceAccount` ein gruppenverwaltetes Dienstkonto zu erstellen, und vergewissern Sie sich, dass `PrincipalsAllowedToRetrieveManagedPassword` alle Service Fabric-Clusterknoten enthält. `AccountName`, `DnsHostName` und `ServicePrincipalName` müssen eindeutig sein.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Installieren und testen Sie das gruppenverwaltete Dienstkonto auf den einzelnen Service Fabric-Clusterknoten (beispielsweise `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Konfigurieren Sie den Benutzerprinzipal, und konfigurieren Sie die RunAsPolicy, um den Benutzer zu verweisen.
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE] 
> Wenn Sie eine RunAs-Richtlinie auf einen Dienst anwenden und das Dienstmanifest Endpunktressourcen mit dem HTTP-Protokoll deklariert, müssen Sie eine Richtlinie vom Typ **SecurityAccessPolicy** angeben.  Weitere Informationen finden Sie unter [Zuweisen einer Sicherheitszugriffsrichtlinie für HTTP- und HTTPS-Endpunkte](service-fabric-assign-policy-to-endpoint.md). 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lesen Sie als Nächstes die folgenden Artikel:
* [Informationen zum Anwendungsmodell](service-fabric-application-model.md)
* [Angeben von Ressourcen in einem Dienstmanifest](service-fabric-service-manifest-resources.md)
* [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
