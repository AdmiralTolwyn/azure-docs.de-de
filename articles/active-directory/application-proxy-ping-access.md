---
title: "Headerbasierte Authentifizierung mit PingAccess für Azure AD-Anwendungsproxy | Microsoft-Dokumentation"
description: "Veröffentlichen von Anwendungen mit PingAccess und App-Proxy zum Unterstützen der headerbasierten Authentifizierung."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: daveba
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bfff8ebff87b6c3c501202e95c463a0f4e235ffc
ms.sourcegitcommit: 3cdc82a5561abe564c318bd12986df63fc980a5a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/05/2018
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Headerbasierte Authentifizierung für einmaliges Anmelden mit Anwendungsproxy und PingAccess

Der Azure Active Directory-Anwendungsproxy und PingAccess arbeiten nun zusammen, um Azure Active Directory-Kunden einen Zugriff auf noch mehr Anwendungen bereitzustellen. PingAccess erweitert die [vorhandenen Angebote für den Anwendungsproxy](active-directory-application-proxy-get-started.md), um den Zugriff per einmaligem Anmelden auf Anwendungen zu ermöglichen, die Header für die Authentifizierung verwenden.

## <a name="what-is-pingaccess-for-azure-ad"></a>Was ist PingAccess für Azure AD?

PingAccess für Azure Active Directory ist ein Angebot von PingAccess, mit dem Sie für Benutzer den Zugriff und das einmalige Anmelden für Anwendungen ermöglichen können, bei denen für die Authentifizierung Header verwendet werden. Der Anwendungproxy behandelt diese Apps wie alle anderen und verwendet Azure AD zum Authentifizieren des Zugriffs und zum Leiten des Datenverkehrs durch den Connectordienst. PingAccess ist den Apps vorgelagert und übersetzt das Zugriffstoken aus Azure AD in einen Header, sodass die Anwendung die Authentifizierung in einem Format empfängt, das sie lesen kann.

Ihre Benutzer bemerken keinen Unterschied, wenn sie sich anmelden, um Ihre Unternehmens-Apps zu nutzen. Sie können weiterhin überall und auf beliebigen Geräten arbeiten. 

Da die Anwendungsproxyconnectors Remotedatenverkehr zu allen Apps unabhängig von deren Authentifizierungstyp weiterleiten, sorgen sie auch weiter für einen automatischen Lastenausgleich.

## <a name="how-do-i-get-access"></a>Wie erhalte ich Zugriff?

Da dieses Szenario im Rahmen einer Partnerschaft von Azure Active Directory und PingAccess angeboten wird, benötigen Sie Lizenzen für beide Dienste. Azure Active Directory Premium-Abonnements enthalten aber eine grundlegende PingAccess-Lizenz, die bis zu 20 Anwendungen abdeckt. Falls Sie mehr als 20 headerbasierte Anwendungen veröffentlichen müssen, können Sie bei PingAccess eine weitere Lizenz erwerben. 

Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Veröffentlichen der Anwendung in Azure

Dieser Artikel richtet sich an Personen, die in diesem Szenario erstmals eine App veröffentlichen. Zusätzlich zu den Schritten für die Veröffentlichung durchlaufen Sie die ersten Schritte mit dem Anwendungsproxy und PingAccess. Falls Sie bereits beide Dienste konfiguriert haben, aber eine Auffrischung zu den Veröffentlichungsschritten erhalten möchten, können Sie die Connectorinstallation überspringen und mit [Hinzufügen Ihrer App zu Azure AD mit Anwendungsproxy](#add-your-app-to-Azure-AD-with-Application-Proxy) fortfahren.

>[!NOTE]
>Da dieses Szenario auf einer Partnerschaft von Azure AD und PingAccess beruht, gelten einige der Anweisungen für die Ping Identity-Website.

### <a name="install-an-application-proxy-connector"></a>Installieren eines Anwendungsproxyconnectors

Wenn Sie den Anwendungsproxy bereits aktiviert und einen Connector installiert haben, können Sie diesen Abschnitt überspringen und mit [Hinzufügen Ihrer App zu Azure AD mit Anwendungsproxy](#add-your-app-to-azure-ad-with-application-proxy) fortfahren.

Der Anwendungsproxyconnector ist ein Windows Server-Dienst, der den Datenverkehr von den Remotemitarbeitern zu Ihren veröffentlichten Apps weiterleitet. Ausführlichere Installationsanweisungen finden Sie unter [Aktivieren des Anwendungsproxys über das Azure-Portal](active-directory-application-proxy-enable.md).

1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Azure Active Directory** > **Anwendungsproxy** aus.
3. Wählen Sie **Connector herunterladen** aus, um den Anwendungsproxyconnector herunterzuladen. Folgen Sie den Installationsanweisungen.

   ![Aktivieren des Anwendungsproxys und Herunterladen des Connectors](./media/application-proxy-ping-access/install-connector.png)

4. Nach dem Herunterladen des Connectors sollte der Anwendungsproxy automatisch für Ihr Verzeichnis aktiviert sein. Falls nicht, können Sie **Anwendungsproxy aktivieren** auswählen.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Hinzufügen Ihrer App zu Azure AD mit Anwendungsproxy

Im Azure-Portal müssen Sie zwei Aktionen durchführen. Zuerst veröffentlichen Sie Ihre Anwendung mit dem Anwendungsproxy. Anschließend stellen Sie einige Informationen zur App zusammen, die Sie während der PingAccess-Schritte verwenden können.

Führen Sie diese Schritte aus, um Ihre App zu veröffentlichen. Eine ausführlichere Beschreibung der Schritte 1 bis 8 finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-publish-azure-portal.md).

1. Falls nicht im letzten Abschnitt erfolgt, melden Sie sich beim [Azure-Portal](https://portal.azure.com) als globaler Administrator an.
2. Wählen Sie **Azure Active Directory** > **Unternehmensanwendungen** aus.
3. Wählen Sie oben auf dem Blatt **Hinzufügen** aus.
4. Wählen Sie **Lokale Anwendung** aus.
5. Füllen Sie die Pflichtfelder mit Informationen zur neuen App aus. Befolgen Sie diese Anleitung für die folgenden Einstellungen:
   - **Interne URL:** Normalerweise geben Sie die URL an, über die Sie zur Anmeldeseite der App gelangen, wenn Sie sich im Unternehmensnetzwerk befinden. Für dieses Szenario muss der Connector den PingAccess-Proxy als Startseite der App verwenden. Verwenden Sie dieses Format: `https://<host name of your PA server>:<port>`. Der Standardport ist 3000, Sie können diesen aber in PingAccess konfigurieren.

    > [!WARNING]
    > Für diesen SSO-Typ muss die interne URL HTTPS anstelle von HTTP verwenden.

   - **Methode für die Vorauthentifizierung** : Azure Active Directory
   - **URL in Headern übersetzen**: Nein

   >[!NOTE]
   >Wenn dies Ihre erste Anwendung ist, verwenden Sie Port 3000, um zu beginnen, und kehren wieder zu dieser Einstellung zurück, um sie bei einer Änderung der PingAccess-Konfiguration zu aktualisieren. Wenn dies Ihre zweite oder höhere App ist, muss sie dem Listener entsprechen, den Sie in PingAccess konfiguriert haben. Erfahren Sie mehr über [Listener in PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Klicken Sie unten auf dem Blatt auf **Hinzufügen**. Ihre Anwendung wird hinzugefügt, das Schnellstartmenü wird geöffnet.
7. Wählen Sie im Schnellstartmenü **Zuweisen eines Benutzers zu Testzwecken** aus, und fügen Sie der Anwendung mindestens einen Benutzer hinzu. Stellen Sie sicher, dass dieses Testkonto auf die lokale Anwendung zugreifen kann.
8. Wählen Sie **Zuweisen** aus, um die Zuweisung des Testbenutzers zu speichern.
9. Wählen Sie auf dem Blatt „App-Verwaltung“ **Einmaliges Anmelden** aus.
10. Wählen Sie im Dropdownmenü **Headerbasierte Anmeldung** aus. Wählen Sie **Speichern** aus.

   >[!TIP]
   >Wenn Sie das headerbasierte einmalige Anmelden zum ersten Mal verwenden, müssen Sie PingAccess installieren. Verwenden Sie den Link auf dieser SSO-Seite zum Herunterladen von PingAccess, um sicherzustellen, dass Ihr Azure-Abonnement automatisch Ihrer PingAccess-Installation zugeordnet wird. Sie können die Download-Website jetzt öffnen oder später zu dieser Seite zurückkehren. 

   ![Auswählen der headerbasierten Anmeldung](./media/application-proxy-ping-access/sso-header.PNG)

11. Schließen Sie das Blatt „Unternehmensanwendungen“, oder scrollen Sie ganz nach links, um zum Menü „Azure Active Directory“ zurückzukehren.
12. Wählen Sie **App-Registrierungen** aus.

   ![Auswählen von „App-Registrierungen“](./media/application-proxy-ping-access/app-registrations.png)

13. Wählen Sie die App aus, die Sie gerade hinzugefügt haben. Klicken Sie dann auf **Antwort-URLs**.

   ![Auswählen von „Antwort-URLs“](./media/application-proxy-ping-access/reply-urls.png)

14. Prüfen Sie, ob die externe URL, die Sie Ihrer App in Schritt 5 zugewiesen haben, in der Liste „Antwort-URLs“ enthalten ist. Falls nicht, fügen Sie sie jetzt hinzu.
15. Wählen Sie auf der Blatt „App-Einstellungen“ **Erforderliche Berechtigungen** aus.

  ![Auswählen von „Erforderliche Berechtigungen“](./media/application-proxy-ping-access/required-permissions.png)

16. Wählen Sie **Hinzufügen**. Wählen Sie für die API **Microsoft Azure Active Directory** aus, und klicken Sie dann auf **Auswählen**. Wählen Sie für die Berechtigungen die Optionen **Lesen und schreiben: Alle Anwendungen** und **Anmelden und Benutzerprofil lesen** aus. Klicken Sie dann auf **Auswählen** und **Fertig**.  

  ![Auswählen von Berechtigungen](./media/application-proxy-ping-access/select-permissions.png)

17. Erteilen Sie Berechtigungen, bevor Sie den Berechtigungenbildschirm schließen. 
![Erteilen von Berechtigungen](media/application-proxy-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Erfassen von Informationen für die Schritte in PingAccess

1. Wählen Sie auf dem Blatt mit den App-Einstellungen die Option **Eigenschaften**. 

  ![Auswählen von „Eigenschaften“](./media/application-proxy-ping-access/properties.png)

2. Speichern Sie den Wert **Anwendungs-ID**. Dieser wird für die Client-ID verwendet, wenn Sie PingAccess konfigurieren.
3. Wählen Sie auf dem Blatt „App-Einstellungen“ die Option **Schlüssel** aus.

  ![Auswählen von „Schlüssel“](./media/application-proxy-ping-access/Keys.png)

4. Erstellen Sie einen Schlüssel, indem Sie eine Schlüsselbeschreibung eingeben und im Dropdownmenü ein Ablaufdatum wählen.
5. Wählen Sie **Speichern** aus. Im Feld **Wert** wird eine GUID angezeigt.

  Speichern Sie diesen Wert nun, da sie ihn nicht erneut anzeigen können, nachdem dieses Fenster geschlossen wurde.

  ![Erstellen eines neuen Schlüssels](./media/application-proxy-ping-access/create-keys.png)

6. Schließen Sie das Blatt „App-Registrierungen“, oder scrollen Sie ganz nach links, um zum Menü „Azure Active Directory“ zurückzukehren.
7. Wählen Sie **Eigenschaften** aus.
8. Speichern Sie die GUID **Verzeichnis-ID**.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Optional – Aktualisieren von GraphAPI zum Senden von benutzerdefinierten Feldern

Eine Liste von Sicherheitstoken, die Azure AD für die Authentifizierung sendet, finden Sie unter [Azure AD-Tokenreferenz](./develop/active-directory-token-and-claims.md). Wenn Sie einen benutzerdefinierten Anspruch benötigen, der andere Token sendet, legen Sie mit dem Graph-Tester oder dem Manifest für die Anwendung im Azure-Portal das App-Feld *acceptMappedClaims* auf **True** fest.    

In diesem Beispiel wird der Graph-Tester verwendet:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Dieses Beispiel verwendet das [Azure-Portal](https://portal.azure.com) zum Aktualisieren des Felds *acceptedMappedClaims*:
1. Melden Sie sich als globaler Administrator beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Azure Active Directory** > **App-Registrierungen**.
3. Wählen Sie Ihre Anwendung und dann **Manifest** aus.
4. Wählen Sie **Bearbeiten** aus, suchen Sie nach dem Feld *acceptedMappedClaims*, und ändern Sie den Wert in **true**.
![App-Manifest](media/application-proxy-ping-access/application-proxy-ping-access-manifest.PNG)
1. Wählen Sie **Speichern** aus.

>[!NOTE]
>Um einen benutzerdefinierten Anspruch zu verwenden, benötigen Sie auch eine für diese Anwendung definierte und ihr zugewiesene benutzerdefinierte Richtlinie.  Diese Richtlinie sollte alle erforderlichen benutzerdefinierten Attribute enthalten.
>
>Richtliniendefinition und Zuweisung können über PowerShell, Azure AD Graph Explorer oder MS Graph ausgeführt werden.  Wenn Sie dies in PowerShell tun, müssen Sie möglicherweise zuerst `New-AzureADPolicy ` verwenden und dann der Anwendung mit `Set-AzureADServicePrincipalPolicy` zuweisen.  Weitere Informationen finden Sie unter [Zuweisung von Anspruchszuordnungsrichtlinien](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Optional – Verwenden eines benutzerdefinierten Anspruchs
Damit Ihre Anwendung einen benutzerdefinierten Anspruch verwenden und zusätzliche Felder beinhalten kann, achten Sie darauf, dass Sie auch [eine Richtlinie für die Zuordnung benutzerdefinierter Ansprüche erstellt und der Anwendung zugeordnet haben ](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Herunterladen von PingAccess und Konfigurieren der App

Nachdem Sie nun alle Schritte des Azure Active Directory-Setups ausgeführt haben, können Sie mit dem Konfigurieren von PingAccess fortfahren. 

Die ausführlichen Anleitungen für den PingAccess-Teil dieses Szenarios werden in der Ping Identity-Dokumentation unter [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) fortgesetzt.

Diese Anleitungen begleiten Sie durch das Einrichten eines PingAccess-Kontos, sofern noch nicht vorhanden, das Installieren des PingAccess-Servers und das Erstellen einer Azure AD OIDC-Anbieterverbindung mithilfe der Verzeichnis-ID, die Sie aus dem Azure-Portal kopiert haben. Anschließend nutzen Sie die Anwendungs-ID und Schlüsselwerte zum Erstellen einer Websitzung auf PingAccess. Danach können Sie die Identitätszuordnung einrichten und einen virtuellen Host, einen virtuellen Standort und eine virtuelle Anwendung erstellen.

### <a name="test-your-app"></a>Testen Ihrer App

Nachdem Sie alle Schritte abgeschlossen haben, sollte Ihre App betriebsbereit sein. Um sie zu testen, öffnen Sie einen Browser und navigieren zur externen URL, die Sie erstellt haben, als Sie die App in Azure veröffentlicht haben. Melden Sie sich mit dem Testkonto an, das Sie der App zugewiesen haben.

## <a name="next-steps"></a>Nächste Schritte

- [Configure PingAccess for Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html) (Konfigurieren von PingAccess für Azure AD)
- [Wie stellt der Azure AD-Anwendungsproxy das einmalige Anmelden bereit?](application-proxy-sso-overview.md)
- [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)
