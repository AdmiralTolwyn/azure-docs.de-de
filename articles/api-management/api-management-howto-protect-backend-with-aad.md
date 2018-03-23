---
title: Schützen eines Web-API-Back-Ends mit Azure Active Directory und API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein Web-API-Back-End mit Azure Active Directory und API Management schützen.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: b7fc48412799aea0c4bba971102b4912dbb18e05
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="how-to-protect-a-web-api-backend-with-azure-active-directory-and-api-management"></a>So schützen Sie ein Web-API-Back-End mit Azure Active Directory und API Management

In diesem Thema wird erörtert, wie Sie ein Web-API-Back-End erstellen und es mithilfe des OAuth 2.0-Protokolls mit Azure Active Directory und API Management schützen.  

## <a name="create-an-azure-ad-directory"></a>Erstellen eines Azure AD-Verzeichnisses
Zum Schützen Ihres Web-API-Back-Ends mit Azure Active Directory benötigen Sie einen AAD-Mandanten. Zum Erstellen eines AAD-Mandanten melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an und klicken auf **Neu**->**App Services**->**Active Directory**->**Verzeichnis**->**Benutzerdefiniert erstellen**. 

![Azure Active Directory][api-management-create-aad-menu]

In diesem Beispiel wird ein Verzeichnis namens **APIMDemo** mit einer Standarddomäne namens **DemoAPIM.onmicrosoft.com** erstellt. 

![Azure Active Directory][api-management-create-aad]

## <a name="create-a-web-api-service-secured-by-azure-active-directory"></a>Erstellen eines durch Azure Active Directory gesicherten Web-API-Diensts
In diesem Schritt wird mithilfe von Visual Studio 2013 ein Web-API-Back-End erstellt. Zum Erstellen des Web-API-Back-End-Projekts in Visual Studio klicken Sie auf **Datei**->**Neu**->**Projekt** und wählen in der Liste mit den Vorlagen vom Typ **Web** dann die Option **ASP.NET-Webanwendung**. 

![Visual Studio][api-management-new-web-app]

Klicken Sie in der Liste **Vorlage auswählen** auf **Web-API**, um ein Web-API-Projekt zu erstellen. Zum Konfigurieren der Azure Directory-Authentifizierung klicken Sie auf **Authentifizierung ändern**.

![Neues Projekt][api-management-new-project]

Klicken Sie auf **Organisationskonten**, und geben Sie die **Domäne** Ihres AAD-Mandanten an. In diesem Beispiel lautet die Domäne **DemoAPIM.onmicrosoft.com**. Die Domäne Ihres Verzeichnisses kann auf der Registerkarte **Domänen** Ihres Verzeichnisses abgerufen werden.

![Domänen][api-management-aad-domains]

Konfigurieren Sie die gewünschten Einstellungen im Dialogfeld **Authentifizierung ändern**, und klicken Sie auf **OK**.

![Authentifizierung ändern][api-management-change-authentication]

Beim Klicken auf **OK** versucht Visual Studio, Ihre Anwendung bei Ihrem Microsoft Azure AD-Verzeichnis zu registrieren, und Sie werden möglicherweise von Visual Studio zur Anmeldung aufgefordert. Melden Sie sich mit einem Administratorkonto für Ihr Verzeichnis an.

![Anmelden bei Visual Studio][api-management-sign-in-vidual-studio]

Zum Konfigurieren dieses Projekts als Azure-Web-API aktivieren Sie das Kontrollkästchen für das **Hosten in der Cloud** und klicken dann auf **OK**.

![Neues Projekt][api-management-new-project-cloud]

Sie werden möglicherweise zur Anmeldung bei Azure aufgefordert und können anschließend die Web-App konfigurieren.

![Konfigurieren][api-management-configure-web-app]

In diesem Beispiel wird ein neuer **App Service-Plan** mit dem Namen **APIMAADDemo** angegeben.

Klicken Sie auf **OK**, um die Web-App zu konfigurieren und das Projekt zu erstellen.

## <a name="add-the-code-to-the-web-api-project"></a>Hinzufügen des Codes zum Web-API-Projekt

Die Web-API in diesem Beispiel implementiert mithilfe eines Modells und eines Controllers einen grundlegenden Rechnerdienst. Klicken Sie zum Hinzufügen des Modells für den Dienst im **Projektmappen-Explorer** mit der rechten Maustaste auf **Modelle**, und wählen Sie dann **Hinzufügen** > **Klasse**. Geben Sie der Klasse den Namen `CalcInput` , und klicken Sie auf **Hinzufügen**.

Fügen Sie am Anfang der `CalcInput.cs`-Datei die folgende `using`-Anweisung hinzu.

```csharp
using Newtonsoft.Json;
```

Ersetzen Sie die generierte Klasse durch den folgenden Code:

```csharp
public class CalcInput
{
    [JsonProperty(PropertyName = "a")]
    public int a;

    [JsonProperty(PropertyName = "b")]
    public int b;
}
```

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Controller**, und wählen Sie **Hinzufügen**->**Controller**. Wählen Sie **Web API 2-Controller – Leer**, und klicken Sie auf **Hinzufügen**. Geben Sie als Controllername **CalcController** ein, und klicken Sie auf **Hinzufügen**.

![Controller hinzufügen][api-management-add-controller]

Fügen Sie am Anfang der `CalcController.cs`-Datei die folgende `using`-Anweisung hinzu.

```csharp
using System.IO;
using System.Web;
using APIMAADDemo.Models;
```

Ersetzen Sie die generierte Controllerklasse durch den folgenden Code: Dieser Code implementiert die `Add`-, `Subtract`-, `Multiply`- und `Divide`-Prozesse für die Basic Calculator-API.

```csharp
[Authorize]
public class CalcController : ApiController
{
    [Route("api/add")]
    [HttpGet]
    public HttpResponseMessage GetSum([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a + b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/sub")]
    [HttpGet]
    public HttpResponseMessage GetDiff([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a - b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/mul")]
    [HttpGet]
    public HttpResponseMessage GetProduct([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a * b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }

    [Route("api/div")]
    [HttpGet]
    public HttpResponseMessage GetDiv([FromUri]int a, [FromUri]int b)
    {
        string xml = string.Format("<result><value>{0}</value><broughtToYouBy>Azure API Management - http://azure.microsoft.com/apim/ </broughtToYouBy></result>", a / b);
        HttpResponseMessage response = Request.CreateResponse();
        response.Content = new StringContent(xml, System.Text.Encoding.UTF8, "application/xml");
        return response;
    }
}
```

Drücken Sie **F6**, um die Lösung zu erstellen und zu überprüfen.

## <a name="publish-the-project-to-azure"></a>Veröffentlichen des Projekts in Azure

Zum Veröffentlichen des Projekts in Azure klicken Sie mit der rechten Maustaste in Visual Studio auf das Projekt **APIMAADDemo** und wählen **Veröffentlichen**. Behalten Sie die Standardeinstellungen im Dialogfeld **Web veröffentlichen** bei, und klicken Sie auf **Veröffentlichen**.

![Veröffentlichen im Web][api-management-web-publish]

## <a name="grant-permissions-to-the-azure-ad-backend-service-application"></a>Gewähren von Berechtigungen für die Azure AD-Back-End-Dienstanwendung
In Ihrem Azure AD-Verzeichnis wird im Rahmen des Konfigurations- und Veröffentlichungsprozesses Ihres Web-API-Projekts eine neue Anwendung für den Back-End-Dienst erstellt.

![Anwendung][api-management-aad-backend-app]

Klicken Sie auf den Namen der Anwendung, um die erforderlichen Berechtigungen zu konfigurieren. Navigieren Sie zur Registerkarte **Konfigurieren**, und blättern Sie nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**. Klicken Sie neben **Windows** **Azure Active Directory** auf die Dropdownliste **Anwendungsberechtigungen**, aktivieren Sie das Kontrollkästchen für **Verzeichnisdaten lesen**, und klicken Sie auf **Speichern**.

![Hinzufügen von Berechtigungen][api-management-aad-add-permissions]

> [!NOTE]
> Wenn **Windows** **Azure Active Directory** unter den Berechtigungen für andere Programme nicht aufgeführt ist, klicken Sie auf **Anwendung hinzufügen** und fügen die Option aus der Liste hinzu.
> 
> 

Notieren Sie die **App-ID-URI** für die Verwendung in einem späteren Schritt, wenn eine Azure AD-Anwendung für das API Management-Entwicklerportal konfiguriert wird.

![App-ID-URI][api-management-aad-sso-uri]

## <a name="import-the-web-api-into-api-management"></a>Importieren der Web-API in API Management
APIs werden im API-Herausgeberportal konfiguriert, das über das Azure-Portal erreichbar ist. Um dorthin zu gelangen, klicken Sie auf der Symbolleiste Ihres API Management-Diensts auf **Herausgeberportal**. Falls Sie noch keine API Management-Dienstinstanz erstellt haben, helfen Ihnen die Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance] des Tutorials [Verwalten Ihrer ersten API][Manage your first API] weiter.

![Herausgeberportal][api-management-management-console]

Vorgänge können [APIs manuell hinzugefügt](api-management-howto-add-operations.md)oder importiert werden.

Erstellen Sie eine Datei mit dem Namen `calcapi.json` mit folgenden Inhalt, und speichern Sie sie auf Ihrem Computer. Stellen Sie sicher, dass das `host` -Attribut auf Ihr Web-API-Back-End verweist. In diesem Beispiel wird `"host": "apimaaddemo.azurewebsites.net"` verwendet.

```json
{
  "swagger": "2.0",
  "info": {
    "title": "Calculator",
    "description": "Arithmetics over HTTP!",
    "version": "1.0"
  },
  "host": "apimaaddemo.azurewebsites.net",
  "basePath": "/api",
  "schemes": [
    "http"
  ],
  "paths": {
    "/add?a={a}&b={b}": {
      "get": {
        "description": "Responds with a sum of two numbers.",
        "operationId": "Add two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>51</code>.",
            "required": true,
            "type": "string",
            "default": "51",
            "enum": [
              "51"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>49</code>.",
            "required": true,
            "type": "string",
            "default": "49",
            "enum": [
              "49"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/sub?a={a}&b={b}": {
      "get": {
        "description": "Responds with a difference between two numbers.",
        "operationId": "Subtract two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>50</code>.",
            "required": true,
            "type": "string",
            "default": "50",
            "enum": [
              "50"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/div?a={a}&b={b}": {
      "get": {
        "description": "Responds with a quotient of two numbers.",
        "operationId": "Divide two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>100</code>.",
            "required": true,
            "type": "string",
            "default": "100",
            "enum": [
              "100"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          }
        ],
        "responses": { }
      }
    },
    "/mul?a={a}&b={b}": {
      "get": {
        "description": "Responds with a product of two numbers.",
        "operationId": "Multiply two integers",
        "parameters": [
          {
            "name": "a",
            "in": "query",
            "description": "First operand. Default value is <code>20</code>.",
            "required": true,
            "type": "string",
            "default": "20",
            "enum": [
              "20"
            ]
          },
          {
            "name": "b",
            "in": "query",
            "description": "Second operand. Default value is <code>5</code>.",
            "required": true,
            "type": "string",
            "default": "5",
            "enum": [
              "5"
            ]
          }
        ],
        "responses": { }
      }
    }
  }
}
```

Klicken Sie zum Importieren der Rechner-API im Menü **API Management** auf der linken Seite auf **APIs**, und klicken Sie anschließend auf **API importieren**.

![Schaltfläche "API importieren"][api-management-import-api]

Führen Sie die folgenden Schritte aus, um die Rechner-API zu konfigurieren.

1. Klicken Sie auf **Aus Datei**, navigieren Sie zur gespeicherten Datei `calculator.json`, und klicken Sie auf das Optionsfeld **Swagger**.
2. Geben Sie in das Textfeld **URL-Suffix für Web-API** den Wert **calc** ein.
3. Klicken Sie auf das Feld **Produkte (optional)**, und wählen Sie **Starter**.
4. Klicken Sie auf **Speichern**, um die API zu importieren.

![Neue API hinzufügen][api-management-import-new-api]

Sobald die API importiert wurde, wird die Zusammenfassungsseite für die API im Herausgeberportal angezeigt.

## <a name="call-the-api-unsuccessfully-from-the-developer-portal"></a>Nicht erfolgreiches Aufrufen der API aus dem Entwicklerportal
An diesem Punkt wurde die API in API Management importiert, kann jedoch noch nicht erfolgreich aus dem Entwicklerportal aufgerufen werden, da der Back-End-Dienst durch Azure AD-Authentifizierung geschützt ist. 

Klicken Sie im Herausgeberportal oben rechts auf **Entwicklerportal** .

![Entwicklerportal][api-management-developer-portal-menu]

Klicken Sie auf **APIs** und dann auf die **Rechner**-API.

![Entwicklerportal][api-management-dev-portal-apis]

Klicken auf **Ausprobieren**.

![Ausprobieren][api-management-dev-portal-try-it]

Klicken Sie auf **Senden**, und notieren Sie den Antwortstatus **401 Nicht autorisiert**.

![Send][api-management-dev-portal-send-401]

Die Anforderung ist nicht autorisiert, da die Back-End-API durch Azure Active Directory geschützt wird. Vor dem erfolgreichen Aufrufen der API muss das Entwicklerportal zum Autorisieren von Entwicklern mit OAuth 2.0 konfiguriert werden. Dieser Prozess wird in den folgenden Abschnitten beschrieben.

## <a name="register-the-developer-portal-as-an-aad-application"></a>Registrieren des Entwicklerportals als AAD-Anwendung
Der erste Schritt bei der Konfiguration des Entwicklerportals zum Autorisieren von Entwicklern mit OAuth 2.0 ist das Registrieren des Entwicklerportals als AAD Anwendung. 

Navigieren Sie zum Azure AD-Mandanten. Wählen Sie in diesem Beispiel **APIMDemo** aus, und navigieren Sie zur Registerkarte **Anwendungen**.

![Neue Anwendung][api-management-aad-new-application-devportal]

Klicken Sie auf die Schaltfläche **Hinzufügen**, um eine neue Azure Active Directory-Anwendung zu erstellen, und wählen Sie **Von meiner Organisation entwickelte Anwendung hinzufügen**.

![Neue Anwendung][api-management-new-aad-application-menu]

Wählen Sie **Web-Anwendung und/oder Web-API-**, geben Sie einen Namen ein, und klicken Sie auf den Vorwärtspfeil. In diesem Beispiel wird **APIMDeveloperPortal** verwendet.

![Neue Anwendung][api-management-aad-new-application-devportal-1]

Geben Sie als **Anmelde-URL** die URL Ihres API Management-Diensts ein, und fügen Sie `/signin` an. In diesem Beispiel wird `https://contoso5.portal.azure-api.net/signin` verwendet.

Geben Sie als **App-ID-URL** die URL Ihres API Management-Diensts ein, und fügen Sie einige eindeutige Zeichen an. Dabei kann es sich um beliebige Zeichen handeln. In diesem Beispiel wird `https://contoso5.portal.azure-api.net/dp` verwendet. Wenn die gewünschten **App-Eigenschaften** konfiguriert sind, klicken Sie auf das Häkchen, um die Anwendung zu erstellen.

![Neue Anwendung][api-management-aad-new-application-devportal-2]

## <a name="configure-an-api-management-oauth-20-authorization-server"></a>Konfigurieren eines OAuth 2.0-Autorisierungsservers für API Management
Im nächsten Schritt wird ein OAuth 2.0-Autorisierungsserver in API Management konfiguriert. 

Klicken Sie im Menü „API Management“ auf der linken Seite auf **Sicherheit**, und klicken Sie dann auf **OAuth 2.0** und **Autorisierungsserver hinzufügen**.

![Autorisierungsserver hinzufügen][api-management-add-authorization-server]

Geben Sie in die Felder **Name** und **Beschreibung** einen Namen bzw. eine optionale Beschreibung ein. Anhand dieser Felder wird der OAuth 2.0-Autorisierungsserver innerhalb der Instanz des API Management-Diensts identifiziert. In diesem Beispiel wird **Autorisierungsserverdemo** verwendet. Wenn Sie später einen OAuth 2.0-Server für die Authentifizierung für eine API angeben, wählen Sie diesen Namen.

Geben Sie als **Seiten-URL für Clientregistrierung** einen Platzhalterwert ein, z.B. `http://localhost`.  **Seiten-URL für Clientregistrierung** verweist auf die Seite, die Benutzer zum Erstellen und Konfigurieren eigener Konten für OAuth 2.0-Anbieter nutzen können, die eine Benutzerverwaltung für Konten unterstützen. In diesem Beispiel werden von Benutzern keine eigenen Konten erstellt und konfiguriert, daher wird ein Platzhalter verwendet.

![Autorisierungsserver hinzufügen][api-management-add-authorization-server-1]

Geben Sie anschließend die **Autorisierungsendpunkt-URL** und die **Token-Endpunkt-URL** ein.

![Autorisierungsserver][api-management-add-authorization-server-1a]

Diese Werte können auf der Seite **App-Endpunkte** der AAD-Anwendung abgerufen werden, die Sie für das Entwicklerportal erstellt haben. Navigieren Sie zum Zugriff auf die Endpunkte zur Registerkarte **Konfigurieren** für die AAD-Anwendung, und klicken Sie auf **Endpunkte anzeigen**.

![Anwendung][api-management-aad-devportal-application]

![Endpunkte anzeigen][api-management-aad-view-endpoints]

Kopieren Sie den **OAUTH 2.0-Autorisierungsendpunkt**, und fügen Sie ihn in das Textfeld **Autorisierungsendpunkt-URL** ein.

![Autorisierungsserver hinzufügen][api-management-add-authorization-server-2]

Kopieren Sie den **OAUTH 2.0-Token-Endpunkt**, und fügen Sie ihn in das Textfeld **Token-Endpunkt-URL** ein.

![Autorisierungsserver hinzufügen][api-management-add-authorization-server-2a]

Neben dem Einfügen in den Token-Endpunkt fügen Sie einen zusätzlichen Textparameter mit dem Namen **Ressource** hinzu und geben als Wert den **App-ID-URI** aus der AAD-Anwendung für den Back-End-Dienst an, der bei der Veröffentlichung des Visual Studio-Projekts erstellt wurde.

![App-ID-URI][api-management-aad-sso-uri]

Geben Sie anschließend Clientanmeldeinformationen an. Dabei handelt es sich um die Anmeldeinformationen für die Ressource, auf die Sie zugreifen möchten, in diesem Fall das Entwicklerportal.

![Clientanmeldeinformationen][api-management-client-credentials]

Zum Abrufen der **Client-ID** navigieren Sie zur Registerkarte **Konfigurieren** der AAD-Anwendung für das Entwicklerportal und kopieren die **Client-ID**.

Zum Abrufen des **Clientgeheimnisses** klicken Sie im Abschnitt **Schlüssel** auf die Dropdownliste **Dauer auswählen** und geben ein Intervall an. In diesem Beispiel wird 1 Jahr verwendet.

![Client-ID][api-management-aad-client-id]

Klicken Sie auf **Speichern**, um die Konfiguration zu speichern und den Schlüssel anzuzeigen. 

> [!IMPORTANT]
> Notieren Sie sich diesen Schlüssel. Nachdem Sie das Fenster für die Azure Active Directory-Konfiguration geschlossen haben, ist eine Anzeige des Schlüssels nicht mehr möglich.
> 
> 

Kopieren Sie den Schlüssel in die Zwischenablage, wechseln Sie wieder zum Herausgeberportal, fügen Sie den Schlüssel in das Textfeld **Clientgeheimnis** ein, und klicken Sie auf **Speichern**.

![Autorisierungsserver hinzufügen][api-management-add-authorization-server-3]

Unmittelbar nach den Clientanmeldeinformationen folgt eine Autorisierungscodeberechtigung. Kopieren Sie diesen Autorisierungscode, und wechseln Sie wieder zur Ihrer Anwendungskonfigurationsseite im Azure AD-Entwicklerportal, fügen Sie die Autorisierungsberechtigung in das Feld **Antwort-URL** ein, und klicken Sie erneut auf **Speichern**.

![Antwort-URL][api-management-aad-reply-url]

Im nächsten Schritt konfigurieren Sie die Berechtigungen für die AAD-Anwendung des Entwicklerportals. Klicken Sie auf **Anwendungsberechtigungen**, und aktivieren Sie das Kontrollkästchen **Verzeichnisdaten lesen**. Klicken Sie auf **Speichern**, um diese Änderung zu speichern, und klicken Sie dann auf **Anwendung hinzufügen**.

![Hinzufügen von Berechtigungen][api-management-add-devportal-permissions]

Klicken Sie auf das Suchsymbol, geben Sie im Feld „Beginnt mit“ die Zeichenfolge **APIM** ein, wählen Sie **APIMAADDemo**, und klicken Sie zum Speichern auf das Häkchen.

![Hinzufügen von Berechtigungen][api-management-aad-add-app-permissions]

Klicken Sie auf **Delegierte Berechtigungen** für **APIMAADDemo**, aktivieren Sie das Kontrollkästchen für **Zugriff auf APIMAADDemo**, und klicken Sie auf **Speichern**. Dies ermöglicht der Entwicklerportalanwendung den Zugriff auf den Back-End-Dienst.

![Hinzufügen von Berechtigungen][api-management-aad-add-delegated-permissions]

## <a name="enable-oauth-20-user-authorization-for-the-calculator-api"></a>Aktivieren der OAuth 2.0-Benutzerautorisierung für die Rechner-API
Da der OAuth 2.0-Server jetzt konfiguriert ist, können Sie ihn in den Sicherheitseinstellungen für Ihre API angeben. 

Klicken Sie im linken Menü auf **APIs** und anschließend auf **Rechner,** um die Einstellungen anzuzeigen und zu konfigurieren.

![Rechner-API][api-management-calc-api]

Navigieren Sie zur Registerkarte **Sicherheit**, aktivieren Sie das Kontrollkästchen **OAuth 2.0**, wählen Sie in der Dropdownliste **Autorisierungsserver** den gewünschten Autorisierungsserver aus, und klicken Sie auf **Speichern**.

![Rechner-API][api-management-enable-aad-calculator]

## <a name="successfully-call-the-calculator-api-from-the-developer-portal"></a>Erfolgreiches Aufrufen der Rechner-API aus dem Entwicklerportal
Da die OAuth 2.0-Autorisierung jetzt in der API konfiguriert ist, können die zugehörigen Vorgänge erfolgreich aus dem Entwicklercenter aufgerufen werden. 

Navigieren Sie zurück zum Vorgang **Zwei ganze Zahlen addieren** des Rechnerdiensts im Entwicklerportal, und klicken Sie auf **Ausprobieren**. Beachten Sie das neue Element im Abschnitt **Autorisierung** , das dem Autorisierungsserver entspricht, den Sie gerade hinzugefügt haben.

![Rechner-API][api-management-calc-authorization-server]

Wählen Sie in der Dropdownliste „Autorisierung“ die Option **Autorisierungscode** , und geben Sie die Anmeldeinformationen des zu verwendenden Kontos ein. Wenn Sie bereits mit dem Konto angemeldet sind, werden Sie möglicherweise nicht dazu aufgefordert.

![Rechner-API][api-management-devportal-authorization-code]

Klicken Sie auf **Senden**, und notieren Sie den **Antwortstatus** von **200 OK** sowie die Ergebnisse des Vorgangs im Antwortinhalt.

![Rechner-API][api-management-devportal-response]

## <a name="configure-a-desktop-application-to-call-the-api"></a>Konfigurieren einer Desktopanwendung für das Aufrufen der API

Konfigurieren Sie eine einfache Desktopanwendung zum Aufrufen der API. Im ersten Schritt wird die Desktopanwendung in Azure AD registriert und erhält Zugriff auf das Verzeichnis und den Back-End-Dienst. 

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Konfigurieren einer JWT-Überprüfungsrichtlinie zur Vorautorisierung von Anforderungen

Verwenden Sie die Richtlinie [JWT überprüfen](api-management-access-restriction-policies.md#ValidateJWT), um Anforderungen vorab zu autorisieren, indem die Zugriffstoken für jede eingehende Anforderung überprüft werden. Wenn die Anforderung nicht von der Richtlinie „JWT überprüfen“ überprüft wird, wird sie von API Management blockiert und nicht an das Back-End weitergegeben.

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/DemoAPIM.onmicrosoft.com/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>https://DemoAPIM.NOTonmicrosoft.com/APIMAADDemo</value>
        </claim>
    </required-claims>
</validate-jwt>
```

Weitere Informationen finden Sie in der [Cloud Cover-Episode 177 zu weiteren API Management-Features](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). Spulen Sie bis 13:50 vor. Ab 18:50 finden Sie eine Demonstration des Aufrufs eines Vorgangs über das Entwicklerportal, sowohl mit dem als auch ohne das erforderliche Autorisierungstoken.

## <a name="next-steps"></a>Nächste Schritte
* Hier finden Sie weitere [Videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) zu API Management.
* Weitere Methoden zum Sichern Ihres Back-End-Diensts finden Sie unter [Gegenseitige Zertifikatauthentifizierung](api-management-howto-mutual-certificates.md).

[api-management-management-console]: ./media/api-management-howto-protect-backend-with-aad/api-management-management-console.png

[api-management-import-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-api.png
[api-management-import-new-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-import-new-api.png
[api-management-create-aad-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad-menu.png
[api-management-create-aad]: ./media/api-management-howto-protect-backend-with-aad/api-management-create-aad.png
[api-management-new-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-web-app.png
[api-management-new-project]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project.png
[api-management-new-project-cloud]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-project-cloud.png
[api-management-change-authentication]: ./media/api-management-howto-protect-backend-with-aad/api-management-change-authentication.png
[api-management-sign-in-vidual-studio]: ./media/api-management-howto-protect-backend-with-aad/api-management-sign-in-vidual-studio.png
[api-management-configure-web-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-configure-web-app.png
[api-management-aad-domains]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-domains.png
[api-management-add-controller]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-controller.png
[api-management-web-publish]: ./media/api-management-howto-protect-backend-with-aad/api-management-web-publish.png
[api-management-aad-backend-app]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-backend-app.png
[api-management-aad-add-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-permissions.png
[api-management-developer-portal-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-developer-portal-menu.png
[api-management-dev-portal-apis]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-apis.png
[api-management-dev-portal-try-it]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-try-it.png
[api-management-dev-portal-send-401]: ./media/api-management-howto-protect-backend-with-aad/api-management-dev-portal-send-401.png
[api-management-aad-new-application-devportal]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal.png
[api-management-aad-new-application-devportal-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-1.png
[api-management-aad-new-application-devportal-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-new-application-devportal-2.png
[api-management-aad-devportal-application]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-devportal-application.png
[api-management-add-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server.png
[api-management-aad-sso-uri]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-sso-uri.png
[api-management-aad-view-endpoints]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-view-endpoints.png
[api-management-aad-client-id]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-client-id.png
[api-management-add-authorization-server-1]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1.png
[api-management-add-authorization-server-2]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2.png
[api-management-add-authorization-server-2a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-2a.png
[api-management-add-authorization-server-3]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-3.png
[api-management-aad-reply-url]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-reply-url.png
[api-management-add-devportal-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-devportal-permissions.png
[api-management-aad-add-app-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-app-permissions.png
[api-management-aad-add-delegated-permissions]: ./media/api-management-howto-protect-backend-with-aad/api-management-aad-add-delegated-permissions.png
[api-management-calc-api]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-api.png
[api-management-enable-aad-calculator]: ./media/api-management-howto-protect-backend-with-aad/api-management-enable-aad-calculator.png
[api-management-devportal-authorization-code]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-authorization-code.png
[api-management-devportal-response]: ./media/api-management-howto-protect-backend-with-aad/api-management-devportal-response.png
[api-management-calc-authorization-server]: ./media/api-management-howto-protect-backend-with-aad/api-management-calc-authorization-server.png
[api-management-add-authorization-server-1a]: ./media/api-management-howto-protect-backend-with-aad/api-management-add-authorization-server-1a.png
[api-management-client-credentials]: ./media/api-management-howto-protect-backend-with-aad/api-management-client-credentials.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-protect-backend-with-aad/api-management-new-aad-application-menu.png

[Create an API Management service instance]: get-started-create-service-instance.md
[Manage your first API]: import-and-publish.md
