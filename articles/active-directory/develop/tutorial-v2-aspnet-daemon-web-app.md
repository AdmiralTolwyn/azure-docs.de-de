---
title: Erstellen eines mehrinstanzenfähigen Daemons, der den Microsoft Identity Platform-Endpunkt verwendet
description: In diesem Tutorial erfahren Sie, wie Sie von einer Windows-Desktopanwendung (WPF) aus eine durch Azure Active Directory geschützte ASP.NET-Web-API aufrufen. Der WPF-Client authentifiziert einen Benutzer, fordert ein Zugriffstoken an und ruft die Web-API auf.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/10/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:ASP.NET
ms.openlocfilehash: b9c40d93c48bcf5959b5d9651510ce6076eb789e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201753"
---
# <a name="tutorial-build-a-multitenant-daemon-that-uses-the-microsoft-identity-platform-endpoint"></a>Tutorial: Erstellen eines mehrinstanzenfähigen Daemons, der den Microsoft Identity Platform-Endpunkt verwendet

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Microsoft Identity Plattform auf die Daten von Microsoft-Geschäftskunden in einem nicht interaktiven Prozess mit langer Ausführungsdauer zugreifen. Der Beispiel-Daemon verwendet die [Gewährung von OAuth 2.0-Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md), um ein Zugriffstoken abzurufen. Dieses Token wird dann vom Daemon verwendet, um [Microsoft Graph](https://graph.microsoft.io) aufzurufen und auf Organisationsdaten zuzugreifen.

> [!div class="checklist"]
> * Integrieren einer Daemon-App in Microsoft Identity Platform
> * Erteilen von Anwendungsberechtigungen direkt für die App durch einen Administrator
> * Abrufen eines Zugriffstokens zum Aufrufen der Microsoft Graph-API
> * Rufen Sie die Microsoft Graph-API auf.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

Die App wird als ASP.NET-MVC-Anwendung erstellt. Sie verwendet die OWIN OpenID Connect-Middleware für die Benutzeranmeldung.

Bei der Daemon-Komponente in diesem Beispiel handelt es sich um einen API-Controller (`SyncController.cs`). Wenn der Controller aufgerufen wird, pullt er von Microsoft Graph eine Liste mit Benutzern im Azure AD-Mandanten (Azure Active Directory) des Kunden. `SyncController.cs` wird durch einen AJAX-Aufruf in der Webanwendung ausgelöst. Er verwendet die [Microsoft-Authentifizierungsbibliothek (MSAL) für .NET](msal-overview.md), um ein Zugriffstoken für Microsoft Graph abzurufen.

>[!NOTE]
> Wenn Sie mit Microsoft Identity Platform noch nicht vertraut sind, wird empfohlen, mit dem Schnellstart [Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über die Identität einer Konsolen-App](quickstart-v2-netcore-daemon.md) zu beginnen.

## <a name="scenario"></a>Szenario

Da es sich bei der App um eine mehrinstanzenfähige App für Microsoft-Geschäftskunden handelt, müssen Kunden die Möglichkeit haben, sich zu registrieren oder die Anwendung mit ihren Unternehmensdaten zu verknüpfen. Im Zuge der Verknüpfung weist ein Unternehmensadministrator der App zunächst direkt *Anwendungsberechtigungen* zu, damit sie auf nicht interaktive Weise (ohne Beteiligung eines angemeldeten Benutzers) auf Unternehmensdaten zugreifen kann. Der Großteil der Logik in diesem Beispiel zeigt, wie diese Verknüpfung mithilfe des Identity Platform-Endpunkts [Administratoreinwilligung](v2-permissions-and-consent.md#using-the-admin-consent-endpoint) implementiert wird.

![Topologie](./media/tutorial-v2-aspnet-daemon-webapp/topology.png)

Weitere Informationen zu den Konzepten in diesem Beispiel finden Sie unter [Microsoft Identity Platform und der Fluss von OAuth 2.0-Clientanmeldeinformationen](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Voraussetzungen

Für das Beispiel in dieser Schnellstartanleitung benötigen Sie Folgendes:

- [Visual Studio 2017 oder 2019](https://visualstudio.microsoft.com/downloads/)
- Einen Azure AD-Mandanten. Weitere Informationen finden Sie unter [Schnellstart: Einrichten eines Mandanten](quickstart-create-new-tenant.md).
- Mindestens ein Benutzerkonto in Ihrem Azure AD-Mandanten. Dieses Beispiel funktioniert nicht mit einem Microsoft-Konto (ehemals Windows Live-Konto). Falls Sie sich beim [Azure-Portal](https://portal.azure.com) mit einem Microsoft-Konto angemeldet und bislang noch kein Benutzerkonto in Ihrem Verzeichnis erstellt haben, müssen Sie jetzt eines erstellen.

## <a name="clone-or-download-this-repository"></a>Klonen oder Herunterladen des Repositorys

Geben Sie in Ihrer Shell oder Befehlszeile den folgenden Befehl ein:

```Shell
git clone https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2.git
```

Oder [laden Sie das Beispiel in einer ZIP-Datei herunter](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/archive/master.zip).

## <a name="register-your-application"></a>Anwendung registrieren

Dieses Beispiel enthält ein einzelnes Projekt. Zum Registrieren der Anwendung bei Ihrem Azure AD-Mandanten haben Sie folgende Möglichkeiten:

- Mithilfe der Schritte [Registrieren der Beispielanwendung bei Ihrem Azure AD-Mandanten](#register-your-application) und [Auswählen des Azure AD-Mandanten für die Anwendungen](#choose-the-azure-ad-tenant)
- Mithilfe von PowerShell-Skripts, die:
  - *Automatisch* die Azure AD-Anwendungen und die zugehörigen Objekte (Kennwörter, Berechtigungen, Abhängigkeiten) für Sie erstellen.
  - Die Konfigurationsdateien der Visual Studio-Projekte ändern.

Wenn Sie die Automatisierung verwenden möchten, gehen Sie wie folgt vor:

1. Führen Sie unter Windows PowerShell aus, und navigieren Sie zum Stamm des geklonten Verzeichnisses.
1. Führen Sie den folgenden Befehl aus:

   ```PowerShell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
   ```

1. Führen Sie das Skript aus, um Ihre Azure AD-Anwendung zu erstellen und den Code der Beispielanwendung entsprechend zu konfigurieren:

   ```PowerShell
   .\AppCreationScripts\Configure.ps1
   ```

   Weitere Skriptausführungsmethoden werden [hier](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/blob/master/AppCreationScripts/AppCreationScripts.md) beschrieben.

1. Öffnen Sie die Visual Studio-Projektmappe, und wählen Sie **Starten** aus, um den Code auszuführen.

Falls Sie die Automatisierung nicht verwenden möchten, führen Sie die Schritte in den folgenden Abschnitten aus.

### <a name="choose-the-azure-ad-tenant"></a>Erstellen des Azure AD-Mandanten

1. Melden Sie sich mit einem Geschäfts-, Schul- oder Unikonto oder mit einem persönlichen Microsoft-Konto beim [Azure-Portal](https://portal.azure.com) an.
1. Sollte sich Ihr Konto in mehreren Azure AD-Mandanten befinden, wählen Sie im Menü oben auf der Seite Ihr Profil und anschließend **Verzeichnis wechseln** aus.
1. Ändern Sie die Portalsitzung zum gewünschten Azure AD-Mandanten.

### <a name="register-the-client-app-dotnet-web-daemon-v2"></a>Registrieren der Client-App (dotnet-web-daemon-v2)

1. Navigieren Sie in Microsoft Identity Platform für Entwickler zur Seite [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908).
1. Wählen Sie **Neue Registrierung** aus.
1. Geben Sie auf der daraufhin angezeigten Seite **Anwendung registrieren** die Registrierungsinformationen für Ihre Anwendung ein:
   - Geben Sie im Abschnitt **Name** einen aussagekräftigen Anwendungsnamen ein, der den Benutzern der App angezeigt wird. Geben Sie beispielsweise **dotnet-web-daemon-v2** ein.
   - Wählen Sie im Abschnitt **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
   - Wählen Sie im Abschnitt **Umleitungs-URI (optional)** im Kombinationsfeld die Option **Web** aus, und geben Sie die folgenden Umleitungs-URIs ein:
       - **https://localhost:44316/**
       - **https://localhost:44316/Account/GrantPermissions**

     Sind mehr als zwei Umleitungs-URIs vorhanden, müssen diese nach erfolgreicher Erstellung der App auf der Registerkarte **Authentifizierung** hinzugefügt werden.
1. Wählen Sie **Registrieren** aus, um die Anwendung zu erstellen.
1. Suchen Sie auf der Seite **Übersicht** der App den Wert **Anwendungsclient-ID**, und notieren Sie ihn zur späteren Verwendung. Sie benötigen diesen Wert, um die Visual Studio-Konfigurationsdatei für dieses Projekt zu konfigurieren.
1. Wählen Sie in der Liste mit den Seiten für die App die Option **Authentifizierung** aus. Führen Sie dann folgende Schritte aus:
   - Legen Sie im Abschnitt **Erweiterte Einstellungen** die Option **Abmelde-URL** auf **https://localhost:44316/Account/EndSession** fest.
   - Wählen Sie im Abschnitt **Erweiterte Einstellungen** > **Implizite Gewährung** die Optionen **Zugriffstoken** und **ID-Token** aus. Für dieses Beispiel muss der [Flow zur impliziten Gewährung](v2-oauth2-implicit-grant-flow.md) aktiviert werden, um den Benutzer anzumelden und eine API aufzurufen.
1. Wählen Sie **Speichern** aus.
1. Wählen Sie auf der Seite **Zertifikate & Geheimnisse** im Abschnitt **Geheime Clientschlüssel** die Option **Neuer geheimer Clientschlüssel** aus. Führen Sie dann folgende Schritte aus:

   1. Geben Sie eine Schlüsselbeschreibung ein (beispielsweise **app secret**).
   1. Wählen Sie eine Schlüsseldauer aus: **In 1 Jahr**, **In 2 Jahren** oder **Läuft nie ab**.
   1. Wählen Sie die Schaltfläche **Hinzufügen** aus.
   1. Kopieren Sie den angezeigten Schlüsselwert, und speichern Sie ihn an einem sicheren Ort. Dieser Schlüssel wird später benötigt, um das Projekt in Visual Studio zu konfigurieren. Er wird nicht erneut angezeigt und kann auch nicht auf andere Weise abgerufen werden.
1. Wählen Sie in der Liste mit den Seiten für die App die Option **API-Berechtigungen** aus. Führen Sie dann folgende Schritte aus:
   1. Wählen Sie die Schaltfläche **Berechtigung hinzufügen**.
   1. Stellen Sie sicher, dass die Registerkarte **Microsoft-APIs** ausgewählt ist.
   1. Wählen Sie im Abschnitt **Häufig verwendete Microsoft-APIs** die Option **Microsoft Graph** aus.
   1. Vergewissern Sie sich im Abschnitt **Anwendungsberechtigungen**, dass die richtigen Berechtigungen ausgewählt sind: **User.Read.All**.
   1. Wählen Sie die Schaltfläche **Berechtigungen hinzufügen** aus.

## <a name="configure-the-sample-to-use-your-azure-ad-tenant"></a>Konfigurieren des Beispiels für die Verwendung Ihres Azure-Mandanten

In den folgenden Schritten ist **ClientID** mit „Anwendungs-ID“ oder **AppId** identisch.

Öffnen Sie die Projektmappe in Visual Studio, um die Projekte zu konfigurieren.

### <a name="configure-the-client-project"></a>Konfigurieren des Clientprojekts

Wenn Sie die Setupskripts verwendet haben, wurden die folgenden Änderungen für Sie angewendet.

1. Öffnen Sie die Datei **UserSync\Web.Config**.
1. Suchen Sie den App-Schlüssel **ida:ClientId**. Ersetzen Sie den vorhandenen Wert durch die aus dem Azure-Portal kopierte Anwendungs-ID der Anwendung **dotnet-web-daemon-v2**.
1. Suchen Sie den App-Schlüssel **ida:ClientSecret**. Ersetzen Sie den vorhandenen Wert durch den Schlüssel, den Sie beim Erstellen der App **dotnet-web-daemon-v2** im Azure-Portal gespeichert haben.

## <a name="run-the-sample"></a>Ausführen des Beispiels

Bereinigen Sie die Projektmappe, erstellen Sie die Projektmappe neu, führen Sie die UserSync-Anwendung aus, und melden Sie sich anschließend als Administrator bei Ihrem Azure AD-Mandanten an. Sollten Sie noch keinen Azure AD-Mandanten zum Testen haben, gehen Sie wie [hier](quickstart-create-new-tenant.md) beschrieben vor, um einen zu erhalten.

Wenn Sie sich anmelden, fordert die App von Ihnen zunächst Berechtigungen zum Anmelden sowie zum Lesen Ihres Benutzerprofils an. Mithilfe dieser Einwilligung kann die App überprüfen, ob es sich bei Ihnen um einen Geschäftsbenutzer handelt.

![Benutzerzustimmung](./media/tutorial-v2-aspnet-daemon-webapp/firstconsent.png)

Anschließend versucht die App, über Microsoft Graph eine Liste mit Benutzern aus Ihrem Azure AD-Mandanten zu synchronisieren. Sollte das nicht möglich ist, werden Sie (der Mandantenadministrator) aufgefordert, Ihren Mandanten mit der App zu verbinden.

Die App fordert dann eine Leseberechtigung für die Liste der Benutzer in Ihrem Mandanten an.

![Administratorzustimmung](./media/tutorial-v2-aspnet-daemon-webapp/adminconsent.PNG)

Nach Erteilung der Berechtigung werden Sie von der App abgemeldet. Durch diese Abmeldung wird sichergestellt, dass alle vorhandenen Zugriffstoken für Microsoft Graph aus dem Tokencache entfernt werden. Wenn Sie sich erneut anmelden, verfügt das neue Token über die erforderlichen Berechtigungen für Microsoft Graph-Aufrufe.


Nach Erteilung der Berechtigung kann die App jederzeit Benutzer abfragen. Dies können Sie überprüfen, indem Sie die Schaltfläche **Sync Users** (Benutzer synchronisieren) auswählen und die Benutzerliste aktualisieren. Fügen Sie einen Benutzer hinzu, oder entfernen Sie einen Benutzer, und synchronisieren Sie die Liste anschließend erneut. (Beachten Sie jedoch, dass nur die erste Seite mit Benutzern synchronisiert wird.)

## <a name="about-the-code"></a>Informationen zum Code

Der relevante Code für dieses Beispiel befindet sich in den folgenden Dateien:

- **App_Start\Startup.Auth.cs**, **Controllers\AccountController.cs**: Erste Anmeldung. Die Aktionen für den Controller verfügen über ein Autorisierungsattribut (**Authorize**), das die Anmeldung des Benutzers erzwingt. Die Anwendung verwendet den [Autorisierungscodeflow](v2-oauth2-auth-code-flow.md), um den Benutzer anzumelden.
- **Controllers\SyncController.cs**: Synchronisierung der Benutzerliste mit dem lokalen In-Memory-Speicher.
- **Controllers\UserController.cs**: Anzeige der Benutzerliste aus dem lokalen In-Memory-Speicher.
- **Controllers\AccountController.cs**: Anforderung von Berechtigungen vom Mandantenadministrator mithilfe des Endpunkts für die Administratoreinwilligung.

## <a name="re-create-the-sample-app"></a>Erneutes Erstellen der Beispiel-App

1. Erstellen Sie in Visual Studio ein neues **Visual C#-Projekt** vom Typ **ASP.NET-Webanwendung (.NET Framework)** .
1. Wählen Sie im nächsten Bildschirm die Projektvorlage **MVC** aus. Fügen Sie außerdem Ordner- und Kernverweise für **Web-API** hinzu, da Sie später noch einen Web-API-Controller hinzufügen. Behalten Sie den standardmäßig ausgewählten Authentifizierungsmodus bei: **Keine Authentifizierung**.
1. Wählen Sie im Fenster **Projektmappen-Explorer** das Projekt aus, und drücken Sie **F4**.
1. Legen Sie in den Projekteigenschaften die Eigenschaft **SSL aktiviert** auf **True** fest. Notieren Sie sich die Information unter **SSL-URL**. Sie wird beim Konfigurieren der Registrierung dieser Anwendung im Azure-Portal benötigt.
1. Fügen Sie die folgenden NuGet-Pakete für die ASP.NET-OWIN-Middleware hinzu:
   - Microsoft.Owin.Security.ActiveDirectory
   - Microsoft.Owin.Security.Cookies
   - Microsoft.Owin.Host.SystemWeb
   - Microsoft.IdentityModel.Protocol.Extensions
   - Microsoft.Owin.Security.OpenIdConnect
   - Microsoft.Identity.Client
1. Führen Sie im Ordner **App_Start** die folgenden Schritte aus:
   1. Erstellen Sie eine Klasse mit dem Namen **Startup.Auth.cs**.
   1. Entfernen Sie **.App_Start** aus dem Namespacenamen.
   1. Ersetzen Sie den Code für die Klasse **Startup** durch den Code aus der gleichen Datei der Beispiel-App.
   Achten Sie darauf, die gesamte Klassendefinition zu verwenden. Die Definition ändert sich von **public class Startup** in **public partial class Startup**.
1. Beheben Sie die fehlenden Verweise in **Startup.Auth.cs** durch Hinzufügen von **using**-Anweisungen (gemäß den Empfehlungen von Visual Studio IntelliSense).
1. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Hinzufügen** > **Klasse** aus.
1. Geben Sie **OWIN** in das Suchfeld ein. **OWIN-Startup-Klasse** wird als Option angezeigt. Wählen Sie diese Option aus, und nennen Sie die Klasse **Startup.cs**.
1. Ersetzen Sie in **Startup.cs** den Code für die Klasse **Startup** durch den Code aus der gleichen Datei der Beispiel-App. Auch hier ändert sich die Definition von **public class Startup** in **public partial class Startup**.
1. Fügen Sie im Ordner **Models** eine neue Klasse namens **MsGraphUser.cs** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **MVC 5-Controller - leer** namens **AccountController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **MVC 5-Controller - leer** namens **UserController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie eine neue Instanz von **Web API 2-Controller – Leer** namens **SyncController** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Fügen Sie für die Benutzeroberfläche im Ordner **Views\Account** drei **leere Ansichten (ohne Modell)** namens **GrantPermissions**, **Index** und **UserMismatch** hinzu. Fügen Sie außerdem im Ordner **Views\User** eine Instanz namens **Index** hinzu. Ersetzen Sie die Implementierung durch den Inhalt der gleichnamigen Datei aus dem Beispiel.
1. Aktualisieren Sie **Shared\_Layout.cshtml** und **Home\Index.cshtml**, um die verschiedenen Ansichten ordnungsgemäß miteinander zu verknüpfen.

## <a name="deploy-the-sample-to-azure"></a>Bereitstellen des Beispiels in Azure

Dieses Projekt enthält Web-App- und Web-API-Projekte. Führen Sie jeweils die folgenden Schritte aus, um sie für Azure-Websites bereitzustellen:

1. Erstellen Sie eine Azure-Website.
1. Veröffentlichen Sie die Web-App und die Web-APIs für die Website.
1. Aktualisieren Sie die Clients, um die Website aufzurufen (anstelle von IIS Express).

### <a name="create-and-publish-dotnet-web-daemon-v2-to-an-azure-website"></a>Erstellen und Veröffentlichen von „dotnet-web-daemon-v2“ für eine Azure-Website

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie links oben **Ressource erstellen** aus.
1. Wählen Sie **Web** > **Web-App** aus, und benennen Sie Ihre Website. Nennen Sie sie beispielsweise **dotnet-web-daemon-v2-contoso.azurewebsites.net**.
1. Wählen Sie die Informationen für **Abonnement** und **Ressourcengruppe** sowie für **App Service-Plan und Standort** aus. Legen Sie **Betriebssystem** auf **Windows** und **Veröffentlichen** auf **Code** fest.
1. Wählen Sie **Erstellen** aus, und warten Sie, bis der App-Dienst erstellt wurde.
1. Wählen Sie nach Erhalt der Benachrichtigung **Bereitstellung erfolgreich** die Option **Zu Ressource wechseln** aus, um zum neu erstellten App-Dienst zu navigieren.
1. Suchen Sie die erstellte Website auf dem **Dashboard**, und wählen Sie sie aus, um den Bildschirm **Übersicht** für den App-Dienst zu öffnen.
1. Laden Sie auf der Registerkarte **Übersicht** des App-Diensts das Veröffentlichungsprofil herunter, indem Sie den Link **Veröffentlichungsprofil abrufen** auswählen, und speichern Sie das Profil. Es können auch andere Bereitstellungsmechanismen wie etwa die Bereitstellung per Quellcodeverwaltung verwendet werden.
1. Wechseln Sie zu Visual Studio, und führen Sie die folgenden Schritte aus:
   1. Navigieren Sie zum Projekt **dotnet-web-daemon-v2**.
   1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
   1. Wählen Sie auf der unteren Leiste die Option **Profil importieren** aus, und importieren Sie das zuvor heruntergeladene Veröffentlichungsprofil.
1. Wählen Sie **Konfigurieren**aus.
1. Aktualisieren Sie auf der Registerkarte **Verbindung** die Ziel-URL, sodass sie HTTPS verwendet. Verwenden Sie z. B. `https://dotnet-web-daemon-v2-contoso.azurewebsites.net`. Wählen Sie **Weiter** aus.
1. Vergewissern Sie sich auf der Registerkarte **Einstellungen**, dass die Option **Organisationsauthentifizierung aktivieren** deaktiviert ist.
1. Wählen Sie **Speichern** aus. Wählen Sie im Hauptbildschirm **Veröffentlichen** aus.

Visual Studio veröffentlicht das Projekt und öffnet die Projekt-URL automatisch in einem Browser. Wenn die Standardwebseite des Projekts angezeigt wird, war die Veröffentlichung erfolgreich.

### <a name="update-the-azure-ad-tenant-application-registration-for-dotnet-web-daemon-v2"></a>Aktualisieren der Azure AD-Mandantenanwendungsregistrierung für „dotnet-web-daemon-v2“

1. Wechseln Sie zurück zum [Azure-Portal](https://portal.azure.com).
1. Wählen Sie im linken Bereich den Dienst **Azure Active Directory** und anschließend **App-Registrierungen** aus.
1. Wählen Sie die Anwendung **dotnet-web-daemon-v2** aus.
1. Aktualisieren Sie auf der Seite **Authentifizierung** für Ihre Anwendung die Felder vom Typ **Abmelde-URL** mit der Adresse Ihres Diensts. Verwenden Sie beispielsweise [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Aktualisieren Sie im Menü **Branding** die **URL der Startseite** auf die Adresse Ihres Diensts. Verwenden Sie beispielsweise [https://dotnet-web-daemon-v2-contoso.azurewebsites.net](https://dotnet-web-daemon-v2-contoso.azurewebsites.net).
1. Speichern Sie die Konfiguration.
1. Fügen Sie die gleiche URL der Werteliste unter **Authentifizierung** > **Umleitungs-URIs** hinzu. Falls Sie über mehrere Umleitungs-URLs verfügen, muss für jede ein neuer Eintrag mit dem URI des App-Diensts vorhanden sein.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen
Löschen Sie das im Schritt [Registrieren Ihrer Anwendung](#register-your-application) erstellte App-Objekt, wenn Sie es nicht mehr benötigen.  Befolgen Sie zum Entfernen der Anwendung die Anweisungen unter [Entfernen einer Anwendung, die von Ihnen oder Ihrer Organisation erstellt wurde](quickstart-remove-app.md#remove-an-application-authored-by-you-or-your-organization).

## <a name="get-help"></a>Hier erhalten Sie Hilfe

Verwenden Sie [Stack Overflow](http://stackoverflow.com/questions/tagged/msal), um Unterstützung von der Community zu erhalten.
Stellen Sie Ihre Fragen zuerst auf Stack Overflow, und durchsuchen Sie die vorhandenen Probleme, um zu prüfen, ob vielleicht schon jemand anders die gleiche Frage hatte.
Markieren Sie Ihre Fragen und Kommentare mit „adal“, „msal“ und „dotnet“.

Sollten Sie einen Fehler im Beispiel finden, erstellen Sie ein Problem unter [GitHub-Probleme](https://github.com/Azure-Samples/ms-identity-aspnet-daemon-webapp/issues).

Sollten Sie einen Fehler in MSAL.NET finden, erstellen Sie ein Problem unter [GitHub-Probleme für MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues).

Wenn Sie eine Empfehlung abgeben möchten, besuchen Sie die [Benutzerfeedback-Seite](https://feedback.azure.com/forums/169401-azure-active-directory).

## <a name="next-steps"></a>Nächste Schritte
Informieren Sie sich ausführlicher über die verschiedenen [Authentifizierungsflows und Anwendungsszenarien](authentication-flows-app-scenarios.md), die von Microsoft Identity Platform unterstützt werden.

Weitere Informationen finden Sie in der folgenden Konzeptdokumentation:

- [Mandanten in Azure Active Directory](single-and-multi-tenant-apps.md)
- [Grundlegendes zur Zustimmung für Azure AD-Anwendungen](application-consent-experience.md)
- [Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](howto-convert-app-to-be-multi-tenant.md)
- [Grundlegendes zur Benutzer- und Administratoreinwilligung](howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent)
- [Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory](app-objects-and-service-principals.md)
- [Schnellstart: Registrieren einer Anwendung bei Microsoft Identity Platform](quickstart-register-app.md)
- [Schnellstart: Konfigurieren einer Clientanwendung für den Zugriff auf Web-APIs](quickstart-configure-app-access-web-apis.md)
- [Öffentliche und vertrauliche Clientanwendungen](msal-client-applications.md)

Eine Anleitung für eine einfachere mehrinstanzenfähige Daemon-Konsolenanwendung finden Sie unter [Schnellstart: Abrufen eines Tokens und Aufrufen der Microsoft Graph-API über die Identität einer Konsolen-App](quickstart-v2-netcore-daemon.md).
