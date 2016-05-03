<properties
   pageTitle="Erstellen einer EAI-Logik-App mithilfe von VETR in Logik-Apps in Azure App Service | Microsoft Azure"
   description="Überprüfungs-, Codier- und Transformationsfeatures der BizTalk XML Services"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/20/2016"
   ms.author="rajram"/>


# Erstellen einer EAI-Logik-App mithilfe von VETR

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

In den meisten Szenarien für Enterprise Application Integration (EAI, Unternehmensanwendungsintegration) werden Daten zwischen einer Quelle und einem Ziel vermittelt. Solche Szenarien weisen häufig einen gemeinsamen Satz von Anforderungen auf:

- Sicherstellen, dass Daten aus anderen Systemen ordnungsgemäß formatiert sind.
- Anwenden von Nachschlagevorgängen auf eingehende Daten, um Entscheidungen zu treffen.
- Konvertieren von Daten von einem Format in ein anderes, (z. B. vom Datenformat eines CRM-Systems in ein Datenformat eines ERP-Systems).
- Weiterleiten von Daten an die gewünschten Anwendungen oder Systeme

Dieser Artikel beschreibt ein gängiges Integrationsmuster: "unidirektionale Nachrichtenvermittlung" bzw. VETR (Validate, Enrich, Transformation, Route [Überprüfen, Verfeinern, Transformieren, Weiterleiten]). Das VETR-Muster vermittelt Daten zwischen einer Quellentität und einer Zielentität. In der Regel sind die Quelle und das Ziel Datenquellen.

Nehmen wir eine Website, die Bestellungen annimmt. Benutzer senden über HTTP Bestellungen an das System. Hinter den Kulissen überprüft das System die eingehenden Daten auf Richtigkeit, normalisiert sie und stellt sie zur weiteren Verarbeitung in eine Service Bus-Warteschlange. Das System entnimmt der Warteschlange Bestellungen, die in einem bestimmten Format erwartet werden. Der Datenfluss von A bis Z sieht folglich so aus:

**HTTP** → **Überprüfen** → **Transformieren** → **Service Bus**

![Grundlegender VETR-Datenfluss][1]

Die folgenden BizTalk-API-Apps unterstützen die Erstellung dieses Musters:

* **HTTP-Trigger**: Quelle für das Auslösen des Nachrichtenereignisses
* **Überprüfen**: Überprüft die Richtigkeit der eingehenden Daten
* **Transformieren**: Transformiert Daten aus dem Eingangsformat in das vom nachgelagerten System geforderte Format
* **Service Bus-Connector**: Zielentität, an die Daten gesendet werden


## Erstellen eines einfachen VETR-Musters
### Grundlagen

Wählen Sie im Azure-Portal nacheinander **+ Neu**, **Web + mobil** und **Logik-App** aus. Wählen Sie einen Namen, ein Abonnement, eine Ressourcengruppe und einen Standort, der funktioniert. Ressourcengruppen dienen als Container für Ihre Apps. Alle Ressourcen für Ihre Anwendung befinden sich in der gleichen Ressourcengruppe.

Als Nächstes fügen wir Trigger und Aktionen hinzu.


## Hinzufügen eines HTTP-Triggers
1. Klicken Sie in **Logik-App-Vorlagen** auf **App von Grund auf neu erstellen**.
1. Wählen Sie im Katalog **HTTP-Listener** aus, um einen neuen Listener zu erstellen. Nennen Sie ihn **HTTP1**.
2. Belassen Sie die Einstellung **Antwort automatisch senden?** auf „False“ festgelegt. Konfigurieren Sie die Triggeraktion, indem Sie _HTTP-Methode_ auf _POST_ und die Einstellung _Relative URL_ auf _\\OneWayPipeline_ festlegen: ![HTTP-Trigger][2]
3. Klicken Sie auf Sie das grüne Häkchen, um den Trigger fertigzustellen.

## Hinzufügen der Überprüfungsaktion

Lassen Sie uns nun Aktionen eingeben, die ausgeführt werden, wenn der Trigger ausgelöst wird, d. h. wenn ein Aufruf am HTTP-Endpunkt empfangen wird.

1. Fügen Sie **BizTalk XML Validator** aus dem Katalog hinzu, und wählen Sie die Benennung _(Validate1)_, um eine Instanz zu erstellen.
2. Konfigurieren Sie ein XSD-Schema, um die eingehenden XML-Nachrichten zu überprüfen. Wählen Sie die Aktion _Überprüfen_ und dann _triggers('httplistener').outputs.Content_ als Wert für den Parameter _inputXml_ aus.

Die Überprüfungsaktion ist nun die erste Aktion hinter dem HTTP-Listener:

![BizTalk XML Validator][3]

Auf ähnliche Weise fügen wir den Rest der Aktionen hinzu.

## Hinzufügen der Transformationsaktion
Nun wollen wir Transformationen zum Normalisieren der eingehenden Daten konfigurieren.

1. Fügen Sie aus dem Katalog **BizTalk-Transformationsdienst** hinzu.
2. Um eine Transformation zum Transformieren der eingehenden XML-Nachrichten zu konfigurieren, wählen Sie die Aktion **Transformation** als die Aktion aus, die ausgeführt wird, wenn diese API aufgerufen wird. Wählen Sie ```triggers(‘httplistener’).outputs.Content``` als Wert für _inputXml_ aus. *Map* ist ein optionaler Parameter, da die eingehenden Daten mit allen konfigurierten Transformationen abgeglichen werden. Nur diejenigen, die mit dem Schema übereinstimmen, werden angewendet.
3. Schließlich erfolgt die Transformation nur nach erfolgreicher Überprüfung. Um diese Bedingung zu konfigurieren, klicken Sie rechts oben auf das Zahnradsymbol und wählen _Eine zu erfüllende Bedingung hinzufügen_ aus. Legen Sie die Bedingung auf ```equals(actions('xmlvalidator').status,'Succeeded')``` fest:  

![BizTalk-Transformationen][4]


## Hinzufügen des Service Bus-Connectors
Als Nächstes fügen wir eine Service Bus-Warteschlange als Ziel hinzu, in das Daten geschrieben werden.

1. Fügen Sie einen **Service Bus-Connector** aus dem Katalog hinzu. Legen Sie **Name** auf _Servicebus1_, **Verbindungszeichenfolge** auf die Verbindungszeichenfolge für Ihre Service Bus-Instanz und **Entitätsname** auf _Warteschlange_ fest. Überspringen Sie **Abonnementname**.
2. Wählen Sie die Aktion **Nachricht senden** aus, und legen das Feld **Inhalt** für die Aktion auf _actions('transformservice').outputs.OutputXml_ fest.
3. Legen Sie das Feld **Inhaltstyp** auf *application/xml* fest.  

![Service Bus][5]


## Senden einer HTTP-Antwort
Nach Abschluss der Pipelineverarbeitung senden Sie eine HTTP-Antwort für "Erfolg" und "Fehler" mit den folgenden Schritten zurück:

1. Fügen Sie einen **HTTP-Listener** aus dem Katalog hinzu, und wählen Sie die Aktion **HTTP-Antwort senden**.
2. Legen Sie **Antwort-ID** auf *Nachricht senden* fest.
2. Legen Sie **Antwortinhalt** auf *Pipelineverarbeitung abgeschlossen* fest.
3. Legen Sie **Antwortstatuscode** auf *200* fest, um „HTTP 200 OK“ anzuzeigen.
4. Klicken Sie rechts oben auf das Dropdownmenü, und wählen Sie **Eine zu erfüllende Bedingung hinzufügen** aus. Ändern Sie die Bedingung in den folgenden Ausdruck: ```@equals(actions('azureservicebusconnector').status,'Succeeded')``` <br/>
5. Wiederholen Sie diese Schritte, um eine HTTP-Antwort auch bei einem Fehler zu senden. Ändern Sie die **Bedingung** auf den folgenden Ausdruck: ```@not(equals(actions('azureservicebusconnector').status,'Succeeded'))``` <br/>
6. Klicken Sie auf **OK** und dann auf **Erstellen**.



## Abschluss
Jedes Mal, wenn jemand eine Nachricht an den HTTP-Endpunkt sendet, wird die App ausgelöst und die gerade erstellten Aktionen werden ausgeführt. Zum Verwalten solcher Logik-Apps, die Sie erstellen, klicken Sie im Azure-Portal auf **Durchsuchen** und wählen dann **Logik-Apps** aus. Wählen Sie die App aus, um weitere Informationen anzuzeigen.

Einige hilfreichen Themen:

[Verwalten und Überwachen Ihrer API-Apps und Connectors](app-service-logic-monitor-your-connectors.md) <br/> [Überwachen Ihrer Logik-Apps](app-service-logic-monitor-your-logic-apps.md)

<!--image references -->
[1]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BasicVETR.PNG
[2]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/HTTPListener.PNG
[3]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkXMLValidator.PNG
[4]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/BizTalkTransforms.PNG
[5]: ./media/app-service-logic-create-EAI-logic-app-using-VETR/AzureServiceBus.PNG

<!---HONumber=AcomDC_0420_2016-->