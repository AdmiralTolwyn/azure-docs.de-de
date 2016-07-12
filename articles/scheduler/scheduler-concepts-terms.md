<properties
 pageTitle="Konzepte, Terminologie und Entitäten für Scheduler | Microsoft Azure"
 description="Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler, einschließlich Aufträgen und Auftragssammlungen. Zeigt ein umfangreiches Beispiel für einen geplanten Auftrag."
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article"
 ms.date="06/30/2016"
 ms.author="krisragh"/>

# Konzepte, Terminologie und Entitätshierarchie für Scheduler

## Entitätshierarchie für Scheduler

Die folgende Tabelle beschreibt die wichtigsten Ressourcen, die von der Scheduler-API verfügbar gemacht oder verwendet werden:

|Ressource | Beschreibung |
|---|---|
|**Auftragssammlung**|Eine Auftragssammlung enthält eine Gruppe von Aufträgen und dient zum Verwalten von Einstellungen, Kontingenten und Drosselungen für die Aufträge in der Sammlung. Eine Auftragssammlung wird von einem Abonnementbesitzer erstellt und fasst Aufträge auf der Grundlage von Verwendungs- oder Anwendungsgrenzen zusammen. Sie ist auf eine einzelne Region beschränkt. Außerdem ermöglicht sie die Erzwingung von Kontingenten, um die Verwendung aller Aufträge in der Sammlung zu beschränken. Dazu gehören „MaxJobs“ und „MaxRecurrence“.|
|**Auftrag**|Ein Auftrag definiert eine einzelne wiederkehrende Aktion mit einfachen oder komplexen Ausführungsstrategien. Beispiele für Aktionen sind HTTP, Speicherwarteschlange, Service Bus-Warteschlange oder Service Bus-Warteschlangenanforderungen.|
|**Auftragsverlauf**|Ein Auftragsverlauf liefert Details zur Ausführung eines Auftrags. Er gibt Aufschluss darüber, ob die Ausführung erfolgreich war, und enthält Details zur Antwort.|

## Entitätsverwaltung für Scheduler

Die Scheduler- und die Service Management-API machen für die Ressourcen allgemein folgende Vorgänge verfügbar:

|Funktion|Beschreibung und URI-Adresse|
|---|---|
|**Auftragssammlungsverwaltung**|GET-, PUT- und DELETE-Unterstützung zum Erstellen und Ändern von Auftragssammlungen und der darin enthaltenen Aufträge. Bei einer Auftragssammlung handelt es sich um einen Container für Aufträge, der mit Kontingenten und gemeinsamen Einstellungen verknüpft ist. Beispiele für Kontingente (siehe Erläuterung weiter unten) wären die maximale Anzahl von Aufträgen sowie das kleinste Wiederholungsintervall. <p>PUT und DELETE: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p><p>GET: `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}`</p>
|**Auftragsverwaltung**|GET-, PUT-, POST-, PATCH- und DELETE-Unterstützung zum Erstellen und Ändern von Aufträgen. Alle Aufträge müssen einer bereits vorhandenen Auftragssammlung angehören. Es gibt also keine implizite Erstellung. <p>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}`</p>|
|**Auftragsverlaufsverwaltung**|GET-Unterstützung zum Abrufen des Auftragsausführungsverlaufs für 60 Tage. Dieser enthält unter anderem Informationen zur verstrichenen Zeit sowie zu den Ergebnissen der Auftragsausführung. Bietet Unterstützung für Abfragezeichenfolgenparameter zur Filterung auf der Grundlage von Zustand und Status. <P>`https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Scheduler/jobCollections/{jobCollectionName}/jobs/{jobName}/history`</p>|

## Auftragstypen

Es gibt mehrere Arten von Aufträgen: HTTP-Aufträge (z.B. HTTPS-Aufträge mit SSL-Unterstützung), Speicherwarteschlangen-Aufträge, Service Bus-Warteschlangenaufträge und Service Bus-Themenaufträge. HTTP-Aufträge sind ideal, wenn Sie über einen Endpunkt einer vorhandenen Workload oder eines vorhandenen Diensts verfügen. Mit Speicherwarteschlangenaufträgen können Sie Nachrichten in Speicherwarteschlangen veröffentlichen. Daher eignen sich diese Aufträge perfekt für Workloads, die Speicherwarteschlangen verwenden. Ebenso sind Service Bus-Aufträge ideal für Workloads, für die Service Bus-Warteschlangen und -Themen verwendet werden.

## Die Auftragsentität im Detail

Ein geplanter Auftrag setzt sich grundsätzlich aus mehreren Komponenten zusammen:

- Die Aktion, die ausgeführt werden soll, wenn der Zeitgeber die Aktion auslöst.

- (Optional) Die Zeit, zu der der Auftrag ausgeführt werden soll.

- (Optional) Die Angabe, wann und wie oft den Auftrag wiederholt werden soll.

- (Optional) Eine Aktion, die ausgelöst werden soll, wenn bei der primären Aktion ein Fehler auftritt.

Intern enthält ein geplanter Auftrag auch vom System bereitgestellte Daten wie etwa die Zeit für die nächste geplante Ausführung.

Der folgende Code bietet ein umfangreiches Beispiel für einen geplanten Auftrag. Ausführliche Informationen finden Sie in den folgenden Abschnitten.

	{
		"startTime": "2012-08-04T00:00Z",               // optional
		"action":
		{
			"type": "http",
			"retryPolicy": { "retryType":"none" },
			"request":
			{
				"uri": "http://contoso.com/foo",        // required
				"method": "PUT",                        // required
				"body": "Posting from a timer",         // optional
				"headers":                              // optional

				{
					"Content-Type": "application/json"
				},
			},
		   "errorAction":
		   {
			   "type": "http",
			   "request":
			   {
				   "uri": "http://contoso.com/notifyError",
				   "method": "POST",
			   },
		   },
		},
		"recurrence":                                   // optional
		{
			"frequency": "week",                        // can be "year" "month" "day" "week" "minute"
			"interval": 1,                              // optional, how often to fire (default to 1)
			"schedule":                                 // optional (advanced scheduling specifics)
			{
				"weekDays": ["monday", "wednesday", "friday"],
				"hours": [10, 22]
			},
			"count": 10,                                 // optional (default to recur infinitely)
			"endTime": "2012-11-04",                     // optional (default to recur infinitely)
		},
		"state": "disabled",                           // enabled or disabled
		"status":                                       // controlled by Scheduler service
		{
			"lastExecutionTime": "2007-03-01T13:00:00Z",
			"nextExecutionTime": "2007-03-01T14:00:00Z ",
			"executionCount": 3,
											    "failureCount": 0,
												"faultedCount": 0
		},
	}

Wie im obigen geplanten Beispielauftrag zu sehen, besteht eine Auftragsdefinition aus mehreren Komponenten:

- Startzeit („startTime“)

- Aktion („action“), einschließlich Fehleraktion („errorAction“)

- Wiederholung („recurrence“)

- Zustand („State“)

- Status („status“)

- Wiederholungsrichtlinie („retryPolicy“)

Sehen wir uns die einzelnen Elemente im Detail an:

## startTime

"StartTime" ist die Startzeit und ermöglicht dem Aufrufer die Angabe eines Zeitzonenoffsets bei der Übertragung im [ISO-8601-Format](http://en.wikipedia.org/wiki/ISO_8601).

## action und errorAction

„action“ ist die Aktion, die bei jedem Vorkommen aufgerufen wird, und beschreibt eine Art von Dienstaufruf. Die Aktion wird gemäß dem angegebenen Zeitplan ausgeführt. Scheduler unterstützt HTTP, Speicherwarteschlange, Service Bus-Themen und Service Bus-Warteschlangenaktionen.

Die Aktion im obigen Beispiel ist eine HTTP-Aktion. Im folgenden Beispiel wird eine Speicherwarteschlangenaktion verwendet:

	{
			"type": "storageQueue",
			"queueMessage":
			{
				"storageAccount": "myStorageAccount",  // required
				"queueName": "myqueue",                // required
				"sasToken": "TOKEN",                   // required
				"message":                             // required
					"My message body",
			},
	}

Unten ist ein Beispiel für eine Service Bus-Themenaktion angegeben.

  "action": { "type": "serviceBusTopic", "serviceBusTopicMessage": { "topicPath": "t1", "namespace": "mySBNamespace", "transportType": "netMessaging", // Kann entweder netMessaging oder AMQP sein "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, }

Unten ist ein Beispiel für eine Service Bus-Warteschlangenaktion angegeben:


  "action": { "serviceBusQueueMessage": { "queueName": "q1", "namespace": "mySBNamespace", "transportType": "netMessaging", // Kann entweder netMessaging oder AMQP sein "authentication": { "sasKeyName": "QPolicy", "type": "sharedAccessKey" }, "message": "Some message", "brokeredMessageProperties": {}, "customMessageProperties": { "appname": "FromScheduler" } }, "type": "serviceBusQueue" }

„errorAction“ ist der Fehlerhandler (also die Aktion, die aufgerufen wird, wenn bei der primären Aktion ein Fehler auftritt). Mit dieser Variablen können Sie einen Endpunkt für die Fehlerbehandlung aufrufen oder eine Benutzerbenachrichtigung senden. So können Sie einen sekundären Endpunkt erreichen, falls der primäre Endpunkt nicht verfügbar ist (beispielsweise bei einem Notfall am Standort des Endpunkts), oder einen Fehlerbehandlungsendpunkt benachrichtigen. Auch bei der Fehleraktion kann es sich um eine einfache oder um eine komplexe Logik handeln, die auf anderen Aktionen basiert. Informationen zum Erstellen eines SAS-Tokens finden Sie unter [Erstellen und Verwenden einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/azure/jj721951.aspx).

## recurrence

Die Wiederholung besteht aus mehreren Teilen:

- Häufigkeit: in Minuten, Stunden, Tagen, Wochen, Monaten oder Jahren

- Intervall: Intervall mit der angegebenen Häufigkeit für die Wiederholung

- Vorgegebener Zeitplan: Angabe in Minuten, Stunden, Wochentagen, Monaten und Monatstagen für die Wiederholung

- Anzahl: Anzahl der Vorkommen

- Endzeit: Zeitangabe, nach der keine Aufträge mehr ausgeführt werden

Ein Auftrag wird wiederholt, wenn in der JSON-Definition ein Wiederholungsobjekt angegeben ist. Bei gleichzeitiger Angabe einer Anzahl und einer Endzeit gilt die zuerst auftretende Abschlussregel.

## state

Der Auftrag kann einen von vier Zuständen haben: aktiviert, deaktiviert, abgeschlossen oder fehlerhaft. Aufträge können mittels „PUT“ oder „PATCH“ aktiviert oder deaktiviert werden. Bei einem abgeschlossenen oder fehlerhaften Auftrag ist der Zustand endgültig und kann nicht aktualisiert werden. (Der Auftrag kann allerdings weiterhin gelöscht werden). Im Anschluss sehen Sie ein Beispiel für die state-Eigenschaft:


    	"state": "disabled", // enabled, disabled, completed, or faulted
Abgeschlossene und fehlerhafte Aufträge werden nach 60 Tagen gelöscht.

## status

Sobald ein Scheduler-Auftrag gestartet wurde, werden Informationen zum aktuellen Status des Auftrags zurückgegeben. Dieses Objekt wird vom System festgelegt und kann nicht vom Benutzer festgelegt werden. Es ist jedoch in das Auftragsobjekt integriert (und nicht als separate verknüpfte Ressource vorhanden), um den Status eines Auftrags problemlos abrufen zu können.

Der Auftragsstatus enthält den Zeitpunkt der vorherigen Ausführung (sofern zutreffend), den Zeitpunkt der nächsten geplanten Ausführung (bei laufenden Aufträgen) und die Anzahl der Auftragsausführungen.

## retryPolicy

Für den Fall, dass bei einem Scheduler-Auftrag ein Fehler auftritt, kann durch Angabe einer Wiederholungsrichtlinie bestimmt werden, ob und auf welche Weise die Aktion wiederholt werden soll. Hierzu wird das Objekt **retryType** verwendet. Ist keine Wiederholungsrichtlinie vorhanden, wird das Objekt auf **none** festgelegt (wie weiter oben zu sehen). Ist eine Wiederholungsrichtlinie vorhanden, legen Sie es auf **fixed** fest.

Für eine Wiederholungsrichtlinie können zwei zusätzliche Einstellungen angegeben werden: ein Wiederholungsintervall (**retryInterval**) und die Anzahl von Wiederholungen (**retryCount**).

Das Wiederholungsintervall, das durch das Objekt **retryInterval** angegeben wird, ist das Intervall zwischen den Wiederholungen. Der Standardwert ist 30 Sekunden, die konfigurierbare Mindestwert beträgt 15 Sekunden, und der maximale Wert beträgt 18 Monate. Aufträge in Auftragssammlungen vom Typ „Free“ haben einen konfigurierbaren Mindestwert von 1 Stunde. Es wird im ISO-8601-Format definiert. Gleichermaßen wird der Wert für die Anzahl von Wiederholungen mit dem Objekt **retryCount** angegeben; es bestimmt, wie oft versucht wird, einen Vorgang zu wiederholen. Der Standardwert ist 4, der maximal zulässige Wert ist 20. Die Angabe von **retryInterval** und **retryCount** ist jeweils optional. Wenn **retryType** auf **fixed** festgelegt ist und keine expliziten Werte angegeben werden, werden die Standardwerte verwendet.

## Weitere Informationen

 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Hochverfügbarkeit und Zuverlässigkeit von Azure Scheduler](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Azure Scheduler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0706_2016-->