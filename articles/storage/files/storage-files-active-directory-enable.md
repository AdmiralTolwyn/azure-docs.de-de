---
title: Verwenden von Azure AD Domain Services zum Autorisieren des Zugriffs auf Dateidaten über SMB
description: Erfahren Sie, wie Sie eine identitätsbasierte Authentifizierung über SMB (Server Message Block) für Azure Files über Azure Active Directory Domain Services aktivieren. Ihre in die Domäne eingebundenen virtuellen Windows-Computer (VMs) können dann mit Azure AD-Anmeldeinformationen auf Azure-Dateifreigaben zugreifen.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 489cb9e652d571b5322a1bd92663ca089e28b8cd
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980793"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>Aktivieren der Azure Active Directory Domain Services-Authentifizierung über SMB für Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Eine Übersicht über die Azure AD-Authentifizierung über SMB für Azure Files finden Sie unter [AAD DS-Authentifizierung über SMB für Azure Files – Übersicht](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Übersicht über den Workflow

Bevor Sie Azure AD DS-Authentifizierung über SMB für Azure Files aktivieren, vergewissern Sie sich, dass Ihre Azure AD- und Azure Storage-Umgebungen ordnungsgemäß konfiguriert sind. Es wird empfohlen, dass Sie die [Voraussetzungen](#prerequisites) durchgehen, um sicherzustellen, dass Sie alle erforderlichen Schritte ausgeführt haben.

Als nächstes gewähren Sie Zugriff auf Azure Files-Ressourcen mit Azure AD-Anmeldeinformationen, indem Sie die folgenden Schritte ausführen:

1. Aktivieren Sie die Azure AD DS-Authentifizierung über SMB für Ihr Speicherkonto, um das Speicherkonto bei der zugehörigen Bereitstellung von Azure AD DS zu registrieren.
2. Weisen Sie einer Azure AD-Identität (Benutzer, Gruppe oder Dienstprinzipal) Zugriffsberechtigungen für eine Freigabe zu.
3. Konfigurieren Sie NTFS-Berechtigungen über SMB für Verzeichnisse und Dateien.
4. Stellen Sie eine Azure-Dateifreigabe von einem domänengebundenen virtuellen Computer bereit.

Das folgende Diagramm zeigt den vollständigen Workflow zur Aktivierung der Azure AD DS-Authentifizierung über SMB für Azure Files.

![Diagramm mit Azure AD über SMB für Azure Files-Workflow](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie Azure AD über SMB für Azure Files aktivieren, sollten Sie sicherstellen, dass Sie die folgenden Voraussetzungen erfüllt haben:

1.  **Wählen Sie einen Azure AD-Mandanten aus oder erstellen Sie einen.**

    Sie können einen neuen oder vorhandenen Mandanten für die Azure AD-Authentifizierung über SMB verwenden. Der Mandant und die Dateifreigabe, auf die Sie zugreifen möchten, müssen dem gleichen Abonnement zugeordnet sein.

    Zum Erstellen eines neuen Azure AD-Mandanten können Sie [einen Azure AD-Mandanten und ein Azure AD-Abonnement hinzufügen](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Wenn Sie über einen vorhandenen Azure AD-Mandanten verfügen, aber einen neuen Mandanten für die Verwendung mit Azure Files erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines Azure Active Directory-Mandanten](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Aktivieren Sie Azure AD Domain Services auf dem Azure AD-Mandanten.**

    Zur Unterstützung der Authentifizierung mit Azure AD-Anmeldeinformationen müssen Sie Azure AD Domain Services für Ihren Azure AD-Mandanten aktivieren. Wenn Sie nicht der Administrator des Azure AD-Mandanten sind, wenden Sie sich an den Administrator und folgen Sie der schrittweisen Anleitung zum [Aktivieren von Azure Active Directory Domain Services über das Azure-Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Es dauert in der Regel etwa 15 Minuten, bis eine Bereitstellung von Azure AD DS abgeschlossen ist. Vergewissern Sie sich, dass der Integritätsstatus von Azure AD DS bei aktivierter Kennworthashsynchronisierung **Wird ausgeführt** anzeigt, bevor Sie mit dem nächsten Schritt fortfahren.

3.  **Domänenbeitritt einer Azure-VM mit Azure AD DS.**

    Damit Sie mit Azure AD-Anmeldeinformationen von einem virtuellen Computer auf eine Dateifreigabe zugreifen können, muss Ihr virtueller Computer in Azure AD DS in eine Domäne eingebunden sein. Weitere Informationen zum Einbinden in die Domäne eines virtuellen Computers finden Sie unter [Einbinden eines virtuellen Windows Server-Computers in eine verwaltete Domäne](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Die Azure AD DS-Authentifizierung über SMB mit Azure Files wird nur auf virtuellen Azure-Computern unterstützt, die auf Betriebssystemversionen über Windows 7 oder Windows Server 2008 R2 ausgeführt werden.

4.  **Wählen oder erstellen Sie eine Azure-Dateifreigabe.**

    Wählen Sie eine neue oder vorhandene Dateifreigabe aus, die mit demselben Abonnement wie Ihr Azure AD-Mandant verbunden ist. Weitere Informationen zum Erstellen einer neuen Dateifreigabe finden Sie unter [Erstellen einer Dateifreigabe in Azure Files](storage-how-to-create-file-share.md).
    Für eine optimale Leistung wird empfohlen, dass sich Ihre Dateifreigabe in derselben Region befindet wie der virtuelle Computer, von dem aus Sie auf die Freigabe zugreifen möchten.

5.  **Überprüfen Sie die Konnektivität von Azure Files, indem Sie Azure-Dateifreigaben mit Ihrem Speicherkontenschlüssel einbinden.**

    Um sicherzustellen, dass Ihr virtueller Computer und die Dateifreigabe richtig konfiguriert sind, versuchen Sie, die Dateifreigabe mit Ihrem Speicherkontenschlüssel einzubinden. Weitere Informationen finden Sie unter [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Aktivieren der Azure AD DS-Authentifizierung für Ihr Konto

Zur Aktivierung der Azure AD DS-Authentifizierung über SMB für Azure Files können Sie eine Eigenschaft für Speicherkonten festlegen, die nach dem 24. September 2018 mit dem Azure-Portal, Azure PowerShell oder der Azure-Befehlszeilenschnittstelle erstellt wurden. Wenn Sie diese Eigenschaft festlegen, wird das Speicherkonto bei der zugehörigen Bereitstellung von Azure AD DS registriert. Die Azure AD DS-Authentifizierung über SMB ist dann für alle neuen und vorhandenen Dateifreigaben im Speicherkonto aktiviert.

Beachten Sie, dass Sie die Azure AD DS-Authentifizierung über SMB erst dann aktivieren können, wenn Sie Azure AD DS erfolgreich in Ihrem Azure AD-Mandanten bereitgestellt haben. Weitere Informationen finden Sie unter [Voraussetzungen](#prerequisites).

### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um die Azure AD DS-Authentifizierung über SMB über das [Azure-Portal](https://portal.azure.com) zu aktivieren:

1. Navigieren Sie im Azure-Portal zu Ihrem vorhandenen Speicherkonto, oder [erstellen Sie ein Speicherkonto](../common/storage-account-create.md).
2. Wählen Sie im Abschnitt **Einstellungen** die Option **Konfiguration**.
3. Wählen Sie in der Dropdownliste **Identity-based Directory Service for Azure File Authentication** (Identitätsbasierter Verzeichnisdienst für Azure Files-Authentifizierung) die Option **Azure Active Directory Domain Services (AADDS)** aus.

In der folgenden Abbildung ist dargestellt, wie Sie die Azure AD DS-Authentifizierung über SMB für Ihr Speicherkonto aktivieren.

![Aktivieren der Azure AD-Authentifizierung über SMB im Azure-Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Um die Azure AD DS-Authentifizierung über SMB mit Azure PowerShell zu aktivieren, installieren Sie das neueste Az-Modul (2.4 oder höher) oder das Az.Storage-Modul (1.5 oder höher). Weitere Informationen zum Installieren von PowerShell finden Sie unter [Installieren von Azure PowerShell unter Windows mit PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Rufen Sie zum Erstellen eines neuen Speicherkontos [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0) auf, und legen Sie dann den Parameter **EnableAzureActiveDirectoryDomainServicesForFile** auf **true** fest. Denken Sie im folgenden Beispiel daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen. (Wenn Sie das vorherige Vorschaumodul verwendet haben, lautet der Parameter für die Funktionsaktivierung **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Verwenden Sie den folgenden Befehl, um diese Funktion für vorhandene Speicherkonten zu aktivieren:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Wenn Sie die Azure AD-Authentifizierung über SMB mithilfe der Azure-Befehlszeilenschnittstelle aktivieren möchten, installieren Sie die neueste Version der Befehlszeilenschnittstelle (Version 2.0.70 oder höher). Weitere Informationen zum Installieren der Azure-Befehlszeilenschnittstelle finden Sie unter [Installieren der Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Rufen Sie zum Erstellen eines neuen Speicherkontos [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) auf, und legen Sie die Eigenschaft `--enable-files-aadds` auf **true** fest. Denken Sie im folgenden Beispiel daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen. (Wenn Sie das vorherige Vorschaumodul verwendet haben, lautet der Parameter für die Featureaktivierung **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Verwenden Sie den folgenden Befehl, um diese Funktion für vorhandene Speicherkonten zu aktivieren:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Zuweisen von Zugriffsberechtigungen zu einer Identität

Für den Zugriff auf Ressourcen von Azure Files mit Azure AD-Anmeldeinformationen muss die Identität (Benutzer, Gruppe oder Dienstprinzipal) über die erforderlichen Berechtigungen auf der Freigabeebene verfügen. Dieser Prozess ähnelt der Angabe von Windows-Freigabeberechtigungen, bei denen Sie die Art des Zugriffs eines bestimmten Benutzers auf eine Dateifreigabe angeben. Die Anleitung in diesem Abschnitt zeigt, wie Sie einer Identität Lese-, Schreib- oder Löschberechtigungen für eine Dateifreigabe zuweisen.

Wir haben zwei integrierte Azure-Rollen zum Gewähren von Berechtigungen auf Freigabeebene für Benutzer eingeführt:

- **Speicherdateidaten-SMB-Freigabeleser** ermöglicht den Lesezugriff auf Azure Storage-Dateifreigaben über SMB.
- **Speicherdateidaten-SMB-Freigabemitwirkender** ermöglicht den Lese-, Schreib- und Löschzugriff auf Azure Storage-Dateifreigaben über SMB.
- **Speicherdateidaten-SMB-Freigabemitwirkender mit erhöhten Rechten** gewährt Lese-, Schreib-, Lösch- und Änderungs-NTFS-Berechtigungen auf Azure Storage-Dateifreigaben über SMB.

> [!IMPORTANT]
> Die vollständige administrative Kontrolle über eine Dateifreigabe, einschließlich der Möglichkeit, einer Identität eine Rolle zuzuweisen, erfordert die Verwendung des Speicherkontenschlüssels. Die administrative Kontrolle wird mit Azure AD-Anmeldeinformationen nicht unterstützt.

Sie können das Azure-Portal, PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, um die integrierten Rollen der Azure AD-Identität eines Benutzers zuzuweisen und damit Berechtigungen auf Freigabeebene zu erteilen.

#### <a name="azure-portal"></a>Azure-Portal
Um einer Azure AD-Identität eine RBAC-Rolle zuzuweisen, führen Sie im [Azure-Portal](https://portal.azure.com) die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrer Dateifreigabe, oder [erstellen Sie eine Dateifreigabe in Azure Files](storage-how-to-create-file-share.md).
2. Wählen Sie **Access Control (IAM)** aus.
3. Auswählen von **Rollenzuweisung hinzufügen**
4. Wählen Sie auf dem Blatt **Rollenzuweisung hinzufügen** in der Liste **Rolle** die entsprechende integrierte Rolle aus (Speicherdateidaten-SMB-Freigabeleser, Speicherdateidaten-SMB-Freigabemitwirkender). Behalten Sie für die Option **Zugriff zuweisen zu** die Standardeinstellung bei: **Azure AD-Benutzer, -Gruppe oder -Dienstprinzipal**. Wählen Sie die Azure AD-Zielidentität anhand des Namens oder der E-Mail-Adresse aus.
5. Wählen Sie abschließend **Speichern** aus, um den Vorgang der Rollenzuweisung abzuschließen.

#### <a name="powershell"></a>PowerShell

Der folgende PowerShell-Befehl zeigt, wie einer Azure AD-Identität basierend auf dem Anmeldenamen eine RBAC-Rolle zugewiesen wird. Weitere Informationen zum Zuweisen von RBAC-Rollen mit PowerShell finden Sie unter [Verwalten des Zugriffs mit RBAC und Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Denken Sie vor dem Ausführen des folgenden Beispielskripts daran, Platzhalterwerte durch eigene Werte zu ersetzen (einschließlich der Klammern).

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Befehlszeilenschnittstelle (CLI)

Der folgende CLI 2.0-Befehl zeigt, wie einer Azure AD-Identität basierend auf dem Anmeldenamen eine RBAC-Rolle zugewiesen wird. Weitere Informationen zum Zuweisen von RBAC-Rollen mithilfe der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwalten des Zugriffs mit RBAC und der Azure-Befehlszeilenschnittstelle](../../role-based-access-control/role-assignments-cli.md).

Denken Sie vor dem Ausführen des folgenden Beispielskripts daran, Platzhalterwerte durch eigene Werte zu ersetzen (einschließlich der Klammern).

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurieren von NTFS-Berechtigungen über SMB
Nachdem Sie mit RBAC Berechtigungen auf Freigabeebene zugewiesen haben, müssen Sie die richtigen NTFS-Berechtigungen auf Stamm-, Verzeichnis- oder Dateiebene zuweisen. Stellen Sie sich die Berechtigungen auf der Freigabeebene als allgemeinen Gatekeeper vor, der festlegt, ob ein Benutzer auf die Freigabe zugreifen kann. Die NTFS-Berechtigungen agieren hingegen auf einer detaillierteren Ebene und legen fest, welche Vorgänge der Benutzer auf der Verzeichnis- oder Dateiebene ausführen kann.

Azure Files unterstützt den vollständigen Satz an grundlegenden und erweiterten NTFS-Berechtigungen. Sie können NTFS-Berechtigungen für Verzeichnisse und Dateien in einer Azure-Dateifreigabe anzeigen und konfigurieren, indem Sie die Freigabe einbinden und dann den Windows-Datei-Explorer verwenden oder den Windows-Befehl [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) oder [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) ausführen.

Um NTFS mit Superuser-Berechtigungen zu konfigurieren, müssen Sie die Freigabe mit Ihrem Speicherkontenschlüssel von Ihrem domänengebundenen virtuellen Computer einbinden. Befolgen Sie die Anweisungen im nächsten Abschnitt, um eine Azure-Dateifreigabe über die Eingabeaufforderung einzubinden und die NTFS-Berechtigungen entsprechend zu konfigurieren.

Die folgenden Berechtigungsgruppen werden im Stammverzeichnis einer Dateifreigabe unterstützt:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Bereitstellen einer Dateifreigabe über die Eingabeaufforderung

Verwenden Sie den Windows-Befehl **net use**, um die Azure-Dateifreigabe zu aktivieren. Denken Sie daran, die Platzhalterwerte im folgenden Beispiel durch Ihre eigenen Werte zu ersetzen. Weitere Informationen zum Einbinden von Dateifreigaben finden Sie unter [Einbinden einer Azure-Dateifreigabe und Zugreifen auf die Freigabe unter Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurieren von NTFS-Berechtigungen mit dem Windows-Datei-Explorer
Verwenden Sie den Windows-Datei-Explorer, um allen Verzeichnissen und Dateien unter der Dateifreigabe, einschließlich des Stammverzeichnisses, vollständige Berechtigungen zu erteilen.

1. Öffnen Sie den Windows-Datei-Explorer, klicken Sie mit der rechten Maustaste auf die Datei bzw. das Verzeichnis, und wählen Sie **Eigenschaften** aus.
2. Klicken Sie auf die Registerkarte **Sicherheit**.
3. Klicken Sie auf die Schaltfläche **Bearbeiten...** , um die Berechtigungen zu ändern.
4. Sie können die Berechtigung bereits vorhandener Benutzer ändern oder auf **Hinzufügen...** klicken, um neuen Benutzern Berechtigungen zu erteilen.
5. Geben Sie im Eingabeaufforderungsfenster zum Hinzufügen neuer Benutzer im Feld **Geben Sie die Namen der auszuwählenden Objekte ein** den Namen des Zielbenutzers ein, dem Sie die Berechtigung erteilen möchten, und klicken Sie auf **Namen überprüfen**, um den vollständigen UPN-Namen des Zielbenutzers zu ermitteln.
7.  Klicken Sie im Menü „Einstellungen“ auf **OK**
8.  Wählen Sie auf der Registerkarte „Sicherheit“ alle Berechtigungen aus, die Sie dem neu hinzugefügten Benutzer erteilen möchten.
9.  Klicken Sie auf **Anwenden**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurieren von NTFS-Berechtigungen mit icacls
Verwenden Sie den folgenden Windows-Befehl, um allen Verzeichnissen und Dateien unter der Dateifreigabe, einschließlich des Stammverzeichnisses, vollständige Berechtigungen zu erteilen. Denken Sie daran, die Platzhalterwerte in diesem Beispiel durch Ihre eigenen Werte zu ersetzen.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Weitere Informationen zur Verwendung von icacls zum Festlegen von NTFS-Berechtigungen und zu den verschiedenen Arten von unterstützten Berechtigungen finden Sie in der [Befehlszeilenreferenz für icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Bereitstellen einer Dateifreigabe von einem in eine Domäne eingebundenen virtuellen Computer

Mit dem folgenden Prozess wird überprüft, ob Ihre Azure AD-Anmeldeinformationen ordnungsgemäß eingerichtet wurden und Sie von einem in die Domäne eingebundenen virtuellen Computer aus Zugriff auf eine Azure-Dateifreigabe haben:

Melden Sie sich mit der Azure AD-Identität, für die Sie Berechtigungen erteilt haben, beim virtuellen Computer an, wie in der folgenden Abbildung gezeigt.

![Screenshot mit dem Azure AD-Anmeldebildschirm für die Benutzerauthentifizierung](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Verwenden Sie den folgenden Befehl, um die Azure-Dateifreigabe einzubinden. Denken Sie daran, die Platzhalterwerte durch Ihre eigenen Werte zu ersetzen. Da Sie bereits authentifiziert wurden, müssen Sie weder den Schlüssel des Speicherkontos noch den Benutzernamen und das Kennwort von Azure AD angeben. Azure AD über SMB unterstützt das einmalige Anmelden mit Azure AD-Anmeldeinformationen.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Sie haben jetzt erfolgreich die Azure AD-Authentifizierung über SMB aktiviert und eine benutzerdefinierte Rolle zugewiesen, die den Zugriff auf eine Azure-Dateifreigabe mit einer Azure AD-Identität ermöglicht. Befolgen Sie die Anweisungen in den Abschnitten [Zuweisen von Zugriffsberechtigungen zu einer Identität](#assign-access-permissions-to-an-identity) und [Konfigurieren von NTFS-Berechtigungen über SMB](#configure-ntfs-permissions-over-smb), um weiteren Benutzern den Zugriff auf Ihre Dateifreigabe zu gewähren.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Files und zur Verwendung von Azure AD über SMB finden Sie in den folgenden Ressourcen:

- [Einführung in Azure Files](storage-files-introduction.md)
- [AAD DS-Authentifizierung über SMB für Azure Files – Übersicht](storage-files-active-directory-overview.md)
- [Häufig gestellte Fragen](storage-files-faq.md)
