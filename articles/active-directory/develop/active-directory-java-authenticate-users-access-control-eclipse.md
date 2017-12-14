---
title: 'Gewusst wie: Verwenden von Access Control (Java) | Microsoft Docs'
description: Erfahren Sie, wie Sie Access Control mit Java in Azure entwickeln und verwenden.
services: active-directory
documentationcenter: java
author: rmcmurray
manager: mtillman
editor: 
ms.assetid: 247dfd59-0221-4193-97ec-4f3ebe01d3c7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.custom: aaddev
ms.openlocfilehash: b555ef40fae8156d2957643697d6450ef22b215a
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-authenticate-web-users-with-azure-access-control-service-using-eclipse"></a>Authentifizieren von Webbenutzern mit dem Azure Access Control Service über Eclipse
Diese Anleitung zeigt, wie der Azure Access Control Service (ACS) im Azure Toolkit für Eclipse verwendet wird. Weitere Informationen zum ACS finden Sie im Abschnitt [Nächste Schritte](#next_steps) .

> [!NOTE]
> Der Azure Access Control Service-Filter ist eine Technologievorschau für die Community. Als Vorabversion wird diese Software von Microsoft nicht offiziell unterstützt.
> 
> 

## <a name="what-is-acs"></a>Was ist der ACS?
Die meisten Entwickler sind keine Identitätsexperten und im Allgemeinen auch nicht bereit, Zeit mit der Entwicklung von Authentifizierungs- und Autorisierungsmechanismen für ihre Anwendungen und Dienste zu verbringen. Der ACS ist ein Azure-Dienst, der Ihnen einfache Möglichkeiten bietet, Benutzer für den Zugriff auf Ihre Webanwendungen und -dienste zu authentifizieren, ohne eine komplexe Authentifizierungslogik in Ihren Code aufnehmen zu müssen.

Folgende Funktionen stehen im ACS zur Verfügung:

* Integration mit Windows Identity Foundation (WIF).
* Unterstützung beliebter Anbieter von Webidentitäten (IPs), wie beispielsweise Windows Live ID, Google, Yahoo und Facebook.
* Unterstützung von Active Directory Federation Services (AD FS) 2.0.
* Ein auf dem Open Data Protocol (OData) basierender Verwaltungsdienst, der den programmgesteuerten Zugriff auf die ACS-Einstellungen ermöglicht.
* Ein Verwaltungsportal, über das Administratoren auf die ACS-Einstellungen zugreifen können.

Weitere Informationen zu ACS finden Sie unter [Access Control Service 2.0][Access Control Service 2.0].

## <a name="concepts"></a>Konzepte
Der ACS basiert auf den Prinzipien der anspruchsbasierten Identität -- einem konsistenten Ansatz zur Erstellung von Authentifizierungsmechanismen für Anwendungen, die entweder lokal oder in der Cloud ausgeführt werden. Die anspruchsbasierte Identität ist eine von Anwendungen und Diensten häufig genutzte Möglichkeit, sich benötigte Identitätsinformationen über Benutzer in ihren Unternehmen, in anderen Unternehmen und im Internet zu beschaffen.

Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, sollten Sie die folgenden Konzepte verstehen:

**Client** - Im Kontext dieses Leitfadens ein Browser, der auf Ihre Webanwendung zuzugreifen versucht.

**Anwendung der vertrauenden Seite (Relying Party, RP)** – Eine Anwendung der vertrauenden Seite ist eine Website oder ein Dienst, die/der die Authentifizierung an eine externe Stelle auslagert. Im Identitätskontext sagt man, dass die RP der Authentifizierungsstelle vertraut. In dieser Anleitung wird erklärt, wie Sie eine Anwendung so konfigurieren, dass sie dem ACS vertraut.

**Token** – ein Token ist eine Sammlung von Sicherheitsdaten, die im Normalfall bei der erfolgreichen Authentifizierung eines Benutzers ausgegeben werden. Es enthält einen Satz von *Ansprüchen*. Dabei handelt es sich um Attribute des authentifizierten Benutzers. Ein Anspruch kann ein Benutzername, ein Bezeichner für eine Rolle, zu der der Benutzer gehört, das Alter des Benutzers usw. sein. Ein Token ist normalerweise digital signiert. Dies bedeutet, dass es jederzeit zum Benutzer zurückverfolgt werden kann und sein Inhalt fälschungssicher ist. Ein Benutzer erlangt Zugriff auf eine Anwendung der vertrauenden Seite, indem er ein gültiges Token vorweist, das von einer Stelle ausgestellt wurde, der die Anwendung der vertrauenden Seite vertraut.

**Identitätsanbieter (Identity Provider, IP)** – ein IP ist eine Stelle, die Benutzeridentitäten authentifiziert und Sicherheitstokens ausgibt. Der tatsächliche Vorgang der Tokenausgabe wird über einen speziellen Dienst namens Security Token Service (STS) ausgeführt. Typische Beispiele für IPs sind Windows Live ID, Facebook, geschäftliche Benutzer-Repositorys (wie Active Directory) usw.
Wenn zwischen dem ACS und einem IP ein Vertrauensverhältnis konfiguriert worden ist, akzeptiert und validiert das System die von diesem IP vergebenen Tokens. ACS kann mehreren IPs gleichzeitig vertrauen. Dies bedeutet, dass Sie, wenn Ihre Anwendung dem ACS vertraut, Ihre Anwendung sofort allen authentifizierten Benutzern aller IPs zur Verfügung stellen können, denen ACS stellvertretend für Sie vertraut.

**Verbundanbieter (Federation Provider, FP)** – Identitätsanbieter (IPs) kennen die Benutzer, authentifizieren Benutzer anhand ihrer Anmeldeinformationen und geben Ansprüche für Benutzer anhand der ihnen bekannten Informationen aus. Ein Verbundanbieter (FP) ist eine andere Art von Authentifizierungsstelle: Er authentifiziert Benutzer nicht direkt, sondern fungiert als Instanz zur Vermittlung der Authentifizierung zwischen einer RP und einer oder mehreren IP-Adressen. Sowohl IPs als auch FPs geben Sicherheitstokens aus. Daher verwenden Sie beide Security Token Services (STS). Der ACS ist ein FP.

**ACS-Regelmodul** - Die Logik, die eingehende Tokens von vertrauenswürdigen IPs in Tokens transformiert, die von einer RP verwendet werden können, ist in Form einfacher Anspruchstransformationsregeln kodifiziert. Der ACS funktioniert als Regelmodul, das jede Transformationsregel anwendet, die Sie für Ihre RP festgelegt haben.

**ACS-Namespace** - Ein Namespace ist eine Partition auf der obersten Ebene des ACS, die Sie zur Organisation Ihrer Einstellungen verwenden. Ein Namespace enthält eine Liste der IPs, denen Sie vertrauen, die Anwendungen der vertrauenden Seite, die Sie bereitstellen möchten, und die Regeln, die das Regelmodul auf eingehende Tokens anwenden soll. Ein Namespace weist verschiedene Endpunkte auf, die von der Anwendung und dem Entwickler verwendet werden, damit der ACS seine Funktion ausführt.

Die folgende Abbildung zeigt, wie die ACS-Authentifizierung bei einer Webanwendung abläuft:

![ACS-Ablaufdiagramm][acs_flow]

1. Der Client (in diesem Fall ein Browser) fordert von der RP eine Seite an.
2. Da die Anforderung noch nicht authentifiziert ist, verweist die RP den Benutzer an die Stelle, der sie vertraut, also an ACS. Der ACS übermittelt dem Benutzer verschiedene IPs, die für diese RP definiert wurden. Der Benutzer wählt einen passenden IP aus.
3. Der Client geht auf die Authentifizierungsseite des IPs und fordert den Benutzer auf, sich anzumelden.
4. Wenn der Client authentifiziert ist (beispielsweise die Anmeldeinformationen eingegeben wurden), gibt der IP ein Sicherheitstoken aus.
5. Nach der Ausgabe des Sicherheitstokens leitet der IP den Client an den ACS weiter und sendet das vom IP ausgegebene Sicherheitstoken an den ACS.
6. Der ACS validiert das vom IP ausgegebene Token, gibt die in diesem Token enthaltenen Identitätsansprüche in das ACS-Regelmodul ein, berechnet die auszugebenden Identitätsansprüche und gibt ein neues Sicherheitstoken aus, das diese Ansprüche enthält.
7. Der ACS leitet den Client an die RP weiter. Der Client sendet das vom ACS ausgegebene neue Sicherheitstoken an die RP. Die RP validiert die Signatur auf dem vom ACS ausgegebenen Sicherheitstoken, validiert die Ansprüche in diesem Token und gibt die ursprünglich angeforderte Seite zurück.

## <a name="prerequisites"></a>Voraussetzungen
Damit Sie die in dieser Anleitung gestellten Aufgaben ausführen können, brauchen Sie Folgendes:

* Ein Java Developer Kit (JDK), Version 1.6 oder höher.
* Eclipse IDE für Java EE-Entwickler, Indigo oder höher. Dies kann von <http://www.eclipse.org/downloads/> heruntergeladen werden. 
* Eine Distribution eines Java-basierten Webservers oder Anwendungsservers wie Apache Tomcat, GlassFish, JBoss Application Server oder Jetty.
* Ein Azure-Abonnement, das Sie unter <http://www.microsoft.com/windowsazure/offers/> erhalten.
* Das Azure Toolkit für Eclipse, Version von April 2014 oder höher. Weitere Informationen finden Sie unter [Installation des Azure Toolkit für Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690946.aspx).
* Ein X.509-Zertifikat, das von Ihrer Anwendung verwendet werden kann. Dieses Zertifikat wird sowohl als öffentliches Zertifikat (.cer) als auch im Format Personal Information Exchange (.PFX) benötigt. (Optionen zur Erstellung dieses Zertifikats finden Sie weiter unten in diesem Lernprogramm).
* Außerdem sollten Sie mit dem Azure-Serveremulator und den Einsatztechniken vertraut sein, die unter [Creating a Hello World Application for Microsoft Azure in Eclipse (Erstellen einer Hallo-Welt-Anwendung für Azure in Eclipse, in englischer Sprache)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx)diskutiert werden.

## <a name="create-an-acs-namespace"></a>Erstellen eines ACS-Namespace
Um mit der Verwendung des Access Control Service (ACS) in Azure beginnen zu können, müssen Sie einen ACS-Namespace erstellen. Dieser Namespace stellt einen eindeutigen Bereich für die Adressierung von ACS-Ressourcen innerhalb Ihrer Anwendung bereit.

1. Melden Sie sich beim [Azure-Verwaltungsportal][Azure Management Portal] an.
2. Klicken Sie auf **Active Directory**. 
3. Klicken Sie zum Erstellen eines neuen Access Control-Namespace auf **Neu**, **App Services**, **Access Control** und dann auf **Schnellerstellung**. 
4. Geben Sie einen Namen für den Namespace ein. Azure prüft, ob der Name eindeutig ist.
5. Wählen Sie die Region aus, in der der Namespace verwendet wird. Verwenden Sie für die beste Leistung die Region, in der Sie ihre Anwendung einsetzen.
6. Wenn Sie über mehr als ein Abonnement verfügen, wählen Sie das Abonnement aus, das Sie für den ACS-Namespace verwenden möchten.
7. Klicken Sie auf **Erstellen**.

Azure erstellt und aktiviert den Namespace. Warten Sie, bis der Status des neuen Namespace auf **Aktiv** gesetzt wird, bevor Sie fortfahren. 

## <a name="add-identity-providers"></a>Hinzufügen von Identitätsanbietern
In dieser Aufgabe fügen Sie IPs hinzu, die mit Ihrer Anwendung der vertrauenden Seite zur Authentifizierung verwendet werden. Zu Demonstrationszwecken wird in dieser Aufgabe gezeigt, wie Windows Live als IP hinzugefügt wird, aber Sie können jeden IP verwenden, der im ACS-Verwaltungsportal aufgelistet wird.

1. Klicken Sie im [Azure-Verwaltungsportal][Azure Management Portal] auf **Active Directory**, wählen Sie einen Access Control-Namespace aus, und klicken Sie dann auf **Verwalten**. Das ACS-Verwaltungsportal wird geöffnet.
2. Klicken Sie im linken Navigationsbereich des ACS-Verwaltungsportals auf **Identitätsanbieter**.
3. Windows Live ID wird standardmäßig aktiviert und kann nicht gelöscht werden. Für die Zwecke dieses Lernprogramms wird nur Windows Live ID verwendet. Auf diesem Bildschirm können Sie allerdings noch andere IPs hinzufügen, indem Sie auf die Schaltfläche **Hinzufügen** klicken.

Windows Live ID wird nun als IP für Ihren ACS-Namespace aktiviert. Als Nächstes geben Sie Ihre Java-Webanwendung (die später erstellt wird) als RP an.

## <a name="add-a-relying-party-application"></a>Hinzufügen einer Anwendung der vertrauenden Seite
In dieser Aufgabe konfigurieren Sie den ACS zur Erkennung Ihrer Java-Webanwendung als gültige Anwendung der vertrauenden Seite.

1. Klicken Sie im ACS-Verwaltungsportal auf **Relying party applications**.
2. Klicken Sie auf der Seite **Relying Party Applications** (Anwendungen vertrauender Seiten) auf **Hinzufügen**.
3. Führen Sie auf der Seite **Add Relying Party Application** die folgenden Aktionen aus:
   
   1. Geben Sie unter **Name**einen Namen für die RP ein. Geben Sie für die Zwecke dieses Lernprogramms **Azure Web App**ein.
   2. Wählen Sie unter **Modus** die Option **Enter settings manually** (Einstellungen manuell eingeben) aus.
   3. Geben Sie unter **Bereich**den URI ein, für den das vom ACS ausgegebene Sicherheitstoken gilt. Geben Sie für diese Aufgabe **http://localhost:8080/** ein.
      ![RP-Bereich zur Verwendung im Serveremulator][relying_party_realm_emulator]
   4. Geben Sie unter **Rückgabe-URL** die URL ein, an die der ACS das Sicherheitstoken zurückgibt. Geben Sie für diese Aufgabe **http://localhost:8080/MyACSHelloWorld/index.jsp**
      ![Rückgabe-URL der vertrauenden Seite zur Verwendung im Serveremulator][relying_party_return_url_emulator] ein.
   5. Übernehmen Sie in den restlichen Feldern die Standardwerte.
4. Klicken Sie auf **Speichern**.

Sie haben nun erfolgreich Ihre Java-Webanwendung konfiguriert, die im Azure-Serveremulator (unter http://localhost:8080/) ausgeführt wird, um als RP in Ihrem ACS-Namespace zu fungieren. Erstellen Sie als Nächstes die Regeln, die der ACS zur Verarbeitung von Ansprüchen für die RP verwendet.

## <a name="create-rules"></a>Erstellen von Regeln
In dieser Aufgabe definieren Sie die Regeln, die festlegen, wie Ansprüche von den IPs an Ihre RP weitergeleitet werden. Für die Zwecke dieser Anleitung konfigurieren wir den ACS auf einfache Weise so, dass er die eingegebenen Anspruchstypen und -werte ohne Filterung oder Änderung direkt in das ausgegebene Token kopiert.

1. Klicken Sie auf der Hauptseite des ACS-Verwaltungsportals auf **Regelgruppen**.
2. Klicken Sie auf der Seite **Regelgruppen** auf **Default Rule Group for Azure Web App** (Standardregelgruppe für Azure-Web-App).
3. Klicken Sie auf der Seite **Regelgruppe bearbeiten** auf **Generieren**.
4. Stellen Sie auf der Seite **Default Rule Group for Azure Web App** (Standardregelgruppe für Azure-Web-App) sicher, dass „Windows Live ID“ aktiviert ist, und klicken Sie dann auf **Generieren**.    
5. Klicken Sie auf der Seite **Regelgruppe bearbeiten** auf **Speichern**.

## <a name="upload-a-certificate-to-your-acs-namespace"></a>Hochladen eines Zertifikats in Ihren ACS-Namespace
In dieser Aufgabe laden Sie ein .PFX-Zertifikat hoch, das verwendet wird, um von Ihrem ACS-Namespace erstellte Tokenanforderungen zu signieren.

1. Klicken Sie auf der Hauptseite des ACS-Verwaltungsportals auf **Certificates and Keys**.
2. Klicken Sie auf der Seite **Certificates and Keys** (Zertifikate und Schlüssel) über **Tokensignatur** auf **Hinzufügen**.
3. Führen Sie auf der Seite **Add Token-Signing Certificate or Key** folgende Aktionen aus:
   1. Klicken Sie im Abschnitt **Verwendet für** auf **Relying Party Application** (Anwendung der vertrauenden Seite), und wählen Sie **Azure-Web-App** aus (dies wurde zuvor als Name Ihrer Anwendung der vertrauenden Seite festgelegt).
   2. Wählen Sie im Abschnitt **Typ** die Option **X.509-Zertifikat** aus.
   3. Klicken Sie im Abschnitt **Zertifikat** auf die Schaltfläche "Durchsuchen", und navigieren Sie zur X.509 Zertifikat-Datei, die Sie verwenden möchten. Dies ist eine .PFX-Datei. Wählen Sie die Datei aus, klicken Sie auf **Öffnen**, und geben Sie im Textfeld **Kennwort** das Zertifikatkennwort ein. Bitte beachten Sie, dass Sie zu Testzwecken ein selbstsigniertes Zertifikat verwenden können. Klicken Sie zum Erstellen eines selbstsignierten Zertifikats im Dialogfeld **ACS Filter Library** (dieses wird später beschrieben) auf die Schaltfläche **Neu**, oder verwenden Sie das Hilfsprogramm **encutil.exe** auf der [Projektwebsite][project website] des Azure-Starterkits für Java.
   4. Stellen Sie sicher, dass **Als primär festlegen** aktiviert ist. Die Seite **Add Token-Signing Certificate or Key** (Zertifikat oder Schlüssel für Tokensignierung hinzufügen) sollte nun in etwa wie folgt aussehen.
       ![Tokensignaturzertifikat hinzufügen][add_token_signing_cert]
   5. Klicken Sie auf **Speichern**, um Ihre Einstellungen zu speichern und die Seite **Add Token-Signing Certificate or Key** (Zertifikat oder Schlüssel für Tokensignierung hinzufügen) zu schließen.

Prüfen Sie als Nächstes die Informationen auf der Seite "Anwendungsintegration", und kopieren Sie den URI, den Sie benötigen, um Ihre Java-Webanwendung zur Verwendung des ACS zu konfigurieren.

## <a name="review-the-application-integration-page"></a>Prüfen der Seite "Anwendungsintegration"
Sie können alle Informationen und den Code, der zur Konfiguration Ihrer Java-Webanwendung (der Anwendung der vertrauenden Seite) für die Arbeit mit dem ACS erforderlich ist, auf der Seite "Anwendungsintegration" des ACS-Verwaltungsportals finden. Sie benötigen diese Informationen, wenn Sie Ihre Java-Webanwendung für eine Verbundauthentifizierung konfigurieren.

1. Klicken Sie im ACS-Verwaltungsportal auf **Application integration**.  
2. Klicken Sie auf der Seite **Application Integration** auf **Login Pages** (Anmeldeseiten).
3. Klicken Sie auf der Seite **Login Page Integration** (Anmeldeseitenintegration) auf **Azure-Web-App**.

Auf der Seite **Login Page Integration: Azure Web App** (Anmeldeseitenintegration: Azure-Web-App) wird die unter **Option 1: Link to an ACS-hosted login page** (Option 1: Link zu einer bei ACS gehosteten Anmeldeseite) aufgelistete URL in Ihrer Java-Webanwendung verwendet. Sie benötigen diesen Wert, wenn Sie die Bibliothek für Azure Access Control Service Filter zu ihrer Java-Anwendung hinzufügen.

## <a name="create-a-java-web-application"></a>Erstellen einer Java-Webanwendung
1. Klicken Sie im Eclipse-Menü auf **Datei**, auf **Neu** und anschließend auf **Dynamic Web Project** (Dynamisches Webprojekt). (Gehen Sie wie folgt vor, wenn **Dynamic Web Project** (Dynamisches Webprojekt) nach dem Klicken auf **Datei** und **Neu** nicht als verfügbares Projekt aufgeführt ist: Klicken Sie auf **Datei**, anschließend auf **Neu** und dann auf **Projekt**. Erweitern Sie die Option **Web**, und klicken Sie auf **Dynamic Web Project** (Dynamisches Webprojekt) und dann auf **Next** (Weiter).) Nennen Sie das Projekt für die Zwecke dieses Tutorials **MyACSHelloWorld**. (Verwenden Sie diesen Namen unbedingt, da in den weiteren Schritten dieses Lernprogramms erwartet wird, dass Ihre WAR-Datei MyACSHelloWorld heißt). Ihr Bildschirm sieht dann in etwa wie folgt aus:
   
    ![Erstellen eines Hallo-Welt-Projekts für den ACS (Beispiel)][create_acs_hello_world]
   
    Klicken Sie auf **Fertig stellen**.
2. Erweitern Sie in der Projektexplorer-Ansicht von Eclipse die Option **MyACSHelloWorld**. Klicken Sie mit der rechten Maustaste auf **WebContent**, und klicken Sie dann auf **Neu** sowie auf **JSP-Datei**.
3. Geben Sie der Datei im Dialogfeld **New JSP File** (Neue JSP-Datei) den Namen **index.jsp**. Nennen Sie den übergeordneten Ordner wie im Folgenden gezeigt MyACSHelloWorld/WebContent:
   
    ![Hinzufügen einer JSP-Datei für den ACS (Beispiel)][add_jsp_file_acs]
   
    Klicken Sie auf **Weiter**.

4. Wählen Sie im Dialogfeld **JSP-Vorlage auswählen** die Option **Neue JSP-Datei (HTML)**, und klicken Sie auf **Fertig stellen**.
5. Wenn in Eclipse die Datei „index.jsp“ geöffnet wird, geben Sie den Text **Hello ACS World!** ein, damit er dynamisch im vorhandenen `<body>`-Element angezeigt wird. Der aktualisierte `<body>` -Inhalt sollte wie folgt aussehen:
   
        <body>
          <b><% out.println("Hello ACS World!"); %></b>
        </body>
   
    Speichern Sie die Datei „index.jsp“.

## <a name="add-the-acs-filter-library-to-your-application"></a>Hinzufügen der ACS-Filter-Bibliothek zu Ihrer Anwendung
1. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, auf **Build Path** (Buildpfad) und anschließend auf **Configure Build Path** (Buildpfad konfigurieren).
2. Klicken Sie im Dialogfeld **Java Build Path** (Java-Buildpfad) auf die Registerkarte **Bibliotheken**.
3. Klicken Sie auf **Bibliothek hinzufügen**.
4. Klicken Sie auf **Azure Access Control Services Filter (von MS Open Tech)** und anschließend auf **Weiter**. Das Dialogfeld **Azure Access Control Services Filter** wird angezeigt.  (Im Feld **Speicherort** kann ein anderer Pfad genannt sein, je nachdem, wo Sie Eclipse installiert haben. Aufgrund von Software-Updates kann auch die Versionsnummer unterschiedlich sein.)
   
    ![ACS-Filter-Bibliothek hinzufügen][add_acs_filter_lib]
5. Kopieren Sie in einem Browser mit der geöffneten Seite **Login Page Integration** (Anmeldeseitenintegration) des Verwaltungsportals die im Feld **Option 1: Link to an ACS-hosted login page** (Link zu einer bei ACS gehosteten Anmeldeseite) aufgeführte URL, und geben Sie sie in das Feld **Option 1: Link to an ACS-hosted login page** des Eclipse-Dialogfelds ein.
6. Öffnen Sie in einem Browser die Seite **Edit Relying Party Application** (Anwendung der vertrauenden Seite bearbeiten) des Verwaltungsportals, kopieren Sie die URL im Feld **Bereich**, und kopieren Sie sie in das Feld **Relying Party Realm** (Bereich der vertrauenden Seite) des Eclipse-Dialogfelds.
7. Wenn Sie ein vorhandenes Zertifikat verwenden möchten, klicken Sie im Abschnitt **Sicherheit** des Eclipse-Dialogfelds auf **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie verwenden möchten, und klicken Sie auf **Öffnen**. Wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu**, um das Dialogfeld **Neues Zertifikat** anzuzeigen, und legen Sie dann das Kennwort, den Namen der CER-Datei und den Namen der PFX-Datei für das neue Zertifikat fest.
8. Aktivieren Sie **Embed the certificate in the WAR file**. Wenn Sie das Zertifikat auf diese Weise einbetten, wird es in Ihre Bereitstellung aufgenommen, ohne dass Sie es manuell als Komponente hinzufügen müssen. (Wenn Sie Ihr Zertifikat stattdessen extern aus Ihrer WAR-Datei übernehmen möchten, können Sie es als Rollenkomponente hinzufügen und **Embed the certificate in the WAR file**deaktivieren.)
9. [Optional] Aktivieren Sie **Require HTTPS connections** . Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
10. Wenn die Bereitstellung auf dem Serveremulator erfolgt, sehen Ihre Einstellungen für **Azure-ACS-Filter** in etwa wie folgt aus:
    
    ![Azure-ACS-Filter-Einstellungen für eine Bereitstellung auf dem Serveremulator][add_acs_filter_lib_emulator]
11. Klicken Sie auf **Fertig stellen**.
12. Wenn die Meldung erscheint, dass eine web.xml-Datei erstellt wird, klicken Sie auf **Ja** .
13. Klicken Sie auf **OK**, um das Dialogfeld **Java Build Path** (Java-Buildpfad) zu schließen.

## <a name="deploy-to-the-compute-emulator"></a>Bereitstellen auf dem Serveremulator
1. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, auf **Azure** und dann auf **Package for Azure** (Paket für Azure).
2. Geben Sie unter **Projektname** den Text **MyAzureACSProject** ein, und klicken Sie auf **Weiter**.
3. Wählen sie ein JDK und einen Anwendungsserver. (Diese Schritte werden detailliert im Lernprogramm [Creating a Hello World Application for Microsoft Azure in Eclipse (Erstellen einer Hallo-Welt-Anwendung für Azure in Eclipse, in englischer Sprache)](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) beschrieben).
4. Klicken Sie auf **Fertig stellen**.
5. Klicken Sie auf die Schaltfläche **Run in Azure Emulator** .
6. Schließen Sie nach dem Start Ihrer Java-Anwendung im Serveremulator alle Instanzen Ihres Browsers, damit andere laufende Browsersitzungen keine Konflikte mit Ihrem ACS-Anmeldungstest auslösen.
7. Führen Sie die Anwendung aus, indem Sie in Ihrem Browser <http://localhost:8080/MyACSHelloWorld/> öffnen (oder <https://localhost:8080/MyACSHelloWorld/>, wenn Sie **Require HTTPS connections** [HTTPS-Verbindungen erfordern] aktiviert haben). Sie sollten nun zur Anmeldung bei Windows Live ID aufgefordert und dann zur Rückgabe-URL weitergeleitet werden, die Sie für die Anwendung der vertrauenden Seite angegeben haben.
8. Wenn Sie die Anzeige Ihrer Anwendung beendet haben, klicken Sie auf die Schaltfläche **Reset Azure Emulator** .

## <a name="deploy-to-azure"></a>Bereitstellen in Azure
Für eine Bereitstellung in Azure müssen Sie den RP-Bereich und die Rückgabe-URL für Ihren ACS-Namespace ändern.

1. Ändern Sie im Azure-Verwaltungsportal auf der Seite **Edit Relying Party Application** (Anwendung der vertrauenden Seite bearbeiten) die Option **Bereich** in die URL Ihrer bereitgestellten Website. Ersetzen Sie **example** durch den DNS-Namen, den Sie für Ihre Bereitstellung festgelegt haben.
   
    ![RP-Bereich zur Verwendung bei der Produktion][relying_party_realm_production]
2. Ändern Sie **Rückgabe-URL** in die URL Ihrer Anwendung. Ersetzen Sie **example** durch den DNS-Namen, den Sie für Ihre Bereitstellung festgelegt haben.
   
    ![Rückgabe-URL der RP zur Verwendung bei der Produktion][relying_party_return_url_production]
3. Klicken Sie auf **Speichern** , um Ihren aktualisierten RP-Bereich und die geänderte Rückgabe-URL zu speichern.
4. Halten Sie die Seite **Integration der Anmeldeseite** in Ihrem Browser geöffnet, da Sie in Kürze etwas daraus kopieren müssen.
5. Klicken Sie im Eclipse-Projektexplorer mit der rechten Maustaste auf **MyACSHelloWorld**, auf **Build Path** (Buildpfad) und anschließend auf **Configure Build Path** (Buildpfad konfigurieren).
6. Klicken Sie auf die Registerkarte **Bibliotheken**, auf **Azure Access Control Services Filter** und anschließend auf **Bearbeiten**.
7. Kopieren Sie in einem Browser mit der geöffneten Seite **Login Page Integration** (Anmeldeseitenintegration) des Verwaltungsportals die im Feld **Option 1: Link to an ACS-hosted login page** (Link zu einer bei ACS gehosteten Anmeldeseite) aufgeführte URL, und geben Sie sie in das Feld **Option 1: Link to an ACS-hosted login page** des Eclipse-Dialogfelds ein.
8. Öffnen Sie in einem Browser die Seite **Edit Relying Party Application** (Anwendung der vertrauenden Seite bearbeiten) des Verwaltungsportals, kopieren Sie die URL im Feld **Bereich**, und kopieren Sie sie in das Feld **Relying Party Realm** (Bereich der vertrauenden Seite) des Eclipse-Dialogfelds.
9. Wenn Sie ein vorhandenes Zertifikat verwenden möchten, klicken Sie im Abschnitt **Sicherheit** des Eclipse-Dialogfelds auf **Durchsuchen**, navigieren Sie zum Zertifikat, das Sie verwenden möchten, und klicken Sie auf **Öffnen**. Wenn Sie ein neues Zertifikat erstellen möchten, klicken Sie auf **Neu**, um das Dialogfeld **Neues Zertifikat** anzuzeigen, und legen Sie dann das Kennwort, den Namen der CER-Datei und den Namen der PFX-Datei für das neue Zertifikat fest.
10. Behalten Sie die Aktivierung von **Embed the certificate in the WAR file** bei, da anzunehmen ist, dass Sie das Zertifikat in die WAR-Datei einbetten möchten.
11. [Optional] Aktivieren Sie **Require HTTPS connections** . Wenn Sie diese Option aktivieren, müssen Sie über das HTTPS-Protokoll auf Ihre Anwendung zugreifen. Wenn Sie nicht möchten, dass HTTPS-Verbindungen erforderlich sind, deaktivieren Sie diese Option.
12. Wenn die Bereitstellung in Azure erfolgt, sehen Ihre Einstellungen für Azure-ACS-Filter in etwa wie folgt aus:
    
    ![Azure-ACS-Filtereinstellungen für eine Bereitstellung in der Produktionsumgebung][add_acs_filter_lib_production]
13. Klicken Sie auf **Fertig stellen**, um das Dialogfeld **Bibliothek bearbeiten** zu schließen.
14. Klicken Sie auf **OK**, um das Dialogfeld **Properties for MyACSHelloWorld** (Eigenschaften für MyACSHelloWorld) zu schließen.
15. Klicken Sie in Eclipse auf die Schaltfläche **Publish to Azure Cloud** . Befolgen Sie die Anweisungen ähnlich wie im Abschnitt **Bereitstellen der Anwendung in Azure** des Themas [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) beschrieben. 

Schließen Sie nach der Bereitstellung Ihrer Webanwendung alle offenen Browser-Sitzungen, und führen Sie Ihre Webanwendung aus. Sie sollten nun die Aufforderung erhalten, sich mit Ihren Anmeldeinformationen für Windows Live ID anzumelden, und danach zur Rückgabe-URL Ihrer Anwendung der vertrauenden Seite weitergeleitet werden.

Wenn Sie Ihre Hello World-ACS-Anwendung nicht mehr benötigen, denken Sie daran, die Bereitstellung zu löschen (Informationen dazu finden Sie im Thema [Erstellen einer Hello World-Anwendung für Azure in Eclipse](http://msdn.microsoft.com/library/windowsazure/hh690944.aspx) .

## <a name="next_steps"></a>Nächste Schritte
Informationen zur vom ACS an Ihre Anwendung zurückgegebenen Security Assertion Markup Language (SAML) finden Sie unter [How to view SAML returned by the Azure Access Control Service][How to view SAML returned by the Azure Access Control Service] (Anzeigen der vom Azure Access Control Service zurückgegebenen SAML). Wenn Sie die ACS-Funktionalität genauer erforschen und mit anspruchsvolleren Szenarien experimentieren möchten, finden Sie weitere Informationen unter [Access Control Service 2.0][Access Control Service 2.0].

In diesem Beispiel wurde auch die Option **Embed the certificate in the WAR file** verwendet. Diese Option vereinfacht die Bereitstellung des Zertifikats. Wenn Sie stattdessen Ihr Signaturzertifikat von Ihrer WAR-Datei getrennt halten möchten, gehen Sie folgendermaßen vor:

1. Geben Sie im Abschnitt **Sicherheit** des Dialogfelds **Azure Access Control Services Filter** die Zeichenfolge **${env.JAVA_HOME}/mycert.cer** ein, und deaktivieren Sie **Embed the certificate in the WAR file** (Zertifikat in die WAR-Datei einbetten). (Wenn Ihre Zertifikatdatei einen anderen Namen hat, ersetzen Sie mycert.cer durch diesen.) Klicken Sie auf **Fertig stellen**, um das Dialogfeld zu schließen.
2. Kopieren Sie das Zertifikat als Komponente in Ihre Bereitstellung: Erweitern Sie im Eclipse-Projektexplorer **MyAzureACSProject**, klicken Sie mit der rechten Maustaste auf **WorkerRole1**, klicken Sie auf **Eigenschaften**, erweitern Sie **Azure Role** (Azure-Rolle), und klicken Sie auf **Komponenten**.
3. Klicken Sie auf **Hinzufügen**.
4. Im Dialogfeld **Komponente hinzufügen** :
   
   1. Im Abschnitt **Importieren** :
      1. Navigieren Sie mithilfe der Schaltfläche **Datei** zu dem Zertifikat, das Sie verwenden möchten. 
      2. Wählen Sie unter **Methode** die Option **Kopieren** aus.
   2. Klicken Sie unter **Als Name**auf das Textfeld, und übernehmen Sie den Standardnamen.
   3. Im Abschnitt **Bereitstellen** :
      1. Wählen Sie unter **Methode** die Option **Kopieren** aus.
      2. Geben Sie unter **To directory** (In Verzeichnis) die Zeichenfolge **%JAVA_HOME%** ein.
   4. Das Dialogfeld **Komponente hinzufügen** sollte nun etwa wie folgt aussehen.
      
       ![Zertifikatkomponente hinzufügen][add_cert_component]
   5. Klicken Sie auf **OK**.

An diesem Punkt wird Ihr Zertifikat in Ihre Bereitstellung aufgenommen. Bitte beachten sie: Unabhängig davon, ob Sie das Zertifikat in die WAR-Datei einbetten oder als Komponente in Ihre Bereitstellung einfügen, müssen Sie das Zertifikat wie im Abschnitt [Hochladen eines Zertifikats in Ihren ACS-Namespace][Upload a certificate to your ACS namespace] beschrieben in Ihren Namespace hochladen.

[What is ACS?]: #what-is
[Concepts]: #concepts
[Prerequisites]: #pre
[Create a Java web application]: #create-java-app
[Create an ACS Namespace]: #create-namespace
[Add Identity Providers]: #add-IP
[Add a Relying Party Application]: #add-RP
[Create Rules]: #create-rules
[Upload a certificate to your ACS namespace]: #upload-certificate
[Review the Application Integration Page]: #review-app-int
[Configure Trust between ACS and Your ASP.NET Web Application]: #config-trust
[Add the ACS Filter library to your application]: #add_acs_filter_library
[Deploy to the compute emulator]: #deploy_compute_emulator
[Deploy to Azure]: #deploy_azure
[Next steps]: #next_steps
[project website]: http://wastarterkit4java.codeplex.com/releases/view/61026
[How to view SAML returned by the Azure Access Control Service]: active-directory-java-view-saml-returned-by-access-control.md
[Access Control Service 2.0]: http://go.microsoft.com/fwlink/?LinkID=212360
[Windows Identity Foundation]: http://www.microsoft.com/download/en/details.aspx?id=17331
[Windows Identity Foundation SDK]: http://www.microsoft.com/download/en/details.aspx?id=4451
[Azure Management Portal]: https://manage.windowsazure.com
[acs_flow]: ./media/active-directory-java-authenticate-users-access-control-eclipse/ACSFlow.png

<!-- Eclipse-specific -->
[add_acs_filter_lib]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibrary.png
[add_acs_filter_lib_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryEmulator.png
[add_acs_filter_lib_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddACSFilterLibraryProduction.png

[relying_party_realm_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmEmulator.png
[relying_party_return_url_emulator]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLEmulator.png
[relying_party_realm_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyRealmProduction.png
[relying_party_return_url_production]: ./media/active-directory-java-authenticate-users-access-control-eclipse/RelyingPartyReturnURLProduction.png
[add_cert_component]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddCertificateComponent.png
[add_jsp_file_acs]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddJSPFileACS.png
[create_acs_hello_world]: ./media/active-directory-java-authenticate-users-access-control-eclipse/CreateACSHelloWorld.png
[add_token_signing_cert]: ./media/active-directory-java-authenticate-users-access-control-eclipse/AddTokenSigningCertificate.png

