---
title: HPC Pack-Cluster mit Azure Active Directory | Microsoft-Dokumentation
description: "Erfahren Sie, wie Sie einen Microsoft HPC Pack 2016-Cluster mithilfe von Azure Active Directory in Azure integrieren können."
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: jeconnoc
ms.assetid: 9edf9559-db02-438b-8268-a6cba7b5c8b7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 11/16/2017
ms.author: danlep
ms.openlocfilehash: bb0e878c4e987d111a535603cede25c639087ca7
ms.sourcegitcommit: 1d8612a3c08dc633664ed4fb7c65807608a9ee20
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2017
---
# <a name="manage-an-hpc-pack-cluster-in-azure-using-azure-active-directory"></a>Verwalten eines HPC Pack-Clusters in Azure mithilfe von Azure Active Directory
[Microsoft HPC Pack 2016](https://technet.microsoft.com/library/cc514029) unterstützt die Integration mit [Azure Active Directory](../../active-directory/index.md) (Azure AD) für Administratoren, die einen HPC Pack-Cluster in Azure bereitstellen.



Befolgen Sie die in diesem Artikel beschriebenen Schritte, um die folgenden allgemeinen Aufgaben auszuführen: 
* Manuelle Integration Ihres HPC Pack-Clusters in Ihren Azure AD-Mandanten
* Verwalten und Planen von Aufträgen in Ihrem HPC Pack-Cluster in Azure 

Für die Integration einer HPC Pack-Clusterlösung in Azure AD sind die folgenden Standardschritte zur Integration anderer Anwendungen und Dienste zu befolgen. In diesem Artikel wird vorausgesetzt, dass Sie mit der grundlegenden Benutzerverwaltung in Azure AD vertraut sind. Weitere Informationen finden Sie in der [Dokumentation zu Azure Active Directory](../../active-directory/index.md) und im folgenden Abschnitt.

## <a name="benefits-of-integration"></a>Vorteile der Integration


Azure Active Directory (Azure AD) ist ein mehrinstanzenfähiger cloudbasierter Dienst für die Verzeichnis- und Identitätsverwaltung, der das einmalige Anmelden (SSO) bei Cloudlösungen ermöglicht.

Durch die Integration eines HPC Pack-Clusters in Azure AD können Sie folgende Ziele erreichen:

* Entfernen des traditionellen Active Directory-Domänencontrollers aus dem HPC Pack-Cluster. Auf diese Weise können Sie die Kosten für die Clusterverwaltung senken, falls eine solche Verwaltung für Ihr Unternehmen nicht erforderlich ist, sowie den Bereitstellungsprozess beschleunigen.
* Profitieren Sie folgenden Vorteilen in Verbindung mit Azure AD:
    *   Einmaliges Anmelden 
    *   Verwenden einer lokalen AD-Identität für den HPC Pack-Cluster in Azure 

    ![Azure Active Directory-Umgebung](./media/hpcpack-cluster-active-directory/aad.png)


## <a name="prerequisites"></a>Voraussetzungen
* **Auf virtuellen Azure-Computern bereitgestellter HPC Pack 2016-Cluster**: Die erforderlichen Schritte finden Sie unter [Bereitstellen eines HPC Pack 2016-Clusters in Azure](hpcpack-2016-cluster.md). Sie benötigen den DNS-Namen des Hauptknotens und die Anmeldeinformationen eines Clusteradministrators, um die Schritte in diesem Artikel auszuführen.

  > [!NOTE]
  > Die Integration in Azure Active Directory wird von den Vorversionen von HPC Pack 2016 nicht unterstützt.



* **Clientcomputer**: Sie benötigen einen Windows- oder Windows Server-Clientcomputer, um HPC Pack-Clienthilfsprogramme auszuführen. Wenn Sie nur das HPC Pack-Webportal oder die REST-API zum Übermitteln von Aufträgen verwenden möchten, können Sie einen Clientcomputer Ihrer Wahl nutzen.

* **HPC Pack-Clienthilfsprogramme**: Installieren Sie die HPC Pack-Clienthilfsprogramme auf dem Clientcomputer. Verwenden Sie hierzu das kostenlose Installationspaket, das im Microsoft Download Center zur Verfügung steht.


## <a name="step-1-register-the-hpc-cluster-server-with-your-azure-ad-tenant"></a>Schritt 1: Registrieren des HPC-Clusterservers bei Ihrem Azure AD-Mandanten
1. Melden Sie sich auf dem [Azure-Portal](https://portal.azure.com)an.
2. Wenn Sie unter Ihrem Konto Zugriff auf mehrere Azure AD-Mandanten haben, können Sie oben rechts auf Ihr Konto klicken. Legen Sie dann die Portalsitzung auf den gewünschten Mandanten fest. Sie müssen über die Berechtigung verfügen, auf Ressourcen im Verzeichnis zuzugreifen. 
3. Klicken Sie im linken Navigationsbereich „Dienste“ auf **Azure Active Directory**, klicken Sie auf **Benutzer und Gruppen**, und stellen Sie sicher, dass bereits Benutzerkonten erstellt oder konfiguriert sind.
4. Klicken Sie in **Azure Active Directory** auf **App-Registrierungen** > **Registrierung einer neuen Anwendung**. Geben Sie Folgendes ein:
    * **Name**: HPCPackClusterServer
    * **Anwendungstyp**: Wählen Sie **Web-App/API** aus.
    * **Anmelde-URL**: Wählen Sie für dieses Beispiel die Basis-URL aus, die standardmäßig `https://hpcserver` lautet.
    * Klicken Sie auf **Erstellen**.
5. Nachdem die App hinzugefügt wurde, wählen Sie sie in der Liste **App-Registrierungen** aus. Klicken Sie dann auf **Einstellungen** > **Eigenschaften**. Geben Sie Folgendes ein:
    * Wählen Sie für **Mehrinstanzenfähig** die Option **Ja** aus.
    * Ändern Sie **App-ID-URI** in `https://<Directory_name>/<application_name>`. Ersetzen Sie `<Directory_name`> durch den vollständigen Namen Ihres Azure AD-Mandanten, zum Beispiel `hpclocal.onmicrosoft.com`, und ersetzen Sie `<application_name>` durch den von Ihnen zuvor gewählten Namen.
6. Klicken Sie auf **Speichern**. Nach Abschluss des Speichervorgangs klicken Sie auf der App-Seite auf **Manifest**. Bearbeiten Sie das Manifest, indem Sie die `appRoles`-Einstellung suchen und die folgende Anwendungsrolle hinzufügen. Klicken Sie dann auf **Speichern**.

  ```json
  "appRoles": [
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "displayName": "HpcAdminMirror",
     "id": "61e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "description": "HpcAdminMirror",
     "value": "HpcAdminMirror"
     },
     {
     "allowedMemberTypes": [
         "User",
         "Application"
     ],
     "description": "HpcUsers",
     "displayName": "HpcUsers",
     "id": "91e10148-16a8-432a-b86d-ef620c3e48ef",
     "isEnabled": true,
     "value": "HpcUsers"
     }
  ],
  ```
7. Klicken Sie in **Azure Active Directory** auf **Unternehmensanwendungen** > **Alle Anwendungen**. Wählen Sie **HPCPackClusterServer** aus der Liste aus.
8. Klicken Sie auf **Eigenschaften**, und ändern Sie **Benutzerzuweisung erforderlich** in **Ja**. Klicken Sie auf **Speichern**.
9. Klicken Sie auf **Benutzer und Gruppen** > **Benutzer hinzufügen**. Wählen Sie einen Benutzer und eine Rolle aus, und klicken Sie dann auf **Zuweisen**. Weisen Sie dem Benutzer eine der verfügbaren Rollen zu (HpcUsers oder HpcAdminMirror). Wiederholen Sie diesen Schritt mit zusätzlichen Benutzern im Verzeichnis. Weitere Hintergrundinformationen über Clusterbenutzer finden Sie unter [Verwalten von Clusterbenutzern](https://technet.microsoft.com/library/ff919335(v=ws.11).aspx).


## <a name="step-2-register-the-hpc-cluster-client-with-your-azure-ad-tenant"></a>Schritt 2: Registrieren des HPC-Clusterclients bei Ihrem Azure AD-Mandanten

1. Melden Sie sich auf dem [Azure-Portal](https://portal.azure.com)an.
2. Wenn Sie unter Ihrem Konto Zugriff auf mehrere Azure AD-Mandanten haben, können Sie oben rechts auf Ihr Konto klicken. Legen Sie dann die Portalsitzung auf den gewünschten Mandanten fest. Sie müssen über die Berechtigung verfügen, auf Ressourcen im Verzeichnis zuzugreifen. 
3. Klicken Sie in **Azure Active Directory** auf **App-Registrierungen** > **Registrierung einer neuen Anwendung**. Geben Sie Folgendes ein:

    * **Name**: HPCPackClusterClient    
    * **Anwendungstyp**: Wählen Sie **Nativ** aus.
    * **Umleitungs-URI** - `http://hpcclient`
    * Klicken Sie auf **Erstellen**

4. Nachdem die App hinzugefügt wurde, wählen Sie sie in der Liste **App-Registrierungen** aus. Kopieren Sie den Wert für **Anwendungs-ID**, und speichern Sie ihn. Sie benötigen diesen später für die Konfiguration Ihrer Anwendung.

5. Klicken Sie auf **Einstellungen** > **Erforderliche Berechtigungen** > **Hinzufügen** > **API auswählen**. Suchen Sie die Anwendung „HpcPackClusterServer“ (in Schritt 1 erstellt), und wählen Sie sie aus.

6. Wählen Sie auf der Seite **Zugriff aktivieren** die Option **Auf „HpcClusterServer“ zugreifen** aus. Klicken Sie anschließend auf **Fertig**.


## <a name="step-3-configure-the-hpc-cluster"></a>Schritt 3: Konfigurieren des HPC-Clusters

1. Stellen Sie in Azure eine Verbindung mit dem HPC Pack 2016-Hauptknoten her.

2. Starten Sie HPC-PowerShell.

3. Führen Sie den folgenden Befehl aus:

    ```powershell

    Set-HpcClusterRegistry -SupportAAD true -AADInstance https://login.microsoftonline.com/ -AADAppName HpcPackClusterServer -AADTenant <your AAD tenant name> -AADClientAppId <client ID> -AADClientAppRedirectUri http://hpcclient
    ```
    Hierbei gilt:

    * `AADTenant` gibt den Azure AD-Mandantennamen an, z.B. `hpclocal.onmicrosoft.com`.
    * `AADClientAppId` gibt die Anwendungs-ID für die in Schritt 2 erstellte App an.

4. Führen Sie je nach Hauptknotenkonfiguration eine der folgenden Aktionen aus:

    * In einem HPC Pack-Cluster mit einem einzelnen Hauptknoten starten Sie den Dienst „HpcScheduler“ neu.

    * In einem HPC Pack-Cluster mit mehreren Hauptknoten führen Sie die folgenden PowerShell-Befehle auf dem Hauptknoten aus, um den Dienst „HpcSchedulerStateful“ neu zu starten:

    ```powershell
    Connect-ServiceFabricCluster

    Move-ServiceFabricPrimaryReplica –ServiceName "fabric:/HpcApplication/SchedulerStatefulService"

    ```

## <a name="step-4-manage-and-submit-jobs-from-the-client"></a>Schritt 4: Verwalten und Übermitteln von Aufträgen vom Client aus

Wenn Sie die HPC Pack-Clienthilfsprogramme auf Ihrem Computer installieren möchten, laden Sie die HPC Pack 2016-Setupdateien (vollständige Installation) aus dem Microsoft Download Center herunter. Wenn Sie die Installation starten, wählen Sie die Setupoption für die **HPC Pack-Clienthilfsprogramme** aus.

Um den Clientcomputer vorzubereiten, installieren Sie das Zertifikat, das auf dem Clientcomputer während der [Einrichtung des HPC-Clusters](hpcpack-2016-cluster.md) verwendet wurde. Verwenden Sie die standardmäßigen Verfahren der Windows-Zertifikatverwaltung, um das öffentliche Zertifikat im Speicher **Zertifikate – Aktueller Benutzer** > **Vertrauenswürdige Stammzertifizierungsstellen** zu installieren. 

Sie können nun die HPC Pack-Befehle ausführen oder die Benutzeroberfläche des HPC Pack-Auftrags-Managers verwenden, um Clusteraufträge mithilfe des Azure AD-Kontos zu übermitteln und zu verwalten. Weitere Informationen zu den Optionen für die Auftragsübermittlung finden Sie unter [Übermitteln von HPC-Aufträgen von einem lokalen Computer an einen in Azure bereitgestellten HPC Pack-Cluster](hpcpack-cluster-submit-jobs.md#step-3-run-test-jobs-on-the-cluster).

> [!NOTE]
> Wenn Sie sich erstmals mit einem HPC Pack-Cluster in Azure verbinden, wird ein Popupfenster angezeigt. Geben Sie Ihre Azure AD-Anmeldeinformationen ein, um sich anzumelden. Das Token wird dann zwischengespeichert. Für spätere Verbindungen mit dem Cluster in Azure wird dann das zwischengespeicherte Token verwendet, sofern keine Änderungen an der Authentifizierung vorgenommen werden oder der Zwischenspeicher gelöscht wird.
>
  
Nach Ausführung der vorherigen Schritte können Sie z.B. folgendermaßen Aufträge von einem lokalen Client abfragen:

```powershell 
Get-HpcJob –State All –Scheduler https://<Azure load balancer DNS name> -Owner <Azure AD account>
```

## <a name="useful-cmdlets-for-job-submission-with-azure-ad-integration"></a>Nützliche Cmdlets für die Auftragsübermittlung mit der Azure AD-Integration 

### <a name="manage-the-local-token-cache"></a>Verwalten des lokalen Tokencaches

HPC Pack 2016 bietet die folgenden HPC PowerShell-Cmdlets für die Verwaltung des lokalen Tokencaches. Diese Cmdlets sind für die nicht interaktive Auftragsübermittlung nützlich. Siehe folgendes Beispiel:

```powershell
Remove-HpcTokenCache

$SecurePassword = "<password>" | ConvertTo-SecureString -AsPlainText -Force

Set-HpcTokenCache -UserName <AADUsername> -Password $SecurePassword -scheduler https://<Azure load balancer DNS name> 
```

### <a name="set-the-credentials-for-submitting-jobs-using-the-azure-ad-account"></a>Festlegen der Anmeldeinformationen für die Auftragsübermittlung mithilfe des Azure AD-Kontos 

Manchmal möchten Sie den Auftrag möglicherweise unter dem HPC-Clusterbenutzer ausführen. (Führen Sie den Auftrag für einen in eine Domäne eingebundenen HPC-Cluster als einzelner Domänenbenutzer und für einen nicht in eine Domäne eingebundenen HPC-Cluster als einzelner lokaler Benutzer auf dem Hauptknoten aus.)

1. Nutzen Sie die folgenden Befehle, um die Anmeldeinformationen festzulegen:

    ```powershell
    $localUser = "<username>"

    $localUserPassword="<password>"

    $secpasswd = ConvertTo-SecureString $localUserPassword -AsPlainText -Force

    $mycreds = New-Object System.Management.Automation.PSCredential ($localUser, $secpasswd)

    Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name>
    ```

2. Übermitteln Sie den Auftrag dann wie folgt. Der Auftrag/die Aufgabe wird unter $localUser auf den Computeknoten ausgeführt.

    ```powershell
    $emptycreds = New-Object System.Management.Automation.PSCredential ($localUser, (new-object System.Security.SecureString))
    ...
    $job = New-HpcJob –Scheduler https://<Azure load balancer DNS name>

    Add-HpcTask -Job $job -CommandLine "ping localhost" -Scheduler https://<Azure load balancer DNS name>

    Submit-HpcJob -Job $job -Scheduler https://<Azure load balancer DNS name> -Credential $emptycreds
    ```
    
   Falls `–Credential` nicht mit `Submit-HpcJob` angegeben wurde, wird der Auftrag oder die Aufgabe mit einem lokal zugeordneten Benutzer als Azure AD-Konto ausgeführt. (Der HPC-Cluster erstellt zum Ausführen der Aufgabe einen lokalen Benutzer, der den gleichen Namen aufweist wie das Azure AD-Konto.)
    
3. Legen Sie erweiterte Daten für das Azure AD-Konto fest. Dies ist hilfreich, wenn Sie einen MPI-Auftrag auf Linux-Knoten unter Verwendung des Azure AD-Kontos ausführen.

   * Festlegen erweiterter Daten für das eigentliche Azure AD-Konto:

      ```powershell
      Set-HpcJobCredential -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data> -AadUser
      ```
      
   * Festlegen erweiterter Daten und Ausführen als HPC-Clusterbenutzer:
   
      ```powershell
      Set-HpcJobCredential -Credential $mycreds -Scheduler https://<Azure load balancer DNS name> -ExtendedData <data>
      ```

