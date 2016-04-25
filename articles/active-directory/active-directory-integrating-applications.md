<properties
   pageTitle="Integrieren von Anwendungen in Azure Active Directory | Microsoft Azure"
   description="Erfahren Sie, wie Sie eine Anwendung in Azure AD hinzufügen, aktualisieren oder sie aus Azure Active Directory (Azure AD) entfernen."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/06/2016"
   ms.author="mbaldwin;bryanla" />

# Integrieren von Anwendungen in Azure Active Directory

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Unternehmensentwickler und SaaS-Anbieter (Software-as-a-Service) können kommerzielle Clouddienste oder Branchenanwendungen entwickeln, die in Azure Active Directory (Azure AD) integriert werden können, um eine sichere Anmeldung und Autorisierung für ihre Dienste bereitzustellen Um eine Anwendung oder einen Dienst in Azure AD integrieren zu können, muss ein Entwickler zunächst die Details zu seiner Anwendung mithilfe des klassischen Azure-Portals in Azure AD registrieren.

Der vorliegende Artikel zeigt, wie Sie eine Anwendung in Azure AD hinzufügen, aktualisieren oder aus sie aus Azure AD entfernen. Sie lernen die verschiedenen Anwendungstypen kennen, die in Azure AD integriert werden können, und erfahren, wie Sie Ihre Anwendungen für den Zugriff auf andere Ressourcen (z. B. auf Web-APIs) konfigurieren.

Weitere Informationen zu den beiden Azure AD-Objekten, die eine registrierte Anwendung darstellen, und der Beziehung zwischen ihnen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory-application-objects.md); weitere Informationen zu den Richtlinien zum Branding bei der Anwendungsentwicklung mit Azure Active Directory finden Sie unter [Brandingrichtlinien für integrierte Apps](active-directory-branding-guidelines.md).

## Hinzufügen einer Anwendung

Jede Anwendung muss zunächst in einem Azure AD-Mandanten registriert werden, um die Funktionen von Azure AD nutzen zu können. Dieser Registrierungsvorgang umfasst das Angeben von Details zu Ihrer Anwendung in Azure AD. Beispielsweise muss die URL für den Speicherort angegeben werden, die URL, an die nach der Authentifizierung eines Benutzers Antworten gesendet werden sollen, die URI zum Identifizieren der App usw.

Wenn Sie eine Webanwendung entwickeln, für die nur eine Benutzeranmeldung in Azure AD erforderlich ist, können Sie einfach die nachstehend aufgeführten Anweisungen befolgen. Wenn Ihre Anwendung Zugriff auf eine Web-API benötigt, oder Sie Ihre Anwendung mehrinstanzenfähig machen möchten, damit Benutzer aus anderen Azure AD-Mandanten darauf zugreifen können, fahren Sie mit dem Abschnitt [Aktualisieren einer Anwendung](#updating-an-application) fort, um Ihre Anwendung weiter zu konfigurieren.

### So registrieren Sie eine neue Anwendung im klassischen Azure-Portal

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol, und klicken Sie dann auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**. Wenn Ihrem Verzeichnis keine Apps hinzugefügt wurden, wird auf dieser Seite der Link "App hinzufügen" angezeigt. Klicken Sie auf den Link oder alternativ auf die Schaltfläche **Hinzufügen** in der Befehlsleiste.

1. Klicken Sie auf der Seite „Was möchten Sie gerne tun?” auf den Link **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.

1. Auf der Seite "Erzählen Sie uns von Ihrer Anwendung" müssen Sie einen Namen für Ihre Anwendung sowie den Typ der Anwendung angeben, die Sie bei Azure AD registrieren. Sie können zwischen Webanwendung und/oder Web-API (Standardeinstellung, in OAuth2 als vertraulicher Client bezeichnet) oder einer nativen Clientanwendung wählen. Letztere stellt eine Anwendung dar, die auf einem Gerät wie z.B. einem Smartphone oder Computer installiert ist (in OAuth2 als öffentlicher Client bezeichnet). Klicken Sie danach auf das Pfeilsymbol in der rechten unteren Ecke der Seite.

1. Geben Sie auf der Seite „App-Eigenschaften“ die URL für die Anmeldung und den App-ID-URI an, wenn Sie eine Webanwendung registrieren. Andernfalls geben Sie lediglich den Weiterleitungs-URI für eine native Clientanwendung an, und klicken Sie dann auf das Kontrollkästchen unten rechts auf der Seite.

1. Ihre Anwendung wird hinzugefügt, und Sie werden auf die Seite "Schnellstart" für Ihre Anwendung umgeleitet. Abhängig davon, ob es sich bei Ihrer Anwendung um eine Web- oder eine systemeigene Anwendung handelt, werden andere Optionen zum Hinzufügen weiterer Funktionen zu Ihrer Anwendung angezeigt. Nachdem Ihre Anwendung hinzugefügt wurde, können Sie beginnen, Ihre Anwendung zu aktualisieren, um Benutzern die Anmeldung zu ermöglichen, auf Web-APIs in anderen Anwendungen zuzugreifen oder eine mehrinstanzenfähige Anwendung zu konfigurieren (um anderen Organisationen zu ermöglichen, auf Ihre Anwendung zuzugreifen).

>[AZURE.NOTE] Standardmäßig wird die neu erstellte Anwendung bei der Registrierung so konfiguriert, dass sich Benutzer aus Ihrem Verzeichnis bei der Anwendung anmelden können.

## Aktualisieren einer Anwendung

Nachdem Ihre Anwendung in Azure AD registriert wurde, muss sie ggf. aktualisiert werden, um Zugriff auf Web-APIs bereitzustellen, für andere Unternehmen zur Verfügung gestellt zu werden usw. Dieser Abschnitt beschreibt, wie Ihre Anwendung für diese Aufgaben konfiguriert wird. Weitere Informationen zur Funktionsweise der Authentifizierung in Azure AD finden Sie unter [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).

### Übersicht über das Consent Framework

Das Consent Framework von Azure AD erleichtert die Entwicklung mehrinstanzenfähiger Web- und systemeigener Clientanwendungen, die Zugriff auf Web-APIs benötigen, die von einem anderen Azure AD-Mandanten gesichert werden als dem, bei dem die Clientanwendung registriert ist. Zu diesen Web-APIs gehören neben Ihren eigenen Web-APIs die Graph-API, Office 365 und anderen Microsoft-Dienste. Das Framework basiert darauf, dass Benutzer oder Administratoren ihre Zustimmung zur Registrierung einer Anwendung in ihrem Verzeichnis erteilen. Diese Zustimmung kann auch den Zugriff auf Verzeichnisdaten umfassen.

Wenn beispielsweise eine Webclientanwendung die Web-API/Ressourcenanwendung von Office 365 zum Lesen von Kalenderinformationen über einen Benutzer aufrufen muss, muss dieser Benutzer der Clientanwendung seine Zustimmung erteilen. Anschließend kann die Clientanwendung die Office 365-Web-API im Namen des Benutzers aufrufen und die Kalenderinformationen nach Bedarf verwenden.

Das Consent Framework basiert auf OAuth 2.0 und seinen verschiedenen Datenflüssen, z. B. Authorization Code Grant und Client Credentials Grant. Dabei kommen öffentliche oder vertrauliche Clients zum Einsatz. Durch die Verwendung von OAuth 2.0 ermöglicht Azure AD die Entwicklung zahlreicher verschiedener Typen von Clientanwendungen, z. B. für Telefon, Tablet, Server oder Web, und ermöglicht den Zugriff auf die erforderlichen Ressourcen.

Ausführlichere Informationen zum Consent Framework finden Sie unter [OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx), [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md) und im Office 365-Thema [Grundlagen der Authentifizierung mit Office 365-APIs](https://msdn.microsoft.com/office/office365/howto/common-app-authentication-tasks).

#### Beispiel für die Zustimmungsbenutzeroberfläche

Die folgenden Schritte zeigen, wie das Consent Framework in der Benutzeroberfläche für den Anwendungsentwickler und für den Benutzer umgesetzt wird.

1. Legen Sie im klassischen Azure-Portal auf der Seite für die Konfiguration Ihrer Webclientanwendung die erforderlichen Berechtigungen für Ihre Anwendung fest, indem Sie die Dropdownmenüs im Steuerelement „Berechtigungen für andere Anwendungen“ verwenden.

    ![Berechtigungen für andere Anwendungen](./media/active-directory-integrating-applications/permissions.png)

1. Angenommen, die Berechtigungen für Ihre Anwendung wurden aktualisiert, die Anwendung wird ausgeführt, und ein Benutzer möchte die Anwendung zum ersten Mal verwenden. Falls die Anwendung noch kein Zugriffs- oder Aktualisierungstoken abgerufen hat, muss die Anwendung über den Autorisierungsendpunkt von Azure AD einen Autorisierungscode anfordern, der anschließend verwendet werden kann, um ein neues Zugriffs- und Aktualisierungstoken abzurufen.

1. Falls der Benutzer noch nicht authentifiziert wurde, wird er zur Anmeldung bei Azure AD aufgefordert.

    ![Benutzer- oder Administratoranmeldung bei Azure AD](./media/active-directory-integrating-applications/useradminsignin.png)

1. Nachdem der Benutzer sich angemeldet hat, ermittelt Azure AD, ob für den Benutzer eine Seite für die Zustimmungserteilung angezeigt werden muss. Das Ergebnis dieser Ermittlung ist davon abhängig, ob der Benutzer (oder der Administrator seiner Organisation) die Anwendungszustimmung bereits erteilt hat. Wenn die Zustimmung noch nicht erteilt wurde, fordert Azure AD den Benutzer zur Zustimmung auf und zeigt an, welche Berechtigungen für die Funktionalität benötigt werden. Der Berechtigungssatz, der im Dialogfeld für die Zustimmung angezeigt wird, ist identisch mit der Auswahl im Steuerelement „Berechtigungen für andere Anwendungen“ im klassischen Azure-Portal.

    ![Oberfläche für die Benutzerzustimmung](./media/active-directory-integrating-applications/userconsent.png)

1. Nachdem der Benutzer seine Zustimmung erteilt hat, wird ein Autorisierungscode an Ihre Anwendung zurückgegeben, mit dem ein Zugriffs- und Aktualisierungstoken abgerufen werden kann. Weitere Informationen zu diesem Datenfluss finden Sie im Abschnitt [Webanwendung zu Web-API](active-directory-authentication-scenarios.md#web-application-to-web-api) im Thema [Authentifizierungsszenarien für Azure AD](active-directory-authentication-scenarios.md).

### Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs

Wenn eine Clientanwendung für den Zugriff auf eine Web-API konfiguriert ist, die durch eine Ressourcenanwendung (d. h. Azure AD Graph-API) bereitgestellt wird, stellt das oben beschriebene Consent Framework sicher, dass dem Client die erforderlichen Berechtigungen basierend auf den angeforderten Berechtigungen erteilt werden. Standardmäßig können alle Anwendungen Berechtigungen aus Azure Active Directory (Graph-API) und der Azure Service Management-API auswählen. Hierbei ist die Azure AD-Berechtigung "Anmeldung aktivieren und Benutzerprofil lesen" bereits standardmäßig ausgewählt. Wenn Ihre Clientanwendung bei einem Office 365-Azure AD-Mandanten registriert ist, stehen Web-APIs und Berechtigungen für SharePoint und Exchange Online ebenfalls zur Auswahl. In den Dropdownmenüs neben der gewünschten Web-API können Sie zwischen zwei Arten von Berechtigungen wählen:

- Anwendungsberechtigungen: Ihre Clientanwendung benötigt direkten Zugriff auf die Web-API selbst (kein Benutzerkontext). Für diese Art von Berechtigung ist die Zustimmung des Administrators erforderlich, sie steht nicht für systemeigene Clientanwendungen zur Verfügung.

- Delegierte Berechtigungen: Ihre Clientanwendung benötigt als angemeldeter Benutzer Zugriff auf die Web-API. Der Zugriff ist jedoch durch die ausgewählte Berechtigung eingeschränkt. Diese Art von Berechtigung kann von einem Benutzer erteilt werden – es sei denn, die Berechtigung ist so konfiguriert, dass sie die Zustimmung durch einen Administrator erfordert.

#### So fügen Sie Zugriff auf Web-APIs hinzu

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol und anschließend auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Anwendung, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.

1. Erweitern Sie auf der Seite „Schnellstart“ den Abschnitt „Auf Web-APIs in anderen Anwendungen zugreifen“, und klicken Sie dann im Abschnitt „Berechtigungen auswählen“ auf den Link **Jetzt konfigurieren**. Die Seite mit den Anwendungseigenschaften wird angezeigt.

1. Scrollen Sie nach unten zum Abschnitt "Berechtigungen für andere Anwendungen". In der ersten Spalte können Sie aus den Ressourcenanwendungen in Ihrem Verzeichnis auswählen, die eine Web-API verfügbar machen. Nach der Auswahl können Sie ggf. die Anwendungs- und Delegierungsberechtigungen auswählen, die die Web-API bereitstellt.

1. Klicken Sie nach der Auswahl auf die Schaltfläche **Speichern** in der Befehlszeile.

>[AZURE.NOTE] Wenn Sie auf die Schaltfläche "Speichern" klicken, werden basierend auf den von Ihnen konfigurierten "Berechtigungen für andere Anwendungen" automatisch die Berechtigungen für die Anwendung in Ihrem Verzeichnis festgelegt. Sie können diese Anwendungsberechtigungen auf der Registerkarte mit den Anwendungseigenschaften anzeigen.

### Konfigurieren einer Ressourcenanwendung zum Bereitstellen von Web-APIs

Sie können eine Web-API entwickeln und sie Clientanwendungen zur Verfügung stellen, indem Sie Ihre Berechtigungsbereiche verfügbar machen. Eine ordnungsgemäß konfigurierte Web-API wird auf die gleiche Weise wie andere Microsoft-Web-APIs (einschließlich der Graph-API und der Office 365-APIs) zur Verfügung gestellt. Berechtigungsbereiche werden durch Ihr Anwendungsmanifest zur Verfügung gestellt. Hierbei handelt es sich um eine JSON-Datei, die die Identitätskonfiguration Ihrer Anwendung repräsentiert. Sie können Ihre Berechtigungsbereiche bereitstellen, indem Sie im klassischen Azure-Portal zu Ihrer Anwendung navigieren und dann auf der Befehlsleiste auf die Schaltfläche „Anwendungsmanifest“ klicken.

#### Hinzufügen von Berechtigungsbereichen zu Ihrer Ressourcenanwendung

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol und anschließend auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Ressourcenanwendung, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.

1. Klicken Sie auf der Befehlsleiste auf die Schaltfläche **Manifest verwalten**, und wählen Sie dann **Manifest herunterladen** aus.

1. Öffnen Sie die JSON-Anwendungsmanifestdatei, und ersetzen Sie den Knoten "oauth2Permissions" durch den folgenden JSON-Codeausschnitt. Dieser Codeausschnitt zeigt, wie ein als Benutzeridentitätswechsel bezeichneter Berechtigungsbereich bereitgestellt wird. Stellen Sie sicher, dass Sie den Text und die Werte für Ihre eigene Anwendung ändern:

		"oauth2Permissions": [
		{
			"adminConsentDescription": "Allow the application full access to the Todo List service on behalf of the signed-in 	user",
			"adminConsentDisplayName": "Have full access to the Todo List service",
			"id": "b69ee3c9-c40d-4f2a-ac80-961cd1534e40",
			"isEnabled": true,
			"type": "User",
			"userConsentDescription": "Allow the application full access to the todo service on your behalf",
			"userConsentDisplayName": "Have full access to the todo service",
			"value": "user_impersonation"
			}
		],

    Der Wert „id“ muss eine neu generierte GUID sein. Diese erstellen Sie, indem Sie ein [Tool zum Generieren von GUIDs](https://msdn.microsoft.com/library/ms241442%28v=vs.80%29.aspx) oder ein programmgesteuertes Verfahren verwenden. Die GUID repräsentiert einen eindeutigen Bezeichner für die Berechtigung, die von der Web-API bereitgestellt wird. Nachdem der Client ordnungsgemäß zum Anfordern des Zugriffs auf Ihre Web-API konfiguriert wurde und die Web-API-aufruft, stellt er ein OAuth 2.0 JWT-Token bereit, dessen Bereichsanspruch (scp) auf den oben genannten Wert festgelegt ist. In diesem Fall ist dies „user\_impersonation“.

	>[AZURE.NOTE] Sie können später bei Bedarf zusätzliche Berechtigungsbereiche bereitstellen. Berücksichtigen Sie, dass Ihre Web-API verschiedene Berechtigungen bereitstellen kann, die einer Vielzahl von unterschiedlichen Funktionen zugeordnet sind. Jetzt können Sie den Zugriff auf die Web-API steuern, indem Sie den Bereichsanspruch (scp) im empfangenen OAuth 2.0 JWT-Token verwenden.

1. Speichern Sie die aktualisierte JSON-Datei, und laden Sie sie durch Klicken auf die Schaltfläche **Manifest verwalten** auf der Befehlsleiste hoch. Wählen Sie **Manifest hochladen** aus, navigieren Sie zur aktualisierten Manifestdatei, und wählen Sie sie dann aus. Nach dem Hochladen ist Ihre Web-API jetzt so konfiguriert, dass sie von anderen Anwendungen im Verzeichnis verwendet werden kann.

#### So überprüfen Sie, ob die Web-API für andere Anwendungen in Ihrem Verzeichnis bereitgestellt wird

1. Klicken Sie im oberen Menü auf **Anwendungen**, und wählen Sie die Clientanwendung aus, für die Sie den Zugriff auf die Web-API konfigurieren möchten. Klicken Sie dann auf **Konfigurieren**.

1. Scrollen Sie nach unten zum Abschnitt "Berechtigungen für andere Anwendungen". Klicken Sie auf das Dropdownmenü „Anwendung auswählen“. Sie können nun die Web-API auswählen, für die Sie soeben eine Berechtigung bereitgestellt haben. Wählen Sie im Dropdownmenü „Delegierte Berechtigungen“ die neue Berechtigung aus.

![Anzeige von Aufgabenlistenberechtigungen](./media/active-directory-integrating-applications/listpermissions.png)

#### Weitere Informationen zum Anwendungsmanifest
Das Anwendungsmanifest ist praktisch ein Mechanismus zum Aktualisieren der Anwendungsentität, der alle Attribute der Identitätskonfiguration einer Azure AD-Anwendung einschließlich der erörterten API-Berechtigungsbereiche definiert. Weitere Informationen zur Anwendungsentität finden Sie in der [Dokumentation zur Anwendungsentität der Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#EntityreferenceApplicationEntity). Dort finden Sie umfassende Referenzinformationen zu den Anwendungsentitätselementen, die verwendet werden, um Berechtigungen für Ihre API anzugeben:

- Das appRoles-Mitglied, das eine Sammlung von [AppRole](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#AppRoleType)-Entitäten darstellt, die zum Definieren der **Anwendungsberechtigungen** für eine Web-API verwendet werden können  
- Das oauth2Permissions-Mitglied, das eine Sammlung von [OAuth2Permission](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#OAuth2PermissionType)-Entitäten darstellt, die zum Definieren der **delegierten Berechtigungen** für eine Web-API verwendet werden können

Weitere Informationen zu Anwendungsmanifestkonzepten im Allgemeinen finden Sie unter [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](active-directory-application-manifest.md).

### Zugreifen auf die Azure AD-Graph- und Office 365-APIs

Wie bereits zuvor erwähnt, können Sie zusätzlich zum Verfügbarmachen von/Zugreifen auf APIs über Ihre eigenen Ressourcenanwendungen auch Ihre Clientanwendung für den Zugriff auf APIs aktualisieren, die von Microsoft-Ressourcen verfügbar gemacht werden. Die Azure AD Graph-API, die in der Liste der Berechtigungen für andere Anwendungen als „Azure Active Directory“ bezeichnet wird, ist standardmäßig für alle Anwendungen verfügbar, die mit Azure AD registriert sind. Wenn Sie Ihre Clientanwendung in einem Azure AD-Mandanten registrieren, der von Office 365 bereitgestellt wurde, können Sie auch auf alle Berechtigungen zugreifen, die durch die APIs für verschiedene Office 365-Ressourcen verfügbar gemacht werden.

Eine vollständige Erläuterung zu Berechtigungsbereichen, die verfügbar gemacht werden durch:

- die Graph-API von Azure AD, finden Sie im Artikel [Berechtigungsbereiche | Graph-API-Konzepte](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes).
- Office 365-APIs, finden Sie im Artikel [Authentifizierung und Autorisierung mit dem allgemeinen Consent Framework](https://msdn.microsoft.com/office/office365/howto/application-manifest). Unter [Einrichten der Office 365-Entwicklungsumgebung](https://msdn.microsoft.com/office/office365/HowTo/setup-development-environment) finden Sie eine umfangreiche Erörterung zum Erstellen einer Client-App, die in Office 365-APIs integriert wird.

>[AZURE.NOTE] Aufgrund einer aktuellen Einschränkung können systemeigene Clientanwendungen die Azure AD Graph-API nur aufrufen, wenn sie die Berechtigung „Zugriff auf das Verzeichnis Ihrer Organisation“ verwenden. Diese Einschränkung gilt nicht für Webanwendungen.

### Konfigurieren von mehrinstanzenfähigen Anwendungen

Wenn Sie Azure AD eine Anwendung hinzufügen, möchten Sie vielleicht, dass auf Ihre Anwendung nur durch Benutzer in Ihrer Organisation zugegriffen werden kann. Alternativ sollen evtl. nur Benutzer in externen Organisationen auf Ihre Anwendung zugreifen können. Diese zwei Anwendungstypen werden als Einzelinstanzanwendung bzw. als mehrinstanzenfähige Anwendung bezeichnet. Sie können die Konfiguration einer Einzelinstanzanwendung so ändern, dass Sie eine mehrinstanzenfähige Anwendung erhalten. Dies wird weiter unten in diesem Abschnitt behandelt.

Es ist wichtig, die Unterschiede zwischen einer Einzelinstanzanwendung und einer mehrinstanzenfähigen Anwendung zu verstehen:

- Eine Einzelinstanzanwendung ist für die Verwendung in einem einzelnen Unternehmen gedacht. Es handelt sich dabei zumeist um eine Branchenanwendung (Line-of-Business, LOB), die von einem Unternehmensentwickler geschrieben wurde. Auf eine Einzelinstanzanwendung greifen nur Benutzer aus einem einzelnen Verzeichnis zu. Daher muss sie auch nur in einem Verzeichnis bereitgestellt werden.
- Eine mehrinstanzenfähige Anwendung ist für die Verwendung in vielen Organisationen vorgesehen. Hierbei handelt es sich um eine SaaS-Webanwendung (Software-as-a-Service), die in der Regel von einem unabhängigen Softwarehersteller (ISV, Independent Software Vendor) geschrieben wurde. Mehrinstanzenfähige Anwendungen müssen in jedem Verzeichnis bereitgestellt werden, in dem sie verwendet werden. Für die Registrierung ist also jeweils die Zustimmung des Benutzers oder Administrators erforderlich, unterstützt durch das Azure AD-Consent Framework. Beachten Sie, dass alle systemeigenen Clientanwendungen standardmäßig mehrinstanzenfähig sind, da sie auf dem Gerät des Ressourcenbesitzers installiert sind. Weitere Informationen zum Consent Framework finden Sie oben in der „Übersicht über das Consent Framework“.

#### Aktivieren von externen Benutzern für das Gewähren des Zugriffs

Wenn Sie eine Anwendung schreiben, die Sie Ihren Kunden oder Partnern außerhalb Ihrer Organisation zur Verfügung stellen möchten, müssen Sie die Anwendungsdefinition im klassischen Azure-Portal aktualisieren.

>[AZURE.NOTE] Wenn Sie die Mehrinstanzenfähigkeit aktivieren, müssen Sie sicherstellen, dass der App-ID-URI Ihrer Anwendung zu einer überprüften Domäne gehört. Darüber hinaus muss die Rückgabe-URL mit "https://" beginnen. Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory-application-objects.md).

##### So aktivieren Sie den Zugriff auf Ihre App für externe Benutzer

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol und anschließend auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Anwendung, die Sie konfigurieren möchten. Die Seite „Schnellstart“ wird mit Konfigurationsoptionen angezeigt.

1. Erweitern Sie auf der Seite „Schnellstart“ den Abschnitt **Mehrinstanzenfähige Anwendung konfigurieren**, und klicken Sie dann im Abschnitt „Zugriff aktivieren“ auf den Link **Jetzt konfigurieren**. Die Seite mit den Anwendungseigenschaften wird angezeigt.

1. Klicken Sie neben „Die Anwendung ist mehrinstanzenfähig“ auf **Ja**, und klicken Sie dann in der Befehlsleiste auf die Schaltfläche **Speichern**.

Nachdem Sie die oben genannten Änderungen vorgenommen haben, können Benutzer und Administratoren in anderen Organisationen Ihrer Anwendung Zugriff auf ihr Verzeichnis und andere Daten gewähren.

### Auslösen von Azure AD-Consent Framework zur Laufzeit

Zur Verwendung des Consent Frameworks müssen mehrinstanzenfähige Clientanwendungen mithilfe von OAuth 2.0 eine Autorisierung anfordern. Es stehen [Codebeispiele](https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multi-tenant) zur Verfügung, die zeigen, wie eine Webanwendung, eine native Anwendung oder eine Server-/Daemonanwendung Autorisierungscodes und Zugriffstoken zum Aufrufen von Web-APIs anfordert.

Ihre Webanwendung kann ggf. auch eine Registrierungsbenutzeroberfläche für Benutzer zur Verfügung stellen. Wenn Sie eine Registrierungsbenutzeroberfläche anbieten, muss der Benutzer üblicherweise auf eine Schaltfläche zur Registrierung klicken, die den Browser an den Azure AD OAuth2.0-Autorisierungsendpunkt oder einen OpenID Connect-Endpunkt mit Benutzerinformationen umleitet. Diese Endpunkte ermöglichen es der Anwendung, Informationen zum neuen Benutzer durch Untersuchen des id\_token-Objekts abzurufen. Nach der Registrierungsphase wird der Benutzer, ähnlich wie oben im Abschnitt „Übersicht über das Consent Framework“ gezeigt, zur Zustimmung aufgefordert.

Alternativ kann Ihre Webanwendung auch eine Benutzeroberfläche bereitstellen, die es Administratoren ermöglicht, ihr Unternehmen zu registrieren. Eine solche Benutzeroberfläche würde den Benutzer ebenfalls an den Azure AD-OAuth 2.0-Autorisierungsendpunkt umleiten. In diesem Fall können Sie auch einen Parameter „prompt=admin\_consent“ an den Autorisierungsendpunkt übergeben, um das Aufrufen der Benutzeroberfläche für die Administratorzustimmung zu erzwingen, in der der Administrator die Zustimmung im Namen seiner Organisation erteilt. Nur ein Benutzer, der sich mit einem Konto authentifiziert, das zur globalen Administratorrolle gehört, kann zustimmen; andere Benutzer erhalten eine Fehlermeldung. Bei erfolgter Zustimmung enthält die Antwort "admin\_consent=true". Bei der Einlösung eines Zugriffstokens erhalten Sie ebenfalls ein "id\_token", das Informationen zu der Organisation und zum Administrator bereitstellt, der sich für Ihre Anwendung registriert hat.

#### Aktivieren der impliziten OAuth 2.0-Gewährung für Single-Page-Anwendungen

Single-Page-Anwendungen (SPAs) sind in der Regel mit einem JavaScript-intensiven Front-End aufgebaut, das im Browser ausgeführt wird, der das Web-API-Back-End der Anwendung zum Ausführen ihrer Geschäftslogik aufruft. Für SPAs, die in Azure AD gehostet werden, verwenden Sie die implizite OAuth 2.0-Gewährung zum Authentifizieren des Benutzers für Azure AD und rufen ein Token ab, mit dem Sie Aufrufe vom JavaScript-Client der Anwendung an ihre Back-End-Web-API sichern können. Nachdem der Benutzer seine Zustimmung erteilt hat, können mit dem gleichen Authentifizierungsprotokoll Token zum Sichern von Aufrufen zwischen dem Client und anderen für die Anwendung konfigurierten Web-API-Ressourcen abgerufen werden. Die implizite OAuth 2.0-Gewährung ist für Anwendungen standardmäßig deaktiviert. Sie können die implizite OAuth 2.0-Gewährung für Ihre Anwendung aktivieren, indem Sie den Wert `oauth2AllowImplicitFlow` in deren [Anwendungsmanifest](active-directory-application-manifest.md) festlegen. Dabei handelt es sich um eine JSON-Datei, welche die Identitätskonfiguration Ihrer Anwendung darstellt.

##### So aktivieren Sie die implizite OAuth 2.0-Gewährung

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.
1. Klicken Sie im linken Menü auf das **Active Directory**-Symbol und anschließend auf das gewünschte Verzeichnis.
1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Anwendung, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.
1. Klicken Sie auf der Befehlsleiste auf die Schaltfläche **Manifest verwalten**, und wählen Sie dann **Manifest herunterladen** aus. Öffnen Sie die JSON-Manifestdatei der Anwendung, und legen Sie den Wert „oauth2AllowImplicitFlow“ auf „true“ fest. Der Standardwert ist „false“.

    `"oauth2AllowImplicitFlow": true,`

1. Speichern Sie die aktualisierte JSON-Datei, und laden Sie sie durch Klicken auf die Schaltfläche **Manifest verwalten** auf der Befehlsleiste hoch. Wählen Sie **Manifest hochladen** aus, navigieren Sie zur aktualisierten Manifestdatei, und wählen Sie sie dann aus. Nachdem das Manifest hochgeladen wurde, ist Ihre Web-API für die Verwendung der impliziten OAuth 2.0-Gewährung zum Authentifizieren von Benutzern konfiguriert.


### Legacybenutzeroberfläche zum Gewähren des Zugriffs

Dieser Abschnitt beschreibt die Legacybenutzeroberfläche für die Zustimmung, die bis zum 12. März 2014 verwendet wurde. Diese Benutzeroberfläche wird weiterhin unterstützt und wird im Folgenden beschrieben. Vor Einführung der neuen Funktionalität konnten Sie nur die folgenden Berechtigungen erteilen:

- Anmelden von Benutzern aus ihrer Organisation

- Anmelden von Benutzern und Lesen der Verzeichnisdaten ihrer Organisation (nur als Anwendung)

- Anmelden von Benutzern und Lesen und Schreiben der Verzeichnisdaten ihrer Organisation (nur als Anwendung)

Sie können die unter [Entwickeln von mehrinstanzenfähigen Webanwendungen mit Azure AD](https://msdn.microsoft.com/library/azure/dn151789.aspx) beschriebenen Schritte ausführen, um Zugriff für neue Anwendungen zu erteilen, die in Azure AD registriert wurden. Beachten Sie unbedingt, dass das neue Consent Framework viel leistungsfähigere Anwendungen ermöglicht und darüber hinaus Benutzern (nicht nur Administratoren) die Möglichkeit bietet, ihre Zustimmung zu diesen Anwendungen zu erteilen.

#### Erstellen des Links, der Zugriff für externe Benutzer erteilt (Legacy)

Damit sich externe Benutzer mithilfe ihrer Organisationskonten für Ihre App registrieren können, müssen Sie Ihre App so aktualisieren, dass Sie eine Schaltfläche anzeigt, mit der die Benutzer auf die Azure AD-Seite zur Erteilung von Zugriff weitergeleitet werden. Einen Brandingleitfaden für diese Registrierungsschaltfläche finden Sie im Thema [Brandingrichtlinien für integrierte Apps](active-directory-branding-guidelines.md). Nachdem der Benutzer den Zugriff gewährt oder verweigert hat, leitet die Azure AD-Seite zum Erteilen des Zugriffs den Browser mit einer Antwort wieder an Ihre App um. Weitere Informationen zu Anwendungseigenschaften finden Sie unter [Anwendungsobjekte und Dienstprinzipale](active-directory-application-objects.md).

Die Seite zum Erteilen des Zugriffs wird von Azure AD erstellt. Sie finden einen Link zu dieser Seite im klassischen Azure-Portal auf der Seite für die Konfiguration der App. Klicken Sie zum Aufrufen der Seite „Konfiguration“ im oberen Menü Ihres Azure AD-Mandanten auf den Link **Anwendungen**, und klicken Sie auf die App, die Sie konfigurieren möchten. Klicken Sie anschließend im oberen Menü der Seite „Schnellstart“ auf **Konfigurieren**.

Der Link für Ihre Anwendung sieht folgendermaßen aus: `http://account.activedirectory.windowsazure.com/Consent.aspx?ClientID=058eb9b2-4f49-4850-9b78-469e3176e247&RequestedPermissions=DirectoryReaders&ConsentReturnURL=https%3A%2F%2Fadatum.com%2FExpenseReport.aspx%3FContextId%3D123456`. In der folgenden Tabelle werden die Bestandteile des Links beschrieben:

|Parameter|Beschreibung|
|---|---|
|ClientId|Erforderlich. Die Client-ID, die Sie beim Hinzufügen Ihrer App erhalten haben.|
|RequestedPermissions|Optional. Die Zugriffsebene, die Ihre App anfordert. Diese wird dem Benutzer angezeigt, der Ihrer App den Zugriff gewährt. Wenn keine Angabe erfolgt, wird als angeforderte Zugriffsebene standardmäßig nur einmaliges Anmelden verwendet. Die weiteren Optionen sind "DirectoryReaders" und "DirectoryWriters". Weitere Informationen zu diesen Berechtigungen finden Sie unter "Anwendungszugriffsebenen".|
|ConsentReturnUrl|Optional. Die URL, an die die Antwort zum Erteilen des Zugriffs zurückgegeben werden soll. Dieser Wert muss URL-codiert sein und sich in derselben Domäne wie die Antwort-URL befinden, die in Ihrer Anwendungsdefinition konfiguriert wurde. Wenn keine Angabe erfolgt, wird die Antwort zum Erteilen des Zugriffs an die konfigurierte Antwort-URL weitergeleitet.|

Wenn Sie einen Wert für "ConsentReturnUrl" angeben, der sich von der Antwort-URL unterscheidet, kann Ihre App separate Logik implementieren, die die Antwort für eine andere URL aus der Antwort-URL verarbeiten kann (die normalerweise SAML-Token für die Anmeldung verarbeitet). Sie können außerdem zusätzliche Parameter in der ConsentReturnURL-codierten URL angeben. Diese werden als Abfragezeichenfolgenparameter bei der Umleitung zurück an Ihre Anwendung übergeben. Dieser Mechanismus kann verwendet werden, um zusätzliche Informationen zu verwalten oder die Anforderung Ihrer App für eine Zugriffserteilung an die Antwort von Azure AD zu binden.

#### Benutzeroberfläche zum Erteilen des Zugriffs und Antwort (Legacy)

Wenn eine Anwendung eine Weiterleitung an den Link zum Erteilen des Zugriffs durchführt, zeigen die folgenden Abbildungen, welche Benutzeroberfläche verwendet wird.

Wenn der Benutzer noch nicht angemeldet ist, wird er zur Anmeldung aufgefordert:

![Anmeldung bei AAD](./media/active-directory-integrating-applications/signintoaad.png)

Nachdem der Benutzer authentifiziert wurde, leitet Azure AD den Benutzer auf die Seite zum Erteilen des Zugriffs um:

![Erteilen von Zugriff](./media/active-directory-integrating-applications/grantaccess.png)

>[AZURE.NOTE] Nur der Unternehmensadministrator der externen Organisation kann den Zugriff auf Ihre App erteilen. Wenn der Benutzer kein Unternehmensadministrator ist, wird eine Option zum Senden einer Nachrichten an den Unternehmensadministrator bereitgestellt, damit Zugriff für die App angefordert werden kann.

Nachdem der Kunde über "Zugriff gewähren" den Zugriff gewährt oder durch Klicken auf "Abbrechen" den Zugriff verweigert hat, sendet Azure AD eine Antwort an die "ConsentReturnUrl" oder an die konfigurierte Antwort-URL. Diese Antwort enthält die folgenden Parameter:

|Parameter|Beschreibung|
|---|---|
|TenantId|Die eindeutige ID der Organisation in Azure AD, die Zugriff für Ihre App gewährt hat. Dieser Parameter wird nur angegeben, wenn der Kunde den Zugriff gewährt hat.|
|Zustimmung|Der Wert wird auf "Gewährt" festgelegt, wenn der App der Zugriff gewährt wurde, oder auf "Verweigert", wenn die Anforderung abgelehnt wurde.|

Weitere Parameter werden an die Anwendung zurückgegeben, wenn sie als Bestandteil der ConsentReturnUrl-codierten URL angegeben wurden. Das folgende Beispiel zeigt eine Antwort auf eine Anforderung zum Erteilen des Zugriffs, die angibt, dass die Anwendung autorisiert wurde. Die Antwort enthält außerdem eine „ContextID“, die in der Anforderung zum Erteilen des Zugriffs bereitgestellt wurde: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Granted&TenantId=f03dcba3-d693-47ad-9983-011650e64134`.

>[AZURE.NOTE] Die Antwort zum Erteilen des Zugriffs enthält kein Sicherheitstoken für den Benutzer. Die App muss den Benutzer separat anmelden.

Das folgende Beispiel zeigt eine Antwort auf eine Anforderung zum Erteilen des Zugriffs, die verweigert wurde: `https://adatum.com/ExpenseReport.aspx?ContextID=123456&Consent=Denied`

#### Rollover von App-Schlüsseln für den ununterbrochenen Zugriff auf die Graph-API (Legacy)

Während der Lebensdauer Ihrer App müssen Sie ggf. die Schlüssel ändern, die Sie verwenden, um bei Azure AD ein Zugriffstoken für den Aufruf der Graph-API anzufordern. Es gibt im Allgemeinen zwei Fälle, in denen Schlüssel geändert werden müssen: Rollover nach einem Notfall, wenn der Schlüssel kompromittiert wurde, oder Rollover, wenn Ihr aktueller Schlüssel bald abläuft. Das folgende Verfahren sollte verwendet werden, um einen unterbrechungsfreien Zugriff auf Ihre App zu gewährleisten, während Sie Ihre Schlüssel (in erster Linie für den zweiten Fall) aktualisieren.

1. Klicken Sie im klassischen Azure-Portal auf Ihren Verzeichnismandanten, klicken Sie im oberen Menü auf **Anwendungen** und dann auf die App, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.

1. Klicken Sie im oberen Menü auf **Konfigurieren**, um eine Liste der Eigenschaften Ihrer App anzuzeigen. Es wird eine Liste Ihrer Schlüssel angezeigt.

1. Klicken Sie unterhalb von „Schlüssel“ auf die Dropdownliste mit dem Titel **Dauer auswählen**, wählen Sie 1 oder 2 Jahre aus, und klicken Sie dann in der Befehlsleiste auf **Speichern**. Ein neuer Kennwortschlüssel für die App wird generiert. Kopieren Sie diesen neuen Kennwortschlüssel. Zu diesem Zeitpunkt können Sie sowohl den vorhandenen als auch den neuen Schlüssel für die App verwenden, um ein Zugriffstoken aus Azure AD abzurufen.

1. Kehren Sie zu Ihrer App zurück, und aktualisieren Sie die Konfiguration, um mit der Verwendung des neuen Kennwortschlüssels zu beginnen. Ein Beispiel dafür, wo diese Aktualisierung stattfinden sollte, finden Sie unter [Verwenden der Graph-API zum Abfragen von Azure AD](https://msdn.microsoft.com/library/azure/dn151791.aspx).

1. Sie sollten nun ein Rollout dieser Änderung für Ihre Produktionsumgebung ausführen. Überprüfen Sie die Konfiguration hierbei zunächst auf einem Dienstknoten, bevor Sie das Rollout in der gesamten Umgebung ausführen.

1. Sobald das Update für die Produktionsbereitstellung abgeschlossen ist, können Sie zum klassischen Azure-Portal zurückkehren und den alten Schlüssel entfernen.

#### Ändern der App-Eigenschaften nach dem Aktivieren des Zugriffs (Legacy)

Nachdem Sie den Zugriff auf Ihre App für externe Benutzer aktiviert haben, können Sie weiterhin Änderungen an den Eigenschaften Ihrer App im klassischen Azure-Portal vornehmen. Kunden allerdings, die bereits Zugriff auf Ihre App erteilt haben, bevor Sie Änderungen an der App vorgenommenen haben, sehen diese Änderungen beim Anzeigen von Details zu Ihrer Anwendung im klassischen Azure-Portal nicht. Sobald die Anwendung Kunden zur Verfügung gestellt wurde, müssen Sie sehr vorsichtig sein, wenn Sie bestimmte Änderungen vornehmen. Wenn Sie z. B. den App-ID-URI aktualisieren, können sich Ihre vorhandenen Kunden, die den Zugriff vor dieser Änderung erteilt haben, nicht über ihre Geschäfts- oder Schulkonten bei Ihrer App anmelden.

Wenn Sie eine Änderung an "RequestedPermissions" vornehmen, um eine höhere Zugriffsebene anzufordern, erhalten vorhandene Kunden möglicherweise die Antwort "Zugriff verweigert" von der Graph-API, wenn sie App-Funktionen nutzen, die neue API-Aufrufe mit einer höheren Zugriffsebene verwenden. Ihre App sollte dem Kunden in diesen Fällen die Möglichkeit bieten, den Zugriff auf Ihre App mit einer höhere Zugriffsebene zu gewähren.

## Entfernen einer Anwendung

In diesem Abschnitt wird die Vorgehensweise beim Entfernen einer Anwendung aus Ihrem Azure AD-Mandanten beschrieben.

### Entfernen einer Anwendung, die von Ihrer Organisation erstellt wurde
Dies sind die Anwendungen, die unter den Filter „Anwendungen im Besitz meines Unternehmens“ auf der Hauptseite „Anwendungen“ für Ihren Azure AD-Mandanten angezeigt werden. Technisch gesehen sind dies Anwendungen, die Sie entweder manuell über das klassische Azure-Portal oder programmgesteuert über PowerShell oder die Graph-API registriert haben. Genauer gesagt, werden sie sowohl durch ein Anwendungs- als auch Dienstprinzipalobjekt in Ihrem Mandanten dargestellt. Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory-application-objects.md).

#### So entfernen Sie eine Einzelinstanzanwendung aus Ihrem Verzeichnis

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol, und klicken Sie dann auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Anwendung, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.

1. Klicken Sie in der Befehlsleiste auf die Schaltfläche **Löschen**.

1. Klicken Sie in der Bestätigungsmeldung auf **Ja**.

#### So entfernen Sie eine mehrinstanzenfähige Anwendung aus Ihrem Verzeichnis

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.

1. Klicken Sie im linken Menü auf das Active Directory-Symbol, und klicken Sie dann auf das gewünschte Verzeichnis.

1. Klicken Sie im oberen Menü auf **Anwendungen**, und klicken Sie dann auf die Anwendung, die Sie konfigurieren möchten. Die Seite "Schnellstart" wird geöffnet und zeigt Informationen zum einmaligen Anmelden (SSO) und weitere Konfigurationsinformationen an.

1. Klicken Sie im Abschnitt „Die Anwendung ist mehrinstanzenfähig“ auf **Nein**. Dies konvertiert Ihre Anwendung in eine Einzelinstanzanwendung. Die Anwendung verbleibt jedoch weiterhin in einer Organisation, die bereits ihre Zustimmung für diese Anwendung erteilt hat.

1. Klicken Sie in der Befehlsleiste auf die Schaltfläche **Löschen**.

1. Klicken Sie in der Bestätigungsmeldung auf **Ja**.

### Entfernen einer mehrinstanzenfähigen Anwendung, die von einer anderen Organisation autorisiert ist
Dies ist eine Teilmenge der Anwendungen, die unter dem Filter „Anwendungen, die mein Unternehmen verwendet“ auf der Hauptseite „Anwendungen“ für Ihren Azure AD-Mandanten angezeigt werden, insbesondere diejenigen, die nicht in der Liste „Anwendungen im Besitz meines Unternehmens“ aufgeführt sind. Technisch gesehen sind dies mehrinstanzenfähige Anwendungen, die bei der Zustimmung registriert wurden. Genauer gesagt, werden sie nur durch ein Dienstprinzipalobjekt in Ihrem Mandanten dargestellt. Weitere Informationen finden Sie unter [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory-application-objects.md).

Um den Zugriff einer mehrinstanzenfähigen Anwendung auf Ihr Verzeichnis zu entziehen (nachdem die Zustimmung erteilt wurde), muss der Unternehmensadministrator über ein Azure-Abonnement zum Entziehen des Zugriffs über das klassische Azure-Portal verfügen. Sie navigieren einfach zur Konfigurationsseite der Anwendung und klicken am unteren Rand auf die Schaltfläche „Zugriff verwalten“. Alternativ kann der Unternehmensadministrator [Azure AD PowerShell-Cmdlets](http://go.microsoft.com/fwlink/?LinkId=294151) verwenden, um den Zugriff zu entziehen.

## Nächste Schritte

- Siehe Abschnitt [Brandingrichtlinien für integrierte Apps](active-directory-branding-guidelines.md)

- Weitere Informationen über [Anwendungsobjekte und Dienstprinzipalobjekte](active-directory-application-objects.md)

- [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](active-directory-application-manifest.md)

- Besuchen Sie das [Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md).

<!---HONumber=AcomDC_0413_2016-->