---
title: Konfigurieren der Leadverwaltung in Salesforce | Azure Marketplace
description: Konfigurieren Sie die Leadverwaltung in Salesforce für Azure Marketplace-Kunden.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 087cdafe8b819e4929e1608ed7e00be2c1169414
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263026"
---
# <a name="configure-lead-management-for-salesforce"></a>Konfigurieren der Leadverwaltung in Salesforce

In diesem Artikel wird beschrieben, wie Sie Ihr Salesforce-System einrichten, um Vertriebsleads aus Ihrem Angebot im kommerziellen Marketplace zu verarbeiten.

> [!Note]
> Der Marketplace unterstützt keine vorab aufgefüllten Listen, z. B. Listen von Werten für das Feld **Country**. Stellen Sie vor dem Fortfahren sicher, dass keine Listen eingerichtet sind. Alternativ können Sie einen [HTTPS-Endpunkt](./commercial-marketplace-lead-management-instructions-https.md) oder eine [Azure-Tabelle](./commercial-marketplace-lead-management-instructions-azure-table.md) für den Erhalt von Leads konfigurieren.

## <a name="set-up-your-salesforce-system"></a>Einrichten des Salesforce-Systems

1. Melden Sie sich bei Salesforce an.
2. Wenn Sie die Salesforce Lighting Experience verwenden:
    1. Wählen Sie auf der Salesforce-Startseite **Setup** aus.
    ![Einrichtung von Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-1.png)

    1. Navigieren Sie auf der Seite „Setup“ über den linken Navigationsbereich zu **Platform Tools (Plattformtools) > Feature Settings (Featureeinstellungen) > Marketing > Web-to-Lead**.

        ![Web-to-Lead in Salesforce](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-2.png)

3. Wenn Sie die klassische Salesforce-Benutzeroberfläche verwenden:
    1. Wählen Sie auf der Salesforce-Startseite **Setup** aus.
    ![Klassisches Salesforce-Setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-setup.png)

    1. Navigieren Sie auf der Seite „Setup“ über den linken Navigationsbereich zu **Build (Erstellen) > Customize (Anpassen) > Leads > Web-to-Lead**.

        ![Web-to-Lead in Salesforce (klassisch)](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-classic-web-to-lead.png)

Die restlichen Anweisungen sind identisch, unabhängig davon, welche Salesforce-Darstellung Sie verwenden.

4. Klicken Sie auf der Seite **Web-to-Lead Setup** (Web-to-Lead-Setup) auf **Create Web-to-Lead Form** (Web-to-Lead-Formular erstellen).
5. Wählen Sie auf **Web-to-Lead Setup** die Schaltfläche **Create Web-to-Lead Form** aus.
    ![Salesforce – Web-to-Lead-Setup](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-3.png)

6. Stellen Sie sicher, dass auf der Seite **Create a Web-to-Lead Form** (Web-to-Lead-Formular erstellen) die Einstellung `the Include reCAPTCHA in HTML` deaktiviert ist, und wählen Sie **Generate** (Generieren) aus. 
    ![Salesforce – Web-to-Lead-Formular erstellen ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-4.png)

7. Es wird HTML-Text angezeigt. Suchen Sie den Text „oid“, kopieren Sie den **Wert „oid“**  aus dem HTML-Text (nur den Text in Anführungszeichen), und speichern Sie ihn. Sie fügen diesen Wert in das Feld **Organisations-ID** im Veröffentlichungsportal ein.
    ![Salesforce – Web-to-Lead-Formular erstellen ](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-5.png)

8. Wählen Sie **Finished** (Abgeschlossen) aus.

## <a name="configure-your-offer-to-send-leads-to-salesforce"></a>Konfigurieren Ihres Angebots zum Senden von Leads zu Salesforce

Führen Sie die folgenden Schritte aus, um die Leadverwaltungsinformationen für Ihr Angebot im Veröffentlichungsportal zu konfigurieren:

1. Navigieren Sie zur Seite **Angebotseinrichtung** für Ihr Angebot.
1. Wählen Sie im Abschnitt „Leadverwaltung“ die Option **Verbinden** aus.
    ![Leadverwaltung – Verbinden](./media/commercial-marketplace-lead-management-instructions-salesforce/lead-management-connect.png)

1. Wählen Sie im Popupfenster „Verbindungsdetails“ **Salesforce** als **Leadzielgruppe** aus, und fügen Sie die `oid` aus dem in den vorherigen Schritten erstellten Web-to-Lead-Formular in das Feld **Organisations-ID** ein.

1. **Kontakt-E-Mail**: Geben Sie E-Mail-Adressen der Personen in Ihrem Unternehmen an, die E-Mail-Benachrichtigungen erhalten sollen, wenn ein neuer Lead empfangen wird. Sie können mehrere durch Semikolon getrennte E-Mail-Adressen angeben.

1. Klicken Sie auf **OK**.

Klicken Sie auf die Schaltfläche „Überprüfen“, um sich zu vergewissern, dass die Verbindung mit einem Leadziel erfolgreich hergestellt wurde. Bei erfolgreicher Verbindungsherstellung enthält das Leadziel einen Testlead.

>[!Note]
>Sie müssen die Konfiguration der übrigen Einstellungen des Angebots abschließen und veröffentlichen, damit Sie Leads für das Angebot erhalten.

![Verbindungsdetails – Leadzielgruppe auswählen](./media/commercial-marketplace-lead-management-instructions-salesforce/choose-lead-destination.png)

![Verbindungsdetails – Leadzielgruppe auswählen](./media/commercial-marketplace-lead-management-instructions-salesforce/salesforce-connection-details.png)
