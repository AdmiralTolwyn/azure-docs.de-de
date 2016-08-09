<properties
	pageTitle="Liste der verfügbaren Connectors und API-Apps | Microsoft Azure App Service"
	description="Erfahren Sie etwas über die Connectors und API-Apps in Azure App Service"
	services="logic-apps"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="erikre"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/28/2016"
	ms.author="mandia"/>


# Liste der Connectors und API-Apps in Logik-Apps
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Informationen zur allgemein verfügbaren Version von Logik-Apps finden Sie unter [Neue Connectorliste](../connectors/apis-list.md).

Hier werden alle verfügbaren Connectors und API-Apps aufgelistet, die von Microsoft für die Verwendung in Logik-Apps erstellt wurden.

Informationen zu Preisen und eine Liste der auf den einzelnen Dienstebenen verfügbaren Komponenten finden Sie unter [Azure App Service-Preise](https://azure.microsoft.com/pricing/details/app-service/).

> [AZURE.NOTE] Wenn Sie Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, besuchen Sie [Logik-App testen](https://tryappservice.azure.com/?appservice=logic). Sie können sofort eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Wichtige Connectors
Die folgende Tabelle listet alle verfügbaren von Microsoft erstellten Connectors und API-Apps auf, die als Haupt-Connectors zur Verfügung stehen:

Name | Beschreibung
--- | ---
[Azure Service Bus](app-service-logic-connector-azureservicebus.md) | Kann Nachrichten aus Service Bus-Warteschlangen und -Themen senden sowie Nachrichten aus Service Bus-Warteschlangen und -Abonnements empfangen.
[Bing-Übersetzer](https://azure.microsoft.com/marketplace/partners/microsoft_com/bingtranslator) | Für Übersetzungen von Text in eine andere Sprache in Bing.
[HTTP](app-service-logic-connector-http.md) | Der HTTP-Listener öffnet einen Endpunkt, der als HTTP-Server fungiert und auf eingehende HTTP- oder HTTPS-Anforderungen lauscht. Die HTTP-Aktion erfordert keine API-App und wird nativ innerhalb der Logik-Apps unterstützt.
[Microsoft Office 365](app-service-logic-connector-office365.md) | Der Office 365-Connector kann über Ihr Office 365-Konto E-Mails senden und empfangen sowie Ihren Kalender und Ihre Kontakte verwalten.
[QuickBooks](app-service-logic-connector-quickbooks.md) | Mit diesem Connector können Sie verschiedene Aufgaben ausführen, u. a. unterschiedliche Entitäten aus Intuit QuickBooks erstellen, aktualisieren und abfragen, beispielsweise Kunden, Artikel, Rechnungen und so weiter.
[Slack](app-service-logic-connector-slack.md) | Herstellen einer Verbindung mit Slack und Veröffentlichen von Nachrichten auf Slack-Kanälen.
[Warten](app-service-logic-connector-wait.md) | Verwenden Sie diesen Connector, um die Ausführung der App zu verzögern. Die App kann für einen bestimmten Zeitraum oder bis zu einem Ereignis an einem bestimmten Zeitpunkt verzögert werden.


## Enterprise-Integrationsconnectors
Die folgende Tabelle enthält alle verfügbaren von Microsoft erstellten Connectors und API-Apps, die als Enterprise-Integrationsconnectors zur Verfügung stehen:

Name | Beschreibung
------------- | -------------
[AS2-Connector](app-service-logic-connector-as2.md) | Empfangen und Senden von Nachrichten mithilfe des AS2-Transportprotokolls. Daten werden mithilfe von digitalen Zertifikaten und Verschlüsselung sicher und zuverlässig übertragen.
[BizTalk EDIFACT](app-service-logic-connector-edifact.md) | Empfängt und sendet Nachrichten mithilfe des EDIFACT-Protokolls in der Business-to-Business-Kommunikation.
[BizTalk Flat File Encoder](app-service-logic-flatfile-encoder.md) | Ermöglicht die Interoperabilität zwischen Flatfiledaten (wie Excel und CSV) und XML-Daten. Mit dieser API-App können Sie eine Flatfile-Instanz in XML konvertieren und umgekehrt.
[BizTalk JSON Encoder](app-service-logic-connector-jsonencoder.md) | Ein Encoder und Decoder helfen Ihrer App bei der Interoperation zwischen JSON- und XML-Daten. Sie können damit eine angegebene JSON-Instanz in XML konvertieren und umgekehrt.
[BizTalk-Regeln](app-service-logic-use-biztalk-rules.md) | Verwenden Sie BizTalk-Regeln zum Definieren und Steuern der Geschäftslogik innerhalb einer Organisation. Geschäftsrichtlinien können ohne erneute Kompilierung oder ohne erneute Bereitstellung der verbundenen Anwendungen aktualisiert werden.
[BizTalk-Handelspartnerverwaltung](app-service-logic-connector-tpm.md) | Definiert und speichert Business-to-Business-Beziehungen anhand von Partnern, Vereinbarungen und Schemas sowie anhand von in Vereinbarungen verwendeten Zertifikaten. Diese Beziehungen werden mit den AS2-, EDIFACT- und X12-API-Apps durchgesetzt.
[BizTalk-Transformationsdienst](app-service-logic-transform-xml-documents.md) | Konvertiert Daten von einem Format in ein anderes. Sie können auch ein vorhandenes Schema (TRFM-Datei) hochladen, die Links zwischen Quell- und Zielschema anzeigen und die Funktion „Test“ mit einem Beispieleingabe-XML-Inhalt verwenden. Verschiedene integrierte Funktionen sind ebenfalls verfügbar, einschließlich Zeichenfolgenbearbeitung, bedingte Zuweisung usw.
[BizTalk X12](app-service-logic-connector-x12.md) | Empfängt und sendet Nachrichten mithilfe des X12-Protokolls in der Business-to-Business-Kommunikation.
[BizTalk XML Validator](app-service-logic-xml-validator.md) | Überprüft XML-Daten im Abgleich mit vordefinierten XML-Schemas. Sie können vorhandene Schemas verwenden oder Schemas auf Grundlage einer Flatfile-Instanz, JSON-Instanz oder vorhandenen Connectors generieren.
[BizTalk XPath Extractor](app-service-logic-xpath-extract.md) | Sucht und extrahiert Daten aus XML-Inhalt basierend auf der ausgewählten XPath-Sprache.
[DB2-Connector](app-service-logic-connector-db2.md) | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM DB2-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen. <br/><br/>Keine Trigger. Aktionen umfassen die Auswahl von Tabellen, das Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>Dieser Connector enthält auch den Microsoft-Client für DRDA zum Herstellen einer Verbindung mit einem Informix-Server über ein TCP/IP-Netzwerk.
[Datei](app-service-logic-connector-file.md) | Mit diesem Connector können Sie eine Verbindung mit dem lokalen Dateisystem oder Netzwerk herstellen und verschiedene Dateiaufgaben, einschließlich Hochladen, Löschen und Auflisten von Dateien, und vieles mehr ausführen.
[FTP<br/>FTPS](app-service-logic-connector-ftp.md) | Stellt eine Verbindung mit einem FTP-/FTPS-Server her und ermöglicht u. a. die Ausführung verschiedener FTP-Tasks, einschließlich Hochladen, Abrufen und Löschen von Dateien.
[Informix](app-service-logic-connector-informix.md) | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einer IBM Informix-Datenbank her. Er kann Befehlen der Informix Structured Query Language Web-API- und OData-API-Vorgänge zuordnen.<br/><br/>Keine Trigger. Aktionen umfassen die Auswahl von Tabellen, das Einfügen, Aktualisieren und Löschen sowie benutzerdefinierte Anweisungen.<br/><br/>Lokal oder mit VPN oder Azure kann ExpressRoute verwendet werden. Dieser Connector enthält auch einen Microsoft-Client für DRDA zum Herstellen einer Verbindung mit einem Informix-Server über ein TCP/IP-Netzwerk.
[Microsoft SQL Server](app-service-logic-connector-sql.md) | Stellt eine Verbindung mit dem lokalen SQL Server oder einer Azure SQL-Datenbank her. Sie können Einträge in einer SQL-Datenbanktabelle erstellen, aktualisieren, abrufen und löschen.
MQ | Stellt lokal und auf einem virtuellen Azure-Computer unter einem Windows-Betriebssystem eine Verbindung mit einem IBM WebSphere MQ-Server, Version 8, her. Bei lokaler Verwendung können VPN- oder Azure ExpressRoute-Verbindungen verwendet werden. Der Connector enthält außerdem den Microsoft-Client für MQ.<br/><br/>Keine Trigger. Keine Aktionen.<br/><br/>**Hinweis** Kann derzeit nicht mit Logic Apps verwendet werden.
[Oracle-Datenbank](app-service-logic-connector-oracle.md) | Stellt eine Verbindung mit der lokalen Oracle-Datenbank her und kann Einträge in einer Datenbanktabelle erstellen, aktualisieren, abrufen und löschen.
[POP3](app-service-logic-connector-pop3.md) (Post Office Protocol)| Stellt eine Verbindung mit einem POP3-Server her, um E-Mails mit Anlagen abzurufen.
[SAP](app-service-logic-connector-sap.md) | Stellt eine Verbindung mit einem lokalen SAP-Server her, ruft RFCs, BAPIs und tRFCs auf und sendet IDOCs.

## Connectors als Trigger
Mehrere Connectors bieten Trigger für Logik-Apps. Diese Trigger haben zwei Typen:

1. Abfragetrigger: Diese Trigger fragen den Dienst in angegebenen Abständen auf neue Daten ab. Sobald neue Daten verfügbar sind, wird eine neue Instanz der Logik-App mit den Daten als Eingabe ausgeführt. Damit die gleichen Daten nicht mehrere Male verarbeitet werden, kann der Trigger Daten bereinigen, die gelesen und an die Logik-App übergeben wurden. Beispiele für solche Connectors sind "Datei", "SQL" und "Azure Storage".
2. Push-Trigger: Diese Trigger lauschen auf den Eingang von Daten an einem Endpunkt oder das Eintreten eines Ereignisses. Dann lösen sie eine neue Instanz einer Logik-App aus. Beispiele für solche Connectors sind "HTTP-Listener" und "Twitter".

## Connectors als Aktionen
Connectors können innerhalb Ihrer Logik-App auch als Aktionen verwendet werden. Aktionen sind hilfreich für die Suche nach Daten in der Logik-App, die dann bei der Ausführung verwendet werden können. Beispielsweise kann es sein, dass Sie Daten aus einer SQL-­Datenbank für zusätzliche Informationen zu einem Kunden bei der Verarbeitung einer Bestellung nachschauen müssen. Oder Sie müssen möglicherweise Daten in ein Ziel schreiben, darin aktualisieren oder löschen. Sie können dazu die Aktionen verwenden, die von den Connectors bereitgestellt werden. Aktionen werden Vorgängen in API-Apps zugeordnet (gemäß ihren Swagger-Metadaten).

## Erstellen Sie eigene Connectors und API-Apps
[Referenz zu Connectors und API-Apps](http://aka.ms/appservicesconnectorreference) [Trigger für Azure App Service-API-Apps](../app-service-api/app-service-api-dotnet-triggers.md) [Referenz zu Logik-Apps](https://msdn.microsoft.com/library/azure/dn948510.aspx)

## Weitere Informationen zu Connectors und API-Apps
[Was sind Connectors und BizTalk-API-Apps?](app-service-logic-what-are-biztalk-api-apps.md) [Verwenden des Hybrid Connection Managers in Azure App Service](app-service-logic-hybrid-connection-manager.md) [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md)

<!---HONumber=AcomDC_0803_2016-->