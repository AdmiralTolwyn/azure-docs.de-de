---
title: Migrieren von Azure Scheduler zu Azure Logic Apps
description: Erfahren Sie, wie Sie Aufträge im Azure Scheduler-Dienst, der eingestellt wird, durch Azure Logic Apps ersetzen können.
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/20/2018
ms.openlocfilehash: 25ed66fd75301475542dbac8e8a01670ee37563c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60531681"
---
# <a name="migrate-azure-scheduler-jobs-to-azure-logic-apps"></a>Migrieren von Azure Scheduler-Aufträgen zu Azure Logic Apps

> [!IMPORTANT]
> Azure Logic Apps ersetzt Azure Scheduler, der eingestellt wird. Führen Sie zum Planen von Aufträgen die Schritte in diesem Artikel durch, um eine Migration zu Azure Logic Apps durchzuführen.

In diesem Artikel wird gezeigt, wie Sie einmalige und wiederkehrende Aufträge planen können, indem Sie anstelle von Azure Scheduler automatisierte Workflows mit Azure Logic Apps erstellen. Wenn Sie geplante Aufträge mit Logic Apps erstellen, haben Sie folgende Vorteile:

* Sie müssen sich keine Gedanken um das Konzept einer *Auftragssammlung* machen, da jede Logik-App eine separate Azure-Ressource darstellt.

* Sie können mithilfe einer einzelnen Logik-App mehrere einmalige Aufträge ausführen.

* Der Azure Logic Apps-Dienst unterstützt Zeitzonen und die Sommerzeit (Daylight Saving Time, DST).

Weitere Informationen finden Sie unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) Erstellen Sie alternativ Ihre erste Logik-App mit dieser Schnellstartanleitung: [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>.

* Verwenden Sie zum Auslösen Ihrer Logik-App durch Senden von HTTP-Anforderungen ein Tool wie die [Postman-Desktop-App](https://www.getpostman.com/apps).

## <a name="schedule-one-time-jobs"></a>Planen von einmaligen Aufträgen

Sie können mehrere einmalige Aufträge ausführen, indem Sie eine einzelne Logik-App erstellen. 

### <a name="create-your-logic-app"></a>Erstellen Ihrer Logik-App

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App im Logik-App-Designer. 

   Führen Sie die grundlegenden in diesem Artikel aus: [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Geben Sie im Suchfeld „Beim Empfang einer HTTP-Anforderung“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Beim Empfang einer HTTP-Anforderung** 

   ![Hinzufügen eines Request-Triggers](./media/migrate-from-scheduler-to-logic-apps/request-trigger.png)

1. Für den Anforderungstrigger können Sie optional ein JSON-Schema angeben, wodurch der Logik-App-Designer die Struktur für die Eingaben aus der eingehenden Anforderung erfassen und Ihnen die Auswahl der Ausgaben später in Ihrem Workflow vereinfachen kann.

   Zum Angeben eines Schemas geben Sie dieses im Feld **JSON-Schema für Anforderungstext** ein, z.B.: 

   ![Anforderungsschema](./media/migrate-from-scheduler-to-logic-apps/request-schema.png)

   Wenn Sie kein Schema haben, jedoch über eine Beispielnutzlast im JSON-Format verfügen, können Sie aus dieser Nutzlast ein Schema generieren.

   1. Wählen Sie im Anforderungstrigger **Beispielnutzlast zum Generieren eines Schemas verwenden**.

   1. Geben Sie unter **Geben oder fügen Sie eine JSON-Beispielnutzlast ein** Ihre Beispielnutzlast ein, und wählen Sie dann **Fertig**. Beispiel:

      ![Beispielnutzlast](./media/migrate-from-scheduler-to-logic-apps/sample-payload.png)

1. Klicken Sie unter dem Trigger auf **Nächster Schritt**. 

1. Geben Sie im Suchfeld den Begriff „Verzögern bis“ als Filter ein. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Verzögern bis**

   Diese Aktion hält den Workflow Ihrer Logik-App bis zum angegebenen Datum zur jeweiligen Uhrzeit an.

   ![Hinzufügen der Aktion „Verzögern bis“](./media/migrate-from-scheduler-to-logic-apps/delay-until.png)

1. Geben Sie den Zeitstempel für den Zeitpunkt an, wenn Sie den Logik-App-Workflow starten möchten. 

   Wenn Sie in das Feld **Zeitstempel** klicken, wird eine Liste mit dynamischem Inhalt angezeigt, sodass Sie optional eine Ausgabe über den Trigger auswählen können.

   ![Angeben von Details zu „Verzögern bis“](./media/migrate-from-scheduler-to-logic-apps/delay-until-details.png)

1. Fügen Sie alle auszuführenden Aktionen hinzu, indem Sie eine Auswahl aus den [ca. über 200 Connectors](../connectors/apis-list.md) treffen. 

   Beispielsweise können Sie eine HTTP-Aktion, die eine Anforderung an eine URL sendet, oder Aktionen einschließen, die mit Storage-Warteschlangen, Service Bus-Warteschlangen oder Service Bus-Themen arbeiten: 

   ![HTTP-Aktion](./media/migrate-from-scheduler-to-logic-apps/request-http-action.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App.

   ![Speichern Ihrer Logik-App](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

   Wenn Sie Ihre Logik-App zum ersten Mal speichern, wird die Endpunkt-URL für den Anforderungstrigger Ihrer Logik-App im Feld **URL für 'HTTP Post'** angezeigt. 
   Wenn Sie Ihre Logik-App aufrufen und Eingaben für Ihre Logik-App zur Verarbeitung senden möchten, verwenden Sie diese URL als Aufrufziel.

   ![Speichern der Endpunkt-URL für den Anforderungstrigger](./media/migrate-from-scheduler-to-logic-apps/request-endpoint-url.png)

1. Kopieren und speichern Sie diese Endpunkt-URL, um später eine manuelle Anforderung senden zu können, die Ihre Logik-App auslöst. 

## <a name="start-a-one-time-job"></a>Starten eines einmaligen Auftrags

Um manuell einen einmaligen Auftrag auszuführen oder auslösen, senden Sie einen Aufruf an die Endpunkt-URL für den Anforderungstrigger Ihrer Logik-App. Geben Sie in diesem Aufruf die zu sendende Eingabe oder Nutzlast an, die Sie weiter oben eventuell durch Angeben eines Schemas beschrieben haben. 

Mit der Postman-App können Sie beispielsweise eine POST-Anforderung mit ähnlichen Einstellungen wie in diesem Beispiel erstellen und dann auf **Senden** klicken, um die Anforderung zu erstellen.

| Anforderungsmethode | URL | Body | Header |
|----------------|-----|------|---------| 
| **POST** | <*endpoint-URL*> | **raw** <p>**JSON(application/json)** <p>Geben Sie in das Feld **raw** die Nutzlast an, die in der Anforderung gesendet werden soll. <p>**Hinweis**: Diese Einstellung konfiguriert automatisch die **Header**-Werte. | **Key**: Content-Typ <br>**Wert**: „application/json“
 |||| 

![Senden einer Anforderung zum manuellen Auslösen Ihrer Logik-App](./media/migrate-from-scheduler-to-logic-apps/postman-send-post-request.png)

Nachdem Sie den Aufruf senden, wird die Antwort Ihrer Logik-App auf der Registerkarte **Text** unter dem Optionsfeld **raw** angezeigt. 

<a name="workflow-run-id"></a>

> [!IMPORTANT]
>
> Wenn Sie den Auftrag später abbrechen möchten, wählen Sie die Registerkarte **Header**. Suchen und kopieren Sie den Headerwert **x-ms-workflow-run-id** in der Antwort. 
>
> ![response](./media/migrate-from-scheduler-to-logic-apps/postman-response.png)

## <a name="cancel-a-one-time-job"></a>Abbrechen eines einmaligen Auftrags

Jeder einmalige Auftrag in Logic Apps wird als einzelne Logik-App-Instanz ausgeführt. Um einen einmaligen Auftrag abzubrechen, können Sie [Workflowausführungen – Abbrechen](https://docs.microsoft.com/rest/api/logic/workflowruns/cancel) in der Logic Apps-REST-API verwenden. Geben Sie beim Senden eines Aufrufs an den Trigger die [Workflowausführungs-ID](#workflow-run-id) an.

## <a name="schedule-recurring-jobs"></a>Planen wiederkehrender Aufträge

### <a name="create-your-logic-app"></a>Erstellen Ihrer Logik-App

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) eine leere Logik-App im Logik-App-Designer. 

   Führen Sie die grundlegenden in diesem Artikel aus: [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

1. Geben Sie im Suchfeld den Begriff „Wiederholung“ als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Serie** 

   ![Hinzufügen eines Recurrence-Triggers](./media/migrate-from-scheduler-to-logic-apps/recurrence-trigger.png)

1. Bei Bedarf können Sie einen erweiterten Zeitplan einrichten.

   ![Erweiterter Zeitplan](./media/migrate-from-scheduler-to-logic-apps/recurrence-advanced-schedule.png)

   Weitere Informationen zu erweiterten Planungsoptionen finden Sie unter [Erstellen und Ausführen wiederkehrender Tasks und Workflows mit Azure Logic Apps](../connectors/connectors-native-recurrence.md).

1. Fügen Sie weitere Aktionen hinzu, indem Sie eine Auswahl aus den [über 200 Connectors](../connectors/apis-list.md) treffen. Klicken Sie unter dem Trigger auf **Nächster Schritt**. Suchen Sie nach den gewünschten Aktionen, und wählen Sie sie aus.

   Beispielsweise können Sie eine HTTP-Aktion, die eine Anforderung an eine URL sendet, oder Aktionen einschließen, die mit Storage-Warteschlangen, Service Bus-Warteschlangen oder Service Bus-Themen arbeiten: 

   ![HTTP-Aktion](./media/migrate-from-scheduler-to-logic-apps/recurrence-http-action.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Logik-App.

   ![Speichern Ihrer Logik-App](./media/migrate-from-scheduler-to-logic-apps/save-logic-app.png)

## <a name="advanced-setup"></a>Erweitertes Setup

Im Folgenden werden weitere Möglichkeiten zum Anpassen Ihrer Aufträge vorgestellt.

### <a name="retry-policy"></a>Wiederholungsrichtlinie

Um die Art und Weise zu steuern, mit der eine Aktion bei einem vorübergehenden Fehler in Ihrer Logik-App erneut ausgeführt wird, können Sie die [Wiederholungsrichtlinie](../logic-apps/logic-apps-exception-handling.md#retry-policies) in den Einstellungen für jede Aktion festlegen. Beispiel:

1. Öffnen Sie das Menü der Aktion ( **...** ), und klicken Sie auf **Einstellungen**.

   ![Öffnen der Aktionseinstellungen](./media/migrate-from-scheduler-to-logic-apps/action-settings.png)

1. Wählen Sie die gewünschte Wiederholungsrichtlinie aus. Weitere Informationen zu den einzelnen Richtlinien finden Sie unter [Wiederholungsrichtlinien](../logic-apps/logic-apps-exception-handling.md#retry-policies).

   ![Auswählen der Wiederholungsrichtlinie](./media/migrate-from-scheduler-to-logic-apps/retry-policy.png)

## <a name="handle-exceptions-and-errors"></a>Behandeln von Ausnahmen und Fehlern

Wenn bei der Ausführung der Standardaktion ein Fehler in Azure Scheduler auftritt, können Sie eine alterative Aktion ausführen, die die Fehlerbedingung behandelt. In Azure Logic Apps können Sie diese Aufgabe ebenfalls ausführen.

1. Bewegen Sie im Logik-App-Designer oberhalb der Aktion, die verarbeitet werden soll, den Zeiger über den Pfeil zwischen den Schritten, und klicken Sie auf **Parallelen Branch hinzufügen**. 

   ![Hinzufügen einer parallelen Verzweigung](./media/migrate-from-scheduler-to-logic-apps/add-parallel-branch.png)

1. Suchen Sie die Aktion, die stattdessen als alternative Aktion ausgeführt werden soll, und wählen Sie sie aus.

   ![Hinzufügen von Parallelaktionen](./media/migrate-from-scheduler-to-logic-apps/add-parallel-action.png)

1. Öffnen Sie in der alternativen Aktion das Menü ( **...** ), und klicken Sie auf **Ausführung konfigurieren nach**.

   ![Ausführung konfigurieren nach](./media/migrate-from-scheduler-to-logic-apps/configure-run-after.png)

1. Deaktivieren Sie das Kontrollkästchen der Eigenschaft **ist erfolgreich**. Wählen Sie die folgenden Eigenschaften aus: **ist fehlerhaft**, **wird übersprungen** und **Timeout aufgetreten**.

   ![Einrichten von runAfter-Eigenschaften](./media/migrate-from-scheduler-to-logic-apps/select-run-after-properties.png)

1. Klicken Sie auf **Fertig**, wenn Sie fertig sind.

Weitere Informationen zur Behandlung von Ausnahmen finden Sie unter [Abfangen und Behandeln von Fehlern mit der runAfter-Eigenschaft](../logic-apps/logic-apps-exception-handling.md#catch-and-handle-failures-with-the-runafter-property).

## <a name="faq"></a>Häufig gestellte Fragen

<a name="retire-date"></a> 

**F:** Wann wird Azure Scheduler eingestellt? <br>
**A:** Azure Scheduler wird voraussichtlich am 30. September 2019 eingestellt.

**F:** Was geschieht mit meinen Scheduler-Auftragssammlungen und -Aufträgen, nachdem der Dienst eingestellt wird? <br>
**A:** Alle Scheduler-Auftragssammlungen und -Aufträge werden aus dem System gelöscht.

**F:** Muss ich vor der Migration meiner Scheduler-Aufträge zu Logic Apps Sicherungen bzw. sonstige Aufgaben durchführen? <br>
**A:** Wir empfehlen Ihnen, immer Ihre Arbeiten zu sichern. Stellen Sie sicher, dass die von Ihnen erstellten Logik-Apps wie erwartet ausgeführt werden, bevor Sie Ihre Scheduler-Aufträge löschen oder deaktivieren. 

**F:** Gibt es ein Tool, mit dem ich meine Aufträge von Scheduler zu Logic Apps migrieren kann? <br>
**A:** Jeder Scheduler-Auftrag ist einzigartig. Ein universell gültiges Tool gibt es nicht. Allerdings werden verschiedene Skripts zur Verfügung gestellt, die Sie entsprechend Ihrer Anforderungen ändern können. Schauen Sie in regelmäßigen Abständen vorbei, um die verfügbaren Skripts einzusehen.

**F:** Wo erhalte ich Support für die Migration meiner Scheduler-Aufträge? <br>
**A:** Nachfolgend finden Sie einige Ressourcen, über die Sie Support anfordern können: 

**Azure-Portal**

Wenn Ihr Azure-Abonnement mit einem kostenpflichtigen Supportplan ausgestattet ist, können Sie eine technische Supportanfrage im Azure-Portal erstellen. Andernfalls können Sie eine andere Supportoption auswählen.

1. Wählen Sie im Hauptmenü des [Azure-Portals](https://portal.azure.com) **Hilfe + Support** aus.

1. Klicken Sie anschließend unter **Support** auf **Neue Supportanfrage**. Geben Sie diese Details für Ihre Anforderung an:

   | Einstellung | Wert |
   |---------|-------|
   | **Problemtyp** | **Technisch** | 
   | **Abonnement** | <*Ihr Azure-Abonnement*> | 
   | **Service** | Klicken Sie unter **Überwachung & Verwaltung** auf **Scheduler**. | 
   ||| 

1. Wählen Sie die gewünschte Supportoption aus. Wenn Sie über einen kostenpflichtigen Supportplan verfügen, klicken Sie auf **Weiter**.

**Community**

* [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-scheduler)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen und Ausführen von wiederkehrenden Aufgaben und Workflows mit Azure Logic Apps](../connectors/connectors-native-recurrence.md)
* [Tutorial: Überprüfen des Datenverkehrs mit einer zeitplanbasierten Logik-App](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)