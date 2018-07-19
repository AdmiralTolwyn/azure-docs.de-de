---
title: Einrichten von in Azure Active Directory eingebundenen Geräten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in Azure Active Directory eingebundene Geräte einrichten.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: f2d285735b92c3acd67dc741f344b836e791be04
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39058462"
---
# <a name="set-up-azure-active-directory-joined-devices"></a>Einrichten von in Azure Active Directory eingebundenen Geräten

Mit der Geräteverwaltung in Azure Active Directory (Azure AD) können Sie sicherstellen, dass Benutzer auf Ihre Ressourcen über Geräte zugreifen, die Ihren Standards für Sicherheit und Konformität entsprechen. Weitere Informationen finden Sie unter [Einführung in die Geräteverwaltung in Azure Active Directory](../device-management-introduction.md).

Wenn Sie unternehmenseigene Windows 10-Geräte über Azure AD steuern möchten, können Sie dazu in Azure AD eingebundene Geräte konfigurieren. Die entsprechenden Schritte werden in diesem Thema beschrieben. 


## <a name="prerequisites"></a>Voraussetzungen

Um ein Windows 10-Gerät einzubinden, muss der Geräteregistrierungsdienst so konfiguriert sein, dass Sie Geräte registrieren können. Sie benötigen Berechtigungen zum Einbinden von Geräten in den Azure AD-Mandanten. Darüber hinaus müssen weniger Geräte registriert sein als die konfigurierte maximale Anzahl. Weitere Informationen finden Sie unter [Configure device settings](../device-management-azure-portal.md#configure-device-settings) (Konfigurieren von Geräteeinstellungen).



## <a name="what-you-should-know"></a>Wichtige Informationen


- Windows bindet das Gerät im Organisationsverzeichnis in Azure AD ein.

- Unter Umständen werden Sie zur mehrstufigen Authentifizierung aufgefordert. Diese Aufforderung kann vom IT-Administrator konfiguriert werden.

- Azure AD prüft, ob eine Registrierung des Geräts in der Mobilgeräteverwaltung erforderlich ist, und registriert es ggf.

- Wenn Sie ein verwalteter Benutzer sind, führt Windows Sie über die automatische Anmeldung auf den Desktop.

- Verbundbenutzer müssen sich mit ihren Anmeldeinformationen anmelden.

- Bei Verwendung eines Verbunds MUSS Ihr Identitätsanbieter den WS-Fed- und WS-Trust-Endpunkt mit Benutzername/Kennwort unterstützen. Zulässige Versionen sind 1.3 und 2005. Dieses Protokoll muss unterstützt werden, um das Gerät mit Azure AD verknüpfen und sich mit einem Kennwort bei dem Gerät anmelden zu können. 




## <a name="joining-a-device"></a>Einbinden eines Geräts

Dieser Abschnitt enthält die Schritte zum Einbinden Ihres Windows 10-Geräts in Azure AD. Erfolgreich verknüpfte Geräte werden als **Mit \<Ihre Azure AD-Instanz\> verbunden** angezeigt.

![Verbunden](./media/device-management-azuread-joined-devices-setup/13.png)


**So binden Sie Ihr Windows 10-Gerät ein:**

1. Klicken Sie im Menü **Start** auf **Einstellungen**.

    ![Einstellungen](./media/device-management-azuread-joined-devices-setup/01.png)

2. Klicken Sie auf **Konten**.

    ![Konten](./media/device-management-azuread-joined-devices-setup/02.png)


3. Klicken Sie auf **Auf Arbeits- oder Schulkonto zugreifen**.

    ![Auf Arbeits- oder Schulkonto zugreifen](./media/device-management-azuread-joined-devices-setup/03.png)

4. Klicken Sie im Dialogfenster **Auf Arbeits- oder Schulkonto zugreifen** auf **Verbinden**.

    ![Verbinden](./media/device-management-azuread-joined-devices-setup/04.png)


5. Klicken Sie im Dialogfenster **Geschäfts-, Schul- oder Unikonto einrichten** auf **Dieses Gerät in Azure Active Directory einbinden**.

    ![Verbinden](./media/device-management-azuread-joined-devices-setup/08.png)


6. Geben Sie im Dialogfenster **Melden Sie sich an** Ihren Kontonamen (z.B. someone@example.com) ein, und klicken Sie dann auf **Weiter**.

    ![Melden Sie sich an](./media/device-management-azuread-joined-devices-setup/10.png)


6. Geben Sie im Dialogfenster **Kennwort eingeben** Ihr Kennwort ein, und klicken Sie dann auf **Anmelden**.

    ![Kennwort eingeben](./media/device-management-azuread-joined-devices-setup/05.png)


7. Geben Sie im Dialogfenster **Stellen Sie sicher, dass dies Ihr Unternehmen ist.** auf **Beitreten**.

    ![Stellen Sie sicher, dass dies Ihr Unternehmen ist.](./media/device-management-azuread-joined-devices-setup/11.png)


8. Klicken Sie im Dialogfenster **Alles erledigt** auf **Fertig**.

    ![Alles erledigt](./media/device-management-azuread-joined-devices-setup/12.png)

## <a name="verification"></a>Überprüfung

Ob ein Gerät in eine Azure AD-Instanz eingebunden wurde, können Sie im Dialogfenster **Auf Arbeits- oder Schulkonto zugreifen** Ihres Geräts überprüfen.

![Verbunden](./media/device-management-azuread-joined-devices-setup/13.png)

Alternativ können Sie den folgenden Befehl ausführen: `dsregcmd /status`.  
Auf einem erfolgreich eingebundenen Gerät wird **Yes** für **AzureAdJoined** angezeigt.

![Verbunden](./media/device-management-azuread-joined-devices-setup/14.png)

Sie können die Geräteeinstellungen auch im Azure AD-Portal überprüfen.

![Verbunden](./media/device-management-azuread-joined-devices-setup/15.png)

Weitere Informationen finden Sie unter [Locate devices](../device-management-azure-portal.md#locate-devices) (Geräte ermitteln).


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter 

- [Einführung in die Geräteverwaltung in Azure Active Directory](../device-management-introduction.md)
- [Verwalten von Geräten mithilfe des Azure-Portals – Vorschau](../device-management-azure-portal.md)
- 



