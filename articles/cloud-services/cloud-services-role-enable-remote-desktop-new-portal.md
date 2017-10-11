---
title: "Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services | Microsoft-Dokumentation"
description: "Konfigurieren einer Azure-Clouddienstanwendung für Remotedesktopverbindungen."
services: cloud-services
documentationcenter: 
author: mmccrory
manager: timlt
editor: 
ms.assetid: 73ea1d64-1529-4d72-b58e-f6c10499e6bb
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2016
ms.author: mmccrory
ms.openlocfilehash: 0ff7fde5f3753aa6a24fb0af54d68d0dc0bd96a4
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2017
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services"></a>Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services
> [!div class="op_single_selector"]
> * [Azure-Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Klassisches Azure-Portal](cloud-services-role-enable-remote-desktop.md)
> * [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](../vs-azure-tools-remote-desktop-roles.md)
>
>

Mit Remotedesktop können Sie auf den Desktop einer Rolle zugreifen, die in Azure ausgeführt wird. Mithilfe einer Remotedesktopverbindung können Sie Probleme mit Ihrer Anwendung diagnostizieren und beheben, während diese ausgeführt wird.

Sie können eine Remotedesktopverbindung in Ihrer Rolle während der Entwicklung aktivieren, indem Sie die Remotedesktopmodule in ihre Dienstdefinition aufnehmen, oder Sie können Remotedesktop über die Remotedesktoperweiterung aktivieren. Der bevorzugte Ansatz ist die Verwendung der Remotedesktoperweiterung, da sie Remotedesktop damit auch nach der Bereitstellung der Anwendung aktivieren können, ohne die Anwendung erneut bereitzustellen.

## <a name="configure-remote-desktop-from-the-azure-portal"></a>Konfigurieren von Remotedesktop über das Azure-Portal
Das Azure-Portal ermöglicht die Remotedesktoperweiterung, sodass Sie Remotedesktop auch nach der Bereitstellung der Anwendung aktivieren können. Auf dem Blatt **Remotedesktop** des Clouddiensts können Sie Remotedesktop aktivieren, das lokale Administratorkonto, das zum Herstellen einer Verbindung mit den virtuellen Computern verwendet wird, und das bei der Authentifizierung verwendete Zertifikat ändern und das Ablaufdatum festlegen.

1. Klicken Sie auf **Cloud Services**, auf den Namen des Clouddiensts und dann auf **Remotedesktop**.

    ![Clouddienste – Remotedesktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop.png)

2. Legen Sie fest, ob Sie Remotedesktop für eine einzelne Rolle oder für alle Rollen aktivieren möchten, und ändern Sie dann den Wert des Umschalters in **Aktiviert**.

3. Füllen Sie die erforderlichen Felder für Benutzername, Kennwort, Ablauf und Zertifikat aus.

    ![Clouddienste – Remotedesktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Details.png)

   > [!WARNING]
   > Alle Rolleninstanzen werden neu gestartet, wenn Sie Remotedesktop erstmals aktivieren und auf OK (Häkchen) klicken. Um einen Neustart zu verhindern, muss in der Rolle das Zertifikat installiert sein, mit dem das Kennwort verschlüsselt wird. Zum Verhindern eines Neustarts [laden Sie ein Zertifikat für den Clouddienst hoch](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) und kehren dann zu diesem Dialogfeld zurück.
   >
   >
3. Wählen Sie unter **Rollen** die Rolle aus, die aktualisiert werden soll, oder wählen Sie **Alle** für alle Rollen.

4. Wenn Sie die Konfigurationsupdates beendet haben, klicken Sie auf **Speichern**. Es dauert einige Minuten, bis die Rolleninstanzen Verbindungen empfangen können.

## <a name="remote-into-role-instances"></a>Remotezugriff auf Rolleninstanzen
Nachdem Remotedesktop für die Rollen aktiviert wurde, können Sie direkt im Azure-Portal eine Verbindung initiieren:

1. Klicken Sie auf **Instanzen**, um das Blatt **Instanzen** zu öffnen.
2. Wählen Sie eine Rolleninstanz aus, in der Remotedesktop konfiguriert ist.
3. Klicken Sie auf **Verbinden**, um eine RDP-Datei für die Rolleninstanz herunterzuladen.

    ![Clouddienste – Remotedesktop](./media/cloud-services-role-enable-remote-desktop-new-portal/CloudServices_Remote_Desktop_Connect.png)

4. Klicken Sie auf **Öffnen** und dann auf **Verbinden**, um die Remotedesktopverbindung zu starten.

>[!NOTE]
> Wenn Ihr Clouddienst durch eine NSG geschützt ist, müssen Sie u.U. Regeln erstellen, die den Datenverkehr an den Ports **3389** und **20000** zulassen.  Remotedesktop verwendet Port **3389**.  Für Cloud Services-Instanzen erfolgt ein Lastenausgleich, daher können Sie nicht direkt steuern, mit welcher Instanz eine Verbindung hergestellt wird.  Die Agents *RemoteForwarder* und *RemoteAccess* verwalten den RDP-Datenverkehr und ermöglichen es dem Client, ein RDP-Cookie zu senden und eine bestimmte Instanz anzugeben, mit der eine Verbindung hergestellt werden soll.  Die Agents *RemoteForwarder* und *RemoteAccess* fordern, dass Port **20000*** geöffnet wird, der bei Vorhandensein einer NSG möglicherweise blockiert ist.

## <a name="additional-resources"></a>Weitere Ressourcen

[Konfigurieren von Clouddiensten](cloud-services-how-to-configure.md)
[Häufig gestellte Fragen zu Clouddiensten– Remotedesktop](cloud-services-faq.md)
