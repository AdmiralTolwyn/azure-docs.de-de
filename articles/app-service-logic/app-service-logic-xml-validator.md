<properties
   pageTitle="Verwendung des BizTalk XML Validators in Logik-Apps in Azure App Service | Microsoft Azure"
   description="Validieren von Schemas mithilfe des BizTalk XML Validators in Ihrer Logik-App"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajram"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="rajram"/>

# BizTalk XML Validator

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Verwenden Sie den BizTalk XML Validator-Connector in Ihrer App zum Validieren von XML-Daten anhand vordefinierter XML-Schemas. Benutzer können vorhandene Schemas verwenden oder Schemas auf Grundlage einer Flatfile-Instanz, JSON-Instanz oder vorhandenen Connectors generieren.

## BizTalk XML Validator verwenden
Um den BizTalk XML Validator zu verwenden, erstellen Sie zunächst eine Instanz der BizTalk XML Validator-API-App. Dies kann entweder Inline beim Erstellen einer Logik-App oder durch Auswählen der BizTalk XML Validator-API-App aus dem Azure Marketplace erfolgen.

### BizTalk XML Validator konfigurieren
BizTalk XML Validator verwendet Schemas als Teil der Konfiguration. Benutzer können das API-App-Konfigurationsblatt entweder durch Starten der API-App direkt aus dem Azure-Portal oder durch Doppelklicken auf die API-App auf der Designeroberfläche starten:

![BizTalk XML Validator Konfiguration][1]

Benutzer können im API-App-Blatt Schemas konfigurieren, indem sie *Schemas* auswählen:

![BizTalk XML Validator Schemas Teil][2]

Benutzer können Schemas von der Festplatte hochladen oder sie aus einer Flatfile-Instanz oder einer JSON-Instanz generieren:

![BizTalk XML Validator Schemas][3]


### Verwenden des BizTalk Flat File Encoder auf der Entwurfsoberfläche
Nach der Konfiguration können Benutzer *->* auswählen und aus der Liste der Aktionen eine Aktion wählen:

![BizTalk XML Validator Liste der Aktionen][4]

#### XML validieren

Die Aktion „XML validieren“ validiert eine vorhandene XML-Eingabe anhand vorkonfigurierter Schemas:

![BizTalk XML Validator XML validieren][5]

Parameter|Typ|Beschreibung des Parameters
---|---|---
Eingabe-XML|Zeichenfolge|Zu validierende XML

Die Aktion gibt die Ausgabe als Objekt zurück. Die Ausgabe enthält das Modell, das die Antwort von XML Validator darstellt. Es besteht aus Ergebnis, Namen des Schemas, Stammknoten und Fehlerbeschreibung.


<!-- References -->
[1]: ./media/app-service-logic-xml-validator/XmlValidator.ClickToConfigure.PNG
[2]: ./media/app-service-logic-xml-validator/XmlValidator.SchemasPart.PNG
[3]: ./media/app-service-logic-xml-validator/XmlValidator.SchemaUpload.PNG
[4]: ./media/app-service-logic-xml-validator/XmlValidator.ListOfActions.PNG
[5]: ./media/app-service-logic-xml-validator/XmlValidator.ValidateXml.PNG

<!---HONumber=AcomDC_0727_2016-->