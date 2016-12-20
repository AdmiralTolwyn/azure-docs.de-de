---
title: Zertifikatobjekte in Azure Automation | Microsoft Docs
description: "Zertifikate können sicher in Azure Automation gespeichert werden, sodass sie von Runbooks oder DSC-Konfigurationen zur Authentifizierung bei Azure und Drittanbieterressourcen verwendet werden können.  Dieser Artikel stellt eine ausführliche Beschreibung von Zertifikaten bereit und zeigt, wie diese in Textrunbooks und grafischen Runbooks eingesetzt werden."
services: automation
documentationcenter: 
author: mgoedtel
manager: stevenka
editor: tysonn
ms.assetid: ac9c22ae-501f-42b9-9543-ac841cf2cc36
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: magoedte;bwren
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1973a3523e121414dfbebf4d00cd2d4fe2005d2f


---
# <a name="certificate-assets-in-azure-automation"></a>Zertifikatobjekte in Azure Automation
Zertifikate können sicher in Azure Automation gespeichert werden, sodass Sie aus Runbooks oder DSC-Konfigurationen mithilfe der Aktivität **Get-AutomationCertificate** darauf zugreifen können. Auf diese Weise können Sie Runbooks und DSC-Konfigurationen erstellen, die Zertifikate für die Authentifizierung verwenden oder diese zu Azure oder zu Drittanbieterressourcen hinzufügen.

> [!NOTE]
> Zu den sicheren Objekten in Azure Automation gehören Anmeldeinformationen, Zertifikate, Verbindungen und verschlüsselte Variablen. Diese Objekte werden mithilfe eines eindeutigen Schlüssels verschlüsselt und in Azure Automation gespeichert, der für jedes Automation-Konto generiert wird. Dieser Schlüssel wird mit einem Masterzertifikat verschlüsselt und in Azure Automation gespeichert. Vor dem Speichern eines sicheren Objekts wird der Schlüssel für das Automation-Konto mit dem Masterzertifikat verschlüsselt und anschließend zum Verschlüsseln des Objekts verwendet.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets
Die Cmdlets in der folgenden Tabelle werden zum Erstellen und Verwalten von Automation-Variablen mit Windows PowerShell verwendet. Sie gehören zum Lieferumfang des [Azure PowerShell-Moduls](../powershell-install-configure.md) , das zur Verwendung in Automation-Runbooks und DSC-Konfigurationen verfügbar ist.

| Cmdlets | Beschreibung |
|:--- |:--- |
| [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) |Ruft Informationen zu einem Zertifikat ab. Sie können mithilfe der Aktivität "Get-AutomationCertificate" nur das Zertifikat selbst abrufen. |
| [New- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913764.aspx) |Importiert ein neues Zertifikat in Azure Automation. |
| [Remove- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913773.aspx) |Entfernt ein Zertifikat aus Azure Automation. |
| [Set- AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913763.aspx) |Legt die Eigenschaften für ein vorhandenes Zertifikat fest, lädt die Zertifikatdatei hoch und legt das Kennwort für eine PFX-Datei fest. |

## <a name="activities-to-access-certificates"></a>Aktivitäten zum Zugreifen auf Zertifikate
Die Aktivitäten in der folgenden Tabelle werden für den Zugriff auf Zertifikate in einem Runbook oder einer DSC-Konfiguration verwendet.

| Aktivitäten | Beschreibung |
|:--- |:--- |
| Get-AutomationCertificate |Ruft ein Zertifikat zur Verwendung in einem Runbook oder einer DSC-Konfiguration ab. |

> [!NOTE]
> Vermeiden Sie die Verwendung von Variablen im Parameter „-Name“ von „Get-AutomationCertificate“, da dies die Ermittlung von Abhängigkeiten zwischen Runbooks oder DSC-Konfigurationen und Zertifikatobjekten zur Entwurfszeit erschweren kann.
> 
> 

## <a name="creating-a-new-certificate"></a>Erstellen eines neues Zertifikats
Wenn Sie ein neues Zertifikat erstellen, laden Sie eine CER- oder PFX-Datei in Azure Automation hoch. Wenn Sie das Zertifikat als exportierbar kennzeichnen, können Sie es aus dem Azure Automation-Zertifikatspeicher übertragen. Ist das Zertifikat nicht exportierbar, können Sie es nur zum Signieren innerhalb des Runbooks oder der DSC-Konfiguration verwenden.

### <a name="to-create-a-new-certificate-with-the-azure-classic-portal"></a>So erstellen Sie ein neues Zertifikat mit dem klassischen Azure-Portal
1. Klicken Sie in Ihrem Automation-Konto im oberen Fensterbereich auf **Objekte** .
2. Klicken Sie unten im Fenster auf **Einstellung hinzufügen**.
3. Klicken Sie auf **Anmeldeinformationen hinzufügen**.
4. Wählen Sie in der Dropdownliste **Anmeldeinformationstyp** die Option **Zertifikat**.
5. Geben Sie im Feld **Name** einen Namen für das Zertifikat ein, und klicken Sie auf den nach rechts weisenden Pfeil.
6. Suchen Sie nach einer CER- oder PFX-Datei.  Wenn Sie eine PFX-Datei auswählen, geben Sie ein Kennwort an, und legen Sie fest, ob das Zertifikat exportiert werden kann.
7. Aktivieren Sie das Kontrollkästchen, um die Zertifikatdatei hochzuladen und das neue Zertifikatobjekt zu speichern.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>So erstellen Sie ein neues Zertifikat mit dem Azure-Portal
1. Klicken Sie in Ihrem Automation-Konto auf **Objekte**, um das Blatt **Objekte** zu öffnen.
2. Klicken Sie auf **Zertifikate**, um das Blatt **Zertifikate** zu öffnen.
3. Klicken Sie oben im Blatt auf **Zertifikat hinzufügen** .
4. Geben Sie im Feld **Name** einen Namen für das Zertifikat ein.
5. Klicken Sie unterhalb von **Zertifikatdatei hochladen** auf **Datei auswählen**, um nach einer CER- oder PFX-Datei zu suchen.  Wenn Sie eine PFX-Datei auswählen, geben Sie ein Kennwort an, und legen Sie fest, ob das Zertifikat exportiert werden kann.
6. Klicken Sie auf **Erstellen** , um das neue Zertifikatobjekt zu speichern.

### <a name="to-create-a-new-certificate-with-windows-powershell"></a>So erstellen Sie ein neues Zertifikat mit Windows PowerShell
Die folgenden Beispielbefehle zeigen, wie Sie ein neues Automation-Zertifikat erstellen und es als exportierbar kennzeichnen. In diesem Beispiel wird eine vorhandene PFX-Datei importiert.

    $certName = 'MyCertificate'
    $certPath = '.\MyCert.pfx'
    $certPwd = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force

    New-AzureAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certName -Path $certPath –Password $certPwd -Exportable

## <a name="using-a-certificate"></a>Verwenden eines Zertifikats
Sie müssen die Aktivität **Get-AutomationCertificate** verwenden, um ein Zertifikat zu verwenden. Eine Verwendung des Cmdlets [Get-AzureAutomationCertificate](http://msdn.microsoft.com/library/dn913765.aspx) ist nicht möglich, da dieses Cmdlet Informationen zum Zertifikatobjekt, aber nicht zum Zertifikat selbst zurückgibt.

### <a name="textual-runbook-sample"></a>Beispiel für ein Textrunbook
Der folgende Beispielcode zeigt, wie Sie ein Zertifikat zu einem Clouddienst in einem Runbook hinzufügen. In diesem Beispiel wird das Kennwort aus einer verschlüsselten Automation-Variable abgerufen.

    $serviceName = 'MyCloudService'
    $cert = Get-AutomationCertificate -Name 'MyCertificate'
    $certPwd = Get-AutomationVariable –Name 'MyCertPassword'
    Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert

### <a name="graphical-runbook-sample"></a>Beispiel für ein grafisches Runbook
Sie können einem grafischen Runbook **Get-AutomationCertificate** hinzufügen, indem Sie im Bibliotheksbereich des grafischen Editors mit der rechten Maustaste auf das Zertifikat klicken und **Zum Zeichenbereich hinzufügen** auswählen.

![](media/automation-certificates/certificate-add-canvas.png)

Die folgende Abbildung zeigt ein Beispiel für die Verwendung eines Zertifikats in einem grafischen Runbook.  Es handelt sich um das oben gezeigte Beispiel zum Hinzufügen eines Zertifikats zu einem Clouddienst aus einem Textrunbook.  

In diesem Beispiel wird der Parametersatz **UseConnectionObject** für die **Send-TwilioSMS**-Aktivität verwendet, die ein Verbindungsobjekt zur Authentifizierung beim Dienst nutzt.  Hier muss eine [Pipelineverknüpfung](automation-graphical-authoring-intro.md#links-and-workflow) verwendet werden, da eine Sequenzverknüpfung eine Auflistung mit einem einzelnen Objekt zurückgeben würde und dies nicht vom Connection-Parameter erwartet wird.

![](media/automation-certificates/add-certificate.png)

## <a name="see-also"></a>Siehe auch
* [Verknüpfungen bei der grafischen Erstellung](automation-graphical-authoring-intro.md#links-and-workflow) 




<!--HONumber=Nov16_HO3-->


