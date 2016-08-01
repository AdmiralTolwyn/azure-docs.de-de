<properties 
	pageTitle="Erstellen von Webdienst-Endpunkten in Machine Learning | Microsoft Azure" 
	description="Erstellen von Webdienst-Endpunkten in Azure Machine Learning" 
	services="machine-learning" 
	documentationCenter="" 
	authors="hiteshmadan" 
	manager="padou" 
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd" 
	ms.date="07/06/2016"
	ms.author="himad"/>


# Erstellen von Endpunkten

>[AZURE.NOTE] In diesem Thema werden für einen klassischen Webdienst geltende Verfahren beschrieben.

Mit Azure Machine Learning können Sie mehrere Endpunkte für einen veröffentlichten Webdienst bereitstellen. Jeder Endpunkt kann einzeln behandelt, eingeschränkt und verwaltet werden, unabhängig von den anderen Endpunkten dieses Webdiensts. Für jeden Endpunkt gibt es eine eindeutige URL und einen eindeutigen Autorisierungsschlüssel.

Dadurch können Sie Webdienste erstellen, die Sie dann an Ihre Kunden weiterverkaufen können. Jeder Endpunkt kann mit eigenen trainierten Modellen individuell angepasst werden und weiterhin mit dem Experiment verknüpft bleiben, auf dessen Grundlage dieser Dienst erstellt wurde. Darüber hinaus können alle Aktualisierungen für das Experiment selektiv auf einen Endpunkt angewendet werden, ohne die Anpassungen zu überschreiben.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

## Erstellungsschritte für Endpunkte
- Öffnen Sie [http://manage.windowsazure.com](http://manage.windowsazure.com) und klicken Sie in der linken Spalte auf **Machine Learning**. Klicken Sie auf den Arbeitsbereich mit dem Webdienst, für den Sie sich interessieren.![Navigieren Sie zum Arbeitsbereich](./media/machine-learning-create-endpoint/figure-1.png)

- Klicken Sie auf die Registerkarte **Webdienste**.![Navigieren Sie zum Webdienst](./media/machine-learning-create-endpoint/figure-2.png)

- Klicken Sie auf den Webdienst, für den Sie sich interessieren, um die Liste der verfügbaren Endpunkte anzuzeigen.![Navigieren Sie zum Endpunkt](./media/machine-learning-create-endpoint/figure-3.png)

- Klicken Sie am unteren Rand auf die Schaltfläche **Add Endpoint**. Geben Sie einen Namen und eine Beschreibung ein, und stellen Sie sicher, dass keine anderen Endpunkte mit demselben Namen in diesem Webdienst vorhanden sind. Behalten Sie die Standardeinstellung des Drosselunggrads bei, sofern keine besonderen Anforderungen bestehen. Wenn Sie mehr über Drosselung erfahren möchten, besuchen Sie die Seite [Skalieren von API-Endpunkten](machine-learning-scaling-endpoints.md). ![Endpunkt erstellen](./media/machine-learning-create-endpoint/figure-4.png)

Nach dem Erstellen kann der Endpunkt über synchrone APIs, Batch-APIs und Excel-Arbeitsblätter genutzt werden. Zusätzlich zum Hinzufügen von Endpunkten über diese Benutzeroberfläche können Sie auch die Endpunktverwaltungs-APIs verwenden, um Endpunkte programmgesteuert hinzuzufügen. Weitere Informationen zum Zugreifen auf einen Machine Learning-Webdienst finden Sie unter [Nutzen eines veröffentlichten Azure Machine Learning-Webdiensts](machine-learning-consume-web-services.md).
 
 Beachten Sie, dass Sie den Standardendpunkt weder mit dem Studio noch hier löschen können, wenn Sie Endpunkte hinzugefügt haben. Dadurch wird ein Fehler ausgelöst. Klicken auf Sie auf „Save“.

<!---HONumber=AcomDC_0720_2016-->