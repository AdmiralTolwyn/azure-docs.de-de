<properties
	pageTitle="Azure Machine Learning – häufig gestellte Fragen (FAQ) | Microsoft Azure"
	description="Einführung in Azure Machine Learning: häufig gestellte Fragen (FAQ) zu Abrechnung, Funktionen und Einschränkungen von Clouddiensten für die optimierte Vorhersagemodellierung."
	keywords="Einführung in maschinelles Lernen,Vorhersagemodellierung,was ist maschinelles Lernen"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/18/2016"
	ms.author="garye"/>

# Häufig gestellte Fragen zu Azure Machine Learning (FAQ): Abrechnung, Funktionen, Einschränkungen und Support

In diesem Dokument werden häufig gestellte Fragen zu Azure Machine Learning beantwortet. Dabei handelt es sich um einen Clouddienst zum Entwickeln von Vorhersagemodellen und Lösungen zur Operationalisierung über Webdienste. Dazu gehören Fragen zur Verwendung des Diensts, einschließlich Abrechnungsmodell, Funktionen, Einschränkungen und Unterstützung.

## Allgemeine Fragen

**Was ist Azure Machine Learning?**

Azure Machine Learning ist ein vollständig verwalteter Dienst, mit dem Sie Lösungen zu Vorhersageanalyse in der Cloud erstellen, testen, betreiben und verwalten können. Mit einem Browser können Sie sich anmelden, Daten hochladen und sofort mit Experimenten im Bereich Machine Learning beginnen. Dank der Vorhersagemodellierung per Drag & Drop, einer umfangreichen Modulpalette und einer Bibliothek mit Ausgangsvorlagen können Sie gängige Aufgaben im Bereich Machine Learning schnell und einfach ausführen. Weitere Informationen finden Sie auf der Übersichtsseite zu [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/). Eine Einführung in Machine Learning mit wichtigen Begriffen und Konzepten finden Sie unter [Einführung in Azure Machine Learning](machine-learning-what-is-machine-learning.md).


[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

**Was ist Machine Learning Studio?**

Machine Learning Studio ist eine Workbench-Umgebung, auf die Sie über einen Webbrowser zugreifen. Machine Learning Studio enthält eine Palette von Modulen mit einer visuellen Kompositionsoberfläche, in der Sie durchgängige Datenworkflows in Form von Experimenten erstellen können.

Weitere Informationen zu Machine Learning Studio finden Sie unter [Was ist Machine Learning Studio?](machine-learning-what-is-ml-studio.md).

**Was ist der Machine Learning-API-Dienst?**

Mit dem Machine Learning-API-Dienst können Sie Ihre Vorhersagemodelle, z.B. in Machine Learning Studio erstellte Modelle, als skalierbare, fehlertolerante Webdienste bereitstellen. Die vom Machine Learning-API-Dienst erstellten Webdienste sind REST-APIs. Sie bieten eine Schnittstelle für die Kommunikation zwischen externen Anwendungen und Ihren Predictive Analytics-Modellen.

Weitere Informationen finden Sie unter [Verbinden mit einem Machine Learning-Webdienst](machine-learning-connect-to-azure-machine-learning-web-service.md).


## Fragen zur Abrechnung

**Wie funktioniert die Abrechnung von Machine Learning?**

Abrechnungs- und Preisinformationen finden Sie unter [Machine Learning Preise](https://azure.microsoft.com/pricing/details/machine-learning/).

**Gibt es eine kostenlose Testversion von Machine Learning?**

 Wenn Sie sich für die kostenlose Azure-Testversion anmelden, können Sie die Azure-Dienste einen Monat lang ausprobieren. Weitere Informationen zur kostenlosen Azure-Testversion finden Sie in den [häufig gestellten Fragen zur kostenlosen Testversion von Azure](/pricing/free-trial-faq/).

## Fragen zu Machine Learning Studio

### Erstellen von Experimenten

**Gibt es Versionskontrolle oder eine Git-Integration für Experimentdiagramme?**

Nein. In Machine Learning Studio wird aber jede Experimentiteration beibehalten und kann von anderen Benutzern nicht geändert werden. Weitere Informationen finden Sie unter [Verwalten von Experimentiterationen in Machine Learning-Studio](machine-learning-manage-experiment-iterations.md).

### Importieren und Exportieren von Daten für Machine Learning

**Welche Datenquellen unterstützt Machine Learning?**

Daten können auf drei Arten in ein Machine Learning Studio-Experiment geladen werden: durch das Hochladen einer lokalen Datei als Dataset, durch das Verwenden eines Moduls zum Importieren von Daten aus Clouddatendiensten oder durch das Importieren eines Datasets, das für ein anderes Experiment gespeichert wurde. Weitere Informationen zu den unterstützten Dateiformaten finden Sie unter [Importieren von Trainingsdaten in Machine Learning Studio](machine-learning-data-science-import-data.md).


#### <a id="ModuleLimit"></a>Wie groß können DataSets für meine Module sein?

Module in Machine Learning Studio unterstützen in normalen Anwendungsfällen DataSets bis zu einer Größe von 10 GB an dichten numerischen Daten. Wenn ein Modul mehrere Eingaben akzeptiert, entsprechen 10 GB der Summe aller Eingabegrößen. Sie können über Hive- oder Azure SQL-Datenbank-Abfragen oder durch Vorverarbeitung per Lernen nach Anzahl auch Teile größerer DataSets übernehmen.

Die folgenden Typen von Daten können während der Featurenormalisierung in größere DataSets erweitert werden und sind auf weniger als 10 GB beschränkt:

- Platzsparend
- Kategorisch
- Zeichenfolgen
- Binärdaten

Die folgenden Bereiche sind auf DataSets mit einer Größe unter 10 GB beschränkt:

- Empfohlene Module
- SMOTE-Modul
- Skripting-Module: R, Python, SQL
- Module, bei denen die Größe der Ausgabedaten die der Eingabedaten überschreiten kann, z. B. Join oder Feature-Hashing.
- Cross-Validation, Sweep Parameters, Ordinal Regression und One-vs-All Multiclass, wenn eine sehr große Anzahl von Iterationen durchgeführt wird.

Für DataSets größer als einige GB sollten Sie die Daten in Azure Storage oder Azure SQL-Datenbank laden oder HDInsight verwenden, anstatt die Daten direkt aus lokalen Dateien hochzuladen.


####<a id="UploadLimit"></a>Was sind die Limits für Datenuploads?
Laden Sie für DataSets größer als einige GB die Daten in Azure Storage oder Azure SQL-Datenbank, oder verwenden Sie HDInsight, anstatt die Daten direkt aus lokalen Dateien hochzuladen.

**Können Daten von Amazon S3 gelesen werden?**

Wenn Ihre Daten nicht sehr umfangreich sind und Sie diese über eine HTTP-URL verfügbar machen möchten, können Sie das [Reader][reader]-Modul verwenden. Größere Datenmengen sollten Sie zunächst in Azure Storage übertragen und anschließend mit dem [Reader][reader]-Modul in das Experiment übernehmen.
<!--
<SEE CLOUD DS PROCESS>
-->

**Gibt es eine integrierte Imageeingabefunktion?**

Informationen zur Bildeingabefunktion finden Sie in der Referenz unter [Importieren von Bildern][image-reader].

### Module

**Mein Algorithmus, mein Datenformat oder meine Datentransformation ist nicht in Azure Machine Learning Studio enthalten. Welche Optionen habe ich?**

Sie können das [Forum für Benutzer-Feedback](http://go.microsoft.com/fwlink/?LinkId=404231) besuchen, um herauszufinden, welche Featureanfragen wir momentan verfolgen. Stimmen Sie für eine entsprechende Anfrage ab, wenn ein von Ihnen gewünschtes Feature bereits angefordert wurde. Falls das gewünschte Feature nicht vorhanden ist, können Sie eine neue Anfrage erstellen. Sie können den Status Ihrer Anfrage ebenfalls in diesem Forum verfolgen. Wir verfolgen diese Liste regelmäßig und aktualisieren den Verfügbarkeitsstatus von Features häufig. Mit der integrierten Unterstützung für R und Python können außerdem benutzerdefinierte Transformationen nach Bedarf erstellt werden.


**Kann ich meinen vorhandenen Code in Machine Learning Studio verwenden?**

Ja. Sie können Ihren vorhandenen R- oder Python-Code in Machine Learning Studio importieren, zusammen im gleichen Experiment mit den Lernmodulen in Azure Machine Learning ausführen und die Lösung als Webdienst über Azure Machine Learning bereitstellen. Weitere Informationen finden Sie unter [Erweitern Ihres Experiments mit R](machine-learning-extend-your-experiment-with-r.md) und [Ausführen von Python-Machine Learning-Skripts in Azure Machine Learning Studio](machine-learning-execute-python-scripts.md).

**Ist es möglich, z. B. mit [PMML](http://en.wikipedia.org/wiki/Predictive_Model_Markup_Language) ein Modell zu definieren?**

Nein, dies wird nicht unterstützt. Benutzerdefinierter R- und Python-Code kann jedoch zum Definieren eines Moduls verwendet werden.

**Wie viele Module kann ich parallel in meinem Experiment ausführen?**

Sie können parallel bis zu vier Module in einem Experiment ausführen.


### Datenverarbeitung

**Gibt es eine Möglichkeit zum interaktiven Anzeigen von Daten (neben R-Visualisierungen) im Experiment?**

Durch Klicken auf die Ausgabe eines Moduls können Sie die Daten visualisieren und Statistiken abrufen.

**Bei der Vorschau von Ergebnissen oder Daten im Browser ist die Anzahl der Zeilen und Spalten beschränkt. Warum?**

Da die an den Browser übertragenen Daten umfangreich sein können, ist die Datengröße beschränkt, damit Machine Learning Studio nicht verlangsamt wird. Es ist besser, alle Daten herunterzuladen und Excel oder ein anderes Tool zu verwenden, um die gesamten Daten bzw. Ergebnisse zu visualisieren.

### Algorithmen

**Welche vorhandenen Algorithmen werden in Machine Learning Studio unterstützt?**

Machine Learning Studio unterstützt moderne Algorithmen wie z. B. skalierbare Boosted Decision-Strukturen, Bayessche Empfehlungssysteme, tiefe neuronale Netze und die von Microsoft Research entwickelten "Entscheidungsdschungel". Skalierbare Open Source-Pakete für Machine Learning wie z. B. Vowpal Wabbit sind ebenfalls enthalten. Machine Learning Studio unterstützt Algorithmen für Machine Learning für mehrklassige und binäre Klassifizierung, Regression und Clustering. Weitere Informationen finden Sie in der vollständigen Liste der [Machine Learning-Module][machine-learning-modules].

**Wird automatisch der richtige Machine Learning-Algorithmus für meine Daten vorgeschlagen?**

Nein, es gibt jedoch in Machine Learning Studio verschiedene Möglichkeiten zum Vergleichen der Ergebnisse jedes Algorithmus, um den richtigen für das Problem zu ermitteln.

**Gibt es Richtlinien zum Auswählen eines Algorithmus für die bereitgestellten Algorithmen?** Informationen dazu finden Sie unter [Auswählen eines Algorithmus](machine-learning-algorithm-choice.md).

**Wurden die bereitgestellten Algorithmen in R oder Python geschrieben?**

Diese Algorithmen wurden größtenteils in kompilierten Sprachen geschrieben, um eine höhere Leistung zu erzielen.

**Gibt es nähere Informationen zu den Algorithmen?**

Die Dokumentation enthält Informationen zu den Algorithmen. Darin werden auch die Parameter zur Feinabstimmung beschrieben, damit Sie den Algorithmus für Ihre Zwecke optimieren können.

**Gibt es Unterstützung für das Onlinelernen?**

Nein, derzeit wird nur programmgesteuertes erneutes Trainieren unterstützt.

**Können die Ebenen eines Modells für ein neuronales Netz mit dem integrierten Modul visualisiert werden?**

Nr.

**Kann ich eigene Module in C# oder einer anderen Sprache erstellen?**

Derzeit können neue benutzerdefinierte Module nur in R erstellt werden.

### R-Modul

**Welche R-Pakete sind in Machine Learning Studio verfügbar?**

Machine Learning Studio unterstützt bereits über 400 CRAN R-Pakete, und [hier finden Sie die aktuelle Liste](http://az754797.vo.msecnd.net/docs/RPackages.xlsx) aller enthaltenen Pakete. Lesen Sie auch die Anweisungen zum Abrufen dieser Liste unter [Erweitern des Experiments mit R](machine-learning-extend-your-experiment-with-r.md). Falls das gewünschte Paket nicht in der Liste enthalten ist, können Sie den Namen des Pakets im [Forum für Benutzer-Feedback](http://go.microsoft.com/fwlink/?LinkId=404231) hinterlassen.

**Ist es möglich, benutzerdefinierte R-Module zu erstellen?**

Ja. Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten R-Modulen in Azure Machine Learning](machine-learning-custom-r-modules.md).

**Gibt es eine REPL-Umgebung für R?**

Nein, es gibt keine REPL-Umgebung für R in Machine Learning Studio.

### Python-Modul

**Ist es möglich, benutzerdefinierte Python-Module zu erstellen?**

Derzeit nicht. Sie können aber ein oder mehrere Module vom Typ [Python-Skript ausführen][python] verwenden, um das gleiche Ergebnis zu erzielen.

**Gibt es eine REPL-Umgebung für Python?**

Sie können die „Jupyter Notebooks“ in Machine Learning Studio verwenden. Weitere Informationen finden Sie unter [Introducing Jupyter Notebooks in Azure ML Studio](http://blogs.technet.com/b/machinelearning/archive/2015/07/24/introducing-jupyter-notebooks-in-azure-ml-studio.aspx) (Einführung in Jupyter Notebooks in Azure Machine Learning Studio).

## Webdienst

###Programmgesteuertes erneutes Trainieren von Modellen

**Wie kann ich Azure Machine Learning-Modelle programmgesteuert neu trainieren?**

Verwenden Sie die APIs zum erneuten Trainieren. Weitere Informationen finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](machine-learning-retrain-models-programmatically.md). Außerdem ist Beispielcode unter [Microsoft Azure Maching Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Microsoft Azure Maching Learning – Demo für erneutes Trainieren) verfügbar.

### Erstellen

**Kann ich das Modell lokal oder in einer Anwendung ohne Internetverbindung bereitstellen?**

Nr.


**Gibt es eine Grundlatenz, die für alle Webdienste erwartet wird?**

Informationen hierzu finden Sie unter [Einschränkungen für Azure-Abonnements](../azure-subscription-service-limits.md)

### Verwenden Sie

**Wann sollte ich mein Vorhersagemodell als Stapelausführungsdienst oder als Anfrage-Antwort-Dienst ausführen?**

Der Anfrage-Antwort-Dienst (Request Response Service, RRS) ist ein hoch skalierbarer Webdienst mit niedriger Latenz, der eine Schnittstelle für zustandslose Modelle bereitstellt, die in der Experimentumgebung erstellt und bereitgestellt wurden. Der Stapelausführungsdienst (Batch Execution Service, BES) dient zur asynchronen Bewertung eines Stapels von Datensätzen. Die Eingaben für RRS und BES sind einander sehr ähnlich. BES liest im Gegensatz zu RRS einen Block von Einträgen aus einer Vielzahl von Quellen wie z. B. dem Blobdienst und dem Tabellenspeicherdienst in Azure, Azure SQL-Datenbank, HDInsight (Hive-Abfrage) und HTTP-Quellen. Weitere Informationen finden Sie unter [Verwenden von Machine Learning-Webdiensten](machine-learning-consume-web-services.md).

**Wie aktualisiere ich das Modell für den bereitgestellten Webdienst?**

Sie können das Vorhersagemodell für einen bereitgestellten Dienst einfach ändern, indem Sie das Experiment bearbeiten und erneut ausführen, das Sie beim Erstellen und Speichern des trainierten Modells verwendet haben. Sobald die neue Version des trainierten Modells verfügbar ist, werden Sie von Machine Learning Studio gefragt, ob Sie Ihren Webdienst aktualisieren möchten. Ausführliche Informationen zum Aktualisieren eines bereitgestellten Webdiensts finden Sie unter [Bereitstellen von Machine Learning-Webdiensten](machine-learning-publish-a-machine-learning-web-service.md).

Sie können auch die APIs zum erneuten Trainieren verwenden. Weitere Informationen finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](machine-learning-retrain-models-programmatically.md). Außerdem ist Beispielcode unter [Microsoft Azure Maching Learning Retraining Demo](https://azuremlretrain.codeplex.com/) (Microsoft Azure Maching Learning – Demo für erneutes Trainieren) verfügbar.

**Wie überwache ich meinen Webdienst in der Produktionsumgebung?**

Sobald ein Vorhersagemodell bereitgestellt wurde, können Sie es im klassischen Azure-Portal überwachen. Jeder bereitgestellte Dienst hat ein eigenes Dashboard mit Überwachungsinformationen für den jeweiligen Dienst.

**Kann ich die Ausgabe meines RRS/BES an einer Stelle einsehen?**

Für RRS wird das Ergebnis normalerweise in der Antwort des Webdiensts ausgegeben. Sie können es auch in den Azure-Blobspeicher schreiben. Bei BES wird die Ausgabe standardmäßig in ein Blob geschrieben. Sie können die Ausgabe außerdem mit dem Modul zum [Exportieren von Daten][writer] in eine Datenbank oder Tabelle schreiben.

**Können Webdienste nur aus Modellen erstellt werden, die in Machine Learning Studio erstellt wurden?**

Nein. Webdienste können auch direkt aus Jupyter Notebooks und RStudio erstellt werden.

**Wo finde ich Informationen zu Fehlercodes?**

Eine Liste mit Fehlercodes und den dazugehörigen Beschreibungen finden Sie unter [Machine Learning-Modul-Fehlercodes](https://msdn.microsoft.com/library/azure/dn905910.aspx).

## Skalierbarkeit

**Wie skalierbar ist der Webdienst?**

Derzeit wird der Standardendpunkt mit 20 gleichzeitigen RRS-Anforderungen pro Endpunkt bereitgestellt. Sie können dies auf 200 gleichzeitige Anforderungen pro Endpunkt skalieren, und jeder Webdienst kann auf 10.000 Endpunkte pro Webdienst skaliert werden, wie unter [Skalieren von API-Endpunkten](machine-learning-scaling-endpoints.md) beschrieben. Bei BES lässt jeder Endpunkt die gleichzeitige Verarbeitung von 40 Anforderungen zu, und weitere Anforderungen werden in eine Warteschlange eingereiht. Diese Anforderungen in der Warteschlange werden automatisch ausgeführt, wenn die Warteschlange geleert wird.


**Werden R-Aufträge auf die Knoten verteilt?**

Nein.


**Wie viele Daten kann ich für das Training verwenden?**

Module in Machine Learning Studio unterstützen in normalen Anwendungsfällen DataSets bis zu einer Größe von 10 GB an dichten numerischen Daten. Wenn für ein Modul mehr als eine Eingabe verwendet wird, beträgt die Gesamtgröße für alle Eingaben zusammen 10 GB. Sie können über Hive- oder Azure SQL-Datenbank-Abfragen oder per Vorverarbeitung durch Module vom Typ „Lernen nach Anzahl“ ([Datentransformation/Lernen mit Zahlen][counts]) auch Teile größerer Datasets übernehmen.

Die folgenden Typen von Daten können während der Featurenormalisierung in größere DataSets erweitert werden und sind auf weniger als 10 GB beschränkt:

- Mit geringer Dichte
- Kategorisch
- Zeichenfolgen
- Binärdaten

Die folgenden Bereiche sind auf DataSets mit einer Größe unter 10 GB beschränkt:

- Empfohlene Module
- SMOTE-Modul
- Skripting-Module: R, Python, SQL
- Module, bei denen die Größe der Ausgabedaten die der Eingabedaten überschreiten kann, z. B. Join oder Feature-Hashing.
- Cross-Validate, Tune Model Hyperparameters, Ordinal Regression und One-vs-All Multiclass, wenn eine sehr große Anzahl von Iterationen durchgeführt wird.

Für Datasets, die größer als einige GB sind, sollten Sie die Daten in Azure Storage oder Azure SQL-Datenbank laden oder HDInsight verwenden, anstatt die Daten direkt aus einer lokalen Datei hochzuladen.


**Gibt es Vektorgrößenbeschränkungen?**

Zeilen und Spalten sind jeweils auf die .NET-Einschränkung für Ganzzahlen beschränkt: 2.147.483.647.

**Kann die Größe des virtuellen Computers, der zum Ausführen des Webdiensts verwendet wird, angepasst werden?**

Nein.

## Sicherheit und Verfügbarkeit

**Wer hat standardmäßig Zugriff auf den HTTP-Endpunkt für den Webdienst? Wie kann ich den Zugriff auf den Endpunkt einschränken?**

Nach der Bereitstellung eines Webdiensts wird ein Standardendpunkt für den Dienst erstellt. Der Standardendpunkt kann über seinen API-Schlüssel aufgerufen werden. Zusätzliche Endpunkte können im klassischen Azure-Portal oder programmgesteuert mithilfe der APIs der Webdienstverwaltung unter Verwendung ihrer eigenen Schlüssel hinzugefügt werden. Zugriffsschlüssel sind erforderlich, um Aufrufe für den Webdienst durchzuführen. Weitere Informationen finden Sie unter [Verbinden mit einem Machine Learning-Webdienst](machine-learning-connect-to-azure-machine-learning-web-service.md).


**Was passiert, wenn mein Azure-Speicherkonto nicht gefunden werden kann?**

In Machine Learning Studio ist ein vom Benutzer bereitgestelltes Azure-Speicherkonto zum Speichern von temporären Daten beim Ausführen des Workflows erforderlich. Dieses Speicherkonto wird für Machine Learning Studio beim Erstellen des Arbeitsbereichs zur Verfügung gestellt. Wenn das Speicherkonto nach dem Erstellen des Arbeitsbereichs gelöscht wird, funktioniert der Arbeitsbereich nicht mehr, und alle darin enthaltenen Experimente schlagen fehl.

Wenn Sie das Speicherkonto versehentlich löschen, ist die einzige Möglichkeit der Wiederherstellung ein erneutes Erstellen des Speicherkontos mit identischem Namen und in derselben Region. Anschließend muss der Zugriffsschlüssel wieder synchronisiert werden.


**Was geschieht, wenn mein Speicherkonto-Zugriffsschlüssel nicht mehr synchronisiert ist?**

In Machine Learning Studio ist ein vom Benutzer bereitgestelltes Azure-Speicherkonto zum Speichern von temporären Daten beim Ausführen des Workflows erforderlich. Dieses Speicherkonto wird für Machine Learning Studio beim Erstellen des Arbeitsbereichs zur Verfügung gestellt, und die Zugriffsschlüssel werden diesem Arbeitsbereich zugewiesen. Wenn Zugriffsschlüssel nach dem Erstellen des Arbeitsbereichs geändert werden, kann dieser Arbeitsbereich nicht mehr auf das Speicherkonto zugreifen. Er funktioniert nicht mehr, und alle in diesem Arbeitsbereich enthaltenen Experimente schlagen fehl.

Wenn Sie Zugriffsschlüssel für Speicherkonten geändert haben, müssen Sie die Zugriffsschlüssel im Arbeitsbereich über das klassische Azure-Portal neu synchronisieren.


## Azure Marketplace

Weitere Informationen finden Sie unter [FAQ zum Veröffentlichen und Verwenden von Apps im Machine Learning Marketplace](machine-learning-marketplace-faq.md).

## Support und Lernmaterial

**Wo erhalte ich Lernmaterial für Azure Machine Learning?**

Im [Azure Machine Learning Documentation Center](https://azure.microsoft.com/services/machine-learning/) finden Sie Videoanleitungen und Leitfäden. Diese ausführlichen Leitfäden enthalten eine Einführung in die Dienste und beschreiben den Lebenszyklus der Daten vom Import und der Bereinigung der Daten bis hin zur Erstellung von Vorhersagemodellen und der Bereitstellung der Modelle in der Produktionsumgebung mit Azure Machine Learning.

Wir werden dem Machine Learning Center fortlaufend neues Material hinzufügen. Sie können jederzeit zusätzliches Material für das Machine Learning Center im [Forum für Benutzerfeedback](https://windowsazure.uservoice.com/forums/257792-machine-learning) anfordern.

Es werden auch Schulungen unter [Microsoft Virtual Academy](http://www.microsoftvirtualacademy.com/training-courses/getting-started-with-microsoft-azure-machine-learning) angeboten.

**Wo erhalte ich Support für Azure Machine Learning?**

Technischen Support für Azure Machine Learning erhalten Sie, indem Sie den [Azure-Support](/support/options/) besuchen und **Machine Learning** auswählen.

Für Azure Machine Learning gibt es außerdem ein Community-Forum auf MSDN, in dem Sie Fragen zu Azure Machine Learning stellen können. Das Forum wird vom Azure Machine Learning-Team moderiert. [Azure Forum](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) besuchen.


<!-- Module References -->
[image-reader]: https://msdn.microsoft.com/library/azure/893f8c57-1d36-456d-a47b-d29ae67f5d84/
[join]: https://msdn.microsoft.com/library/azure/124865f7-e901-4656-adac-f4cb08248099/
[machine-learning-modules]: https://msdn.microsoft.com/library/azure/6d9e2516-1343-4859-a3dc-9673ccec9edc/
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7A391181-B6A7-4AD4-B82D-E419C0D6522C
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[python]: https://msdn.microsoft.com/library/azure/CDB56F95-7F4C-404D-BDE7-5BB972E6F232
[counts]: https://msdn.microsoft.com/library/azure/dn913056.aspx

<!---HONumber=AcomDC_0420_2016-->