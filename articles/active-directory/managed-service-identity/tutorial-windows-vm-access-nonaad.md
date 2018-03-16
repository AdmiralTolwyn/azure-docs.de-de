---
title: "Verwenden einer Windows-VM-MSI für den Zugriff auf Azure Key Vault"
description: "Dieses Tutorial erläutert, wie Sie eine Windows-VM-MSI (Managed Service Identity, verwaltete Dienstidentität) verwenden, um auf Azure Key Vault zuzugreifen."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 3c1f41f407dc85eac40d1aa545c588426db6a382
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/08/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Verwenden einer Windows-VM-MSI (Managed Service Identity, verwaltete Dienstidentität) für den Zugriff auf Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine verwaltete Dienstidentität (Managed Service Identity, MSI) für einen virtuellen Windows-Computer aktivieren und diese Identität anschließend verwenden, um auf Azure Key Vault zuzugreifen. Key Vault dient als Bootstrap und ermöglicht es Ihrer Clientanwendung, mithilfe des Geheimnisses auf Ressourcen zuzugreifen, die nicht von Azure Active Directory (AD) geschützt sind. Verwaltete Dienstidentitäten werden von Azure automatisch verwaltet und ermöglichen Ihnen die Authentifizierung für Dienste, die die Azure AD-Authentifizierung unterstützen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. 

Folgendes wird vermittelt:


> [!div class="checklist"]
> * Aktivieren einer verwalteten Dienstidentität auf einem virtuellen Windows-Computer 
> * Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer 
> * Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus der Key Vault 

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com) beim Azure-Portal an.

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Erstellen eines virtuellen Windows-Computers in einer neuen Ressourcengruppe

In diesem Tutorial wird ein neuer virtueller Windows-Computer erstellt. Sie können MSI auch auf einem vorhandenen virtuellen Computer aktivieren.

1.  Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **Ressource erstellen**.
2.  Wählen Sie **Compute** und dann **Windows Server 2016 Datacenter**. 
3.  Geben Sie die Informationen zum virtuellen Computer ein. Der **Benutzername** und das **Kennwort**, die hier erstellt werden, sind die Anmeldeinformationen, die Sie für die Anmeldung beim virtuellen Computer verwenden.
4.  Wählen Sie in der Dropdownliste das richtige **Abonnement** für den virtuellen Computer aus.
5.  Um eine neue **Ressourcengruppe** auszuwählen, in der der virtuelle Computer erstellt werden soll, wählen Sie **Neu erstellen** aus. Klicken Sie zum Abschluss auf **OK**.
6.  Wählen Sie eine Größe für den virtuellen Computer. Wählen Sie die Option **Alle anzeigen**, oder ändern Sie den Filter **Supported disk type** (Unterstützter Datenträgertyp), um weitere Größen anzuzeigen. Behalten Sie auf dem Blatt „Einstellungen“ die Standardwerte bei, und klicken Sie auf **OK**.

    ![Alternativer Bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Aktivieren von MSI auf dem virtuellen Computer 

Eine VM-MSI ermöglicht es Ihnen, Zugriffstoken aus Azure AD abzurufen, ohne dass Sie Anmeldeinformationen in Ihren Code einfügen müssen. Durch das Aktivieren von MSI weisen Sie Azure an, eine verwaltete Identität für den virtuellen Computer zu erstellen. Im Hintergrund werden durch das Aktivieren von MSI zwei Vorgänge ausgelöst: Auf dem virtuellen Computer wird die MSI-VM-Erweiterung installiert, und MSI wird im Azure Resource Manager aktiviert.

1.  Wählen Sie den **virtuellen Computer** aus, auf dem Sie MSI aktivieren möchten.  
2.  Klicken Sie in der links angezeigten Navigationsleiste auf **Konfiguration**. 
3.  Die Option **Verwaltete Dienstidenität** wird angezeigt. Wählen Sie zum Registrieren und Aktivieren von MSI die Option **Ja** oder zum Deaktivieren „Nein“. 
4.  Achten Sie darauf, zum Speichern der Konfiguration auf **Speichern** zu klicken.  

    ![Alternativer Bildtext](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Wenn Sie überprüfen möchten, welche Erweiterungen sich auf diesem virtuellen Computer befinden, klicken Sie auf **Erweiterungen**. Wenn MSI aktiviert ist, wird **ManagedIdentityExtensionforWindows** in der Liste angezeigt.

    ![Alternativer Bildtext](../media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Gewähren des Zugriffs auf ein in einer Key Vault gespeicherten Geheimnisses für den virtuellen Computer 
 
Mithilfe von MSI kann der Code Zugriffstoken zur Authentifizierung von Ressourcen abrufen, die die Azure AD-Authentifizierung unterstützen.  Allerdings unterstützen nicht alle Azure-Dienste die Azure AD-Authentifizierung. Um MSI mit diesen Diensten zu verwenden, speichern Sie die Anmeldeinformationen für den Dienst in Azure Key Vault, und greifen Sie mit MSI auf Key Vault zu, um die Anmeldeinformationen abzurufen. 

Zunächst müssen Sie eine Key Vault erstellen und der Identität des virtuellen Computers den Zugriff darauf erteilen.   

1. Wählen Sie oben in der linken Navigationsleiste **Ressource erstellen** > **Sicherheit + Identität** > **Key Vault** aus.  
2. Geben Sie unter **Name** einen Namen für die neue Key Vault an. 
3. Suchen Sie die Key Vault in dem Abonnement und in der Ressourcengruppe, in der sich auch der zuvor erstellte virtuelle Computer befindet. 
4. Wählen Sie **Zugriffsrichtlinien** aus, und klicken Sie auf **Neue hinzufügen**. 
5. Wählen Sie unter „Anhand einer Vorlage konfigurieren“ die Option **Verwaltung von Geheimnissen** aus. 
6. Wählen Sie **Prinzipal auswählen**, und geben Sie im Suchfeld den Namen des zuvor erstellten virtuellen Computers ein.  Wählen Sie in der Ergebnisliste den virtuellen Computer aus, und klicken Sie auf **Auswählen**. 
7. Klicken Sie auf **OK**, um die neue Zugriffsrichtlinie hinzuzufügen. Klicken Sie dann erneut auf **OK**, um die Auswahl der Zugriffsrichtlinie abzuschließen. 
8. Klicken Sie auf **Erstellen**, um die Key Vault zu erstellen. 

    ![Alternativer Bildtext](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Fügen Sie der Key Vault nun ein Geheimnis hinzu, das Sie später mithilfe von Code abrufen können, der auf dem virtuellen Computer ausgeführt wird: 

1. Wählen Sie **Alle Ressourcen** aus, suchen Sie den erstellten Schlüsseltresor, und wählen Sie diesen aus. 
2. Wählen Sie **Geheimnisse** aus, und klicken Sie auf **Hinzufügen**. 
3. Wählen Sie unter **Uploadoptionen** die Option **Manuell** aus. 
4. Geben Sie einen Namen und einen Wert für das Geheimnis ein.  Dabei kann es sich um einen beliebigen Wert handeln. 
5. Lassen Sie das Aktivierungs- und das Ablaufdatum leer, und übernehmen Sie für **Aktiviert** die ausgewählte Option **Ja**. 
6. Klicken Sie auf **Erstellen**, um das Geheimnis zu erstellen. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Abrufen eines Zugriffstokens mithilfe der VM-Identität und Verwenden dieses Zugriffstokens zum Abrufen des Geheimnisses aus der Key Vault  

Wenn bei Ihnen nicht PowerShell 4.3.1 oder höher installiert ist, müssen Sie [die neueste Version herunterladen und installieren](https://docs.microsoft.com/powershell/azure/overview).

Zunächst wird mit der MSI der VM ein Zugriffstoken für die Authentifizierung bei Key Vault abgerufen:
 
1. Navigieren Sie im Portal zu **Virtuelle Computer**, wechseln Sie dann zu Ihrem virtuellen Windows-Computer, und klicken Sie in der **Übersicht** auf **Verbinden**.
2. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des **rs** hinzugefügt haben.  
3. Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt PowerShell in der Remotesitzung.  
4. Rufen Sie in PowerShell die Webanforderung für den Mandanten auf, um das Token für den lokalen Host am spezifischen Port des virtuellen Computers abzurufen.  

    Die PowerShell-Anforderung:
    
    ```powershell
    PS C:\> $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://vault.azure.net"} -Headers @{Metadata="true"} 
    ```
    
    Extrahieren Sie als Nächstes die vollständige Antwort, die als JSON-formatierte Zeichenfolge (JavaScript Object Notation) im „$response“-Objekt gespeichert ist.  
    
    ```powershell
    PS C:\> $content = $response.Content | ConvertFrom-Json 
    ```
    
    Extrahieren Sie dann das Zugriffstoken aus der Antwort.  
    
    ```powershell
    PS C:\> $KeyVaultToken = $content.access_token 
    ```
    
    Verwenden Sie abschließend den PowerShell-Befehl „Invoke-WebRequest“, um das zuvor in der Key Vault erstellte Geheimnis abzurufen. Übergeben Sie dabei das Zugriffstoken im Autorisierungsheader.  Sie benötigen die URL Ihrer Key Vaults. Diese befindet sich im Abschnitt **Zusammenfassung** der Seite **Übersicht** der Key Vault.  
    
    ```powershell
    PS C:\> (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Die Antwort sieht so aus: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Nachdem Sie das Geheimnis aus der Key Vault abgerufen haben, können Sie es für die Authentifizierung bei einem Dienst verwenden, für den ein Name und ein Kennwort angegeben werden müssen. 

## <a name="related-content"></a>Verwandte Inhalte

- Einen Überblick über MSI finden Sie unter [Übersicht über verwaltete Dienstidentitäten](overview.md).

Verwenden Sie den folgenden Kommentarabschnitt, um uns Feedback zu senden und uns bei der Verbesserung unserer Inhalte zu unterstützen.
