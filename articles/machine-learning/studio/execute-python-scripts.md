---
title: Ausführen von Python-Machine Learning-Skripts | Microsoft Docs
description: Beschreibt die Entwurfsprinzipien, die der Unterstützung für Python-Skripts in Azure Machine Learning zugrunde liegen, sowie die grundlegenden Verwendungsszenarien, Funktionen und Einschränkungen.
keywords: maschinelles Lernen in Python,Pandas,Python Pandas,Python-Skripts,Ausführen von Python-Skripts
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 537839295deb631c3b9811c8d40db8608954e8a1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34835252"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Ausführen von Python-Machine Learning-Skripts in Azure Machine Learning Studio

Dieses Thema beschreibt die Entwurfsprinzipien, die der aktuellen Unterstützung von Python-Skripts in Azure Machine Learning zugrunde liegen. Die wichtigsten Funktionen werden ebenfalls beschrieben. Hierzu zählen Folgende:

- Ausführen grundlegender Verwendungsszenarien
- Bewerten eines Experiments in einem Webdienst
- Unterstützung des Imports von vorhandenem Code
- Exportieren von Visualisierungen
- Durchführen der beaufsichtigten Funktionsauswahl
- Grundlegendes zu bestimmten Einschränkungen

[Python](https://www.python.org/) ist ein unverzichtbares Tool im Werkzeugkasten vieler Datenanalysten. Es bietet Folgendes:

* Eine elegante und prägnante Syntax 
* Plattformübergreifende Unterstützung 
* Eine umfangreiche Sammlung leistungsstarker Bibliotheken und 
* Ausgereifte Entwicklungstools. 

Python wird in allen Phasen eines Workflows genutzt, der im Allgemeinen bei der Machine Learning-Modellierung zur Anwendung kommt:

- Erfassung und Verarbeitung von Daten 
- Funktionserstellung
- Modelltraining 
- Modellüberprüfung
- Bereitstellung der Modelle

Azure Machine Learning Studio unterstützt das Einbetten von Python-Skripts in verschiedene Teile eines Machine Learning-Versuchs und auch deren nahtlose Veröffentlichung als Webdienste in Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Entwurfsprinzipien für Python-Skripts in Machine Learning

Die primäre Schnittstelle für Python in Azure Machine Learning Studio wird durch das Modul [Execute Python Script][execute-python-script] bereitgestellt (siehe Abbildung 1).

![Bild1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Bild2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Abbildung 1. Das **Execute Python Script** -Modul

Das [Execute Python Script][execute-python-script]-Modul in Azure ML Studio akzeptiert genau wie sein R-Äquivalent, das [Execute R Script][execute-r-script]-Modul, bis zu drei Eingaben und erzeugt bis zu zwei Ausgaben (siehe Erläuterungen im folgenden Abschnitt). Der auszuführende Python-Code wird in das Parameterfeld als speziell benannte Einstiegspunktfunktion namens `azureml_main`eingegeben. Im Folgenden sind die wichtigsten Entwurfsprinzipien zur Implementierung dieses Moduls aufgeführt:

1. *Muss für Python-Benutzer idiomatisch sein.* Die meisten Python-Benutzer gestalten ihren Code als Funktionen in Modulen. Dass eine Vielzahl von ausführbaren Anweisungen in ein Modul auf oberster Ebene platziert wird, kommt daher äußerst selten vor. Demzufolge akzeptiert das Skriptfeld auch eine speziell benannte Python-Funktion und nicht nur eine Sequenz von Anweisungen. Die in der Funktion verfügbar gemachten Objekte sind Standardtypen der Python-Bibliothek, z.B. [Pandas](http://pandas.pydata.org/)-Datenrahmen und [NumPy](http://www.numpy.org/)-Arrays.
2. *Erfordert eine hohe Genauigkeit zwischen lokalen und Cloudausführungen.* Das zum Ausführen des Python-Codes verwendete Back-End basiert auf [Anaconda](https://store.continuum.io/cshop/anaconda/), einer weitverbreiteten plattformübergreifenden, wissenschaftlichen Python-Distribution. Sie wird mit knapp 200 der gängigsten Python-Pakete geliefert. Daher können Datenanalysten ihren Code in ihrer lokalen mit Azure Machine Learning kompatiblen Anaconda-Umgebung debuggen und qualifizieren. Sie können anschließend vorhandene Entwicklungsumgebungen wie [IPython](http://ipython.org/) Notebook oder [Python Tools für Visual Studio](http://aka.ms/ptvs) nutzen, um ihren Code als Teil eines Azure ML-Experiments auszuführen. Beim `azureml_main`-Einstiegspunkt handelt es sich um eine einfache Python-Funktion, die ****ohne spezifischen Azure ML-Code und ohne Installation des SDK erstellt werden kann.
3. *Muss nahtlos mit anderen Azure Machine Learning-Modulen einsetzbar sein.* Das [Execute Python Script][execute-python-script]-Modul akzeptiert Azure Machine Learning-Standard-Datasets als Eingabe und Ausgabe. Das zugrunde liegende Framework verbindet auf transparente und effiziente Weise die Azure ML-Laufzeit und die Python-Laufzeit. Python kann daher in Verbindung mit vorhandenen Azure ML-Workflows eingesetzt werden, auch mit solchen, die R- und SQLite-Aufrufe verwenden. Als Ergebnis dessen könnte ein Datenanalyst Workflows erstellen, für die Folgendes gilt:
   * Verwenden von Python und Pandas zur Datenvorbereitung und -bereinigung
   * Übergeben von Daten an eine SQL-Transformation, wobei mehrere Datasets zu Features verknüpft werden
   * Trainieren von Modellen mit Algorithmen in Azure Machine Learning 
   * Evaluieren und Nachverarbeiten der Ergebnisse mit R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Grundlegende Verwendungsszenarien für Python-Skripts in ML

In diesem Abschnitt untersuchen wir einige der grundlegenden Anwendungsmöglichkeiten des [Execute Python Script][execute-python-script]-Moduls. Eingaben in das Python-Modul werden als Pandas-Datenrahmen verfügbar gemacht. Die Funktion muss einen einzigen Pandas-Datenrahmen zurückgeben, der in eine Python-[Sequenz](https://docs.python.org/2/c-api/sequence.html) wie z.B. ein Tupel, eine Liste oder ein NumPy-Array eingebettet ist. Das erste Element der Sequenz wird anschließend am ersten Ausgabeport des Moduls zurückgegeben. Dieses Schema wird in Abbildung 2 dargestellt.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Abbildung 2. Zuordnung von Eingabeports zu Parametern und des Rückgabewerts zum Ausgabeport.

Eine ausführlichere Semantik zur Zuordnung der Eingangsports zu Parametern der `azureml_main` -Funktion wird in Tabelle 1 dargestellt:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabelle 1. Zuordnung von Eingabeports zu Funktionsparametern.

Die Zuordnung zwischen Eingangsports und Funktionsparametern erfolgt nach Position:

- Der erste verbundene Eingangsport wird dem ersten Parameter der Funktion zugeordnet. 
- Der zweite Eingangsport (sofern verbunden) wird dem zweiten Parameter der Funktion zugeordnet.

In *Python for Data Analysis* (O'Reilly, 2012) von W. McKinney finden Sie weitere Informationen zu Python Pandas und wie es zum effektiven und effizienten Verarbeiten von Daten verwendet werden kann. 


## <a name="translation-of-input-and-output-types"></a>Übersetzung von Eingabe- und Ausgabetypen 
Eingabedatasets in Azure ML werden in Datenrahmen in Pandas konvertiert. Ausgabedatenrahmen werden zurück in Azure ML-Datasets konvertiert. Die folgenden Konvertierungen werden durchgeführt:

1. Zeichenfolgen- und numerische Spalten werden unverändert konvertiert, und fehlende Werte in einem Dataset werden in NA-Werte in Pandas konvertiert. Dieselbe Konvertierung erfolgt auf dem Rückweg (NA-Werte in Pandas werden in fehlende Werte in Azure ML konvertiert).
2. Indexvektoren in Pandas werden in Azure ML nicht unterstützt. Alle Eingabedatenrahmen in der Python-Funktion haben stets einen numerischen 64-Bit-Index von 0 bis zur Anzahl der Zeilen minus 1. 
3. Azure ML-Datasets können keine doppelten Spaltennamen sowie Spaltennamen aufweisen, die keine Zeichenfolgen sind. Wenn ein Ausgabedatenrahmen nicht-numerische Spalten enthält, ruft das Framework `str` für die Spaltennamen auf. Ebenso werden alle doppelten Spaltennamen automatisch geändert, um sicherzustellen, dass die Namen eindeutig sind. Das Suffix (2) wird dem ersten Duplikat, das Suffix (3) dem zweiten Duplikat usw. hinzugefügt.


## <a name="operationalizing-python-scripts"></a>Operationalisieren von Python-Skripts

Alle [Execute Python Script][execute-python-script]-Module in einem Bewertungsexperiment werden bei der Veröffentlichung als Webdienst aufgerufen. Abbildung 3 zeigt beispielsweise ein Bewertungsexperiment, das den Code zum Auswerten eines einzelnen Python-Ausdrucks enthält. 

![Bild4](./media/execute-python-scripts/figure3a.png)

![Bild5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Abbildung 3. Webdienst zum Auswerten eines Python-Ausdrucks.

Ein anhand dieses Versuchs erstellter Webdienst:

- verwendet als Eingabe einen Python-Ausdruck (als Zeichenfolge)
- sendet ihn an den Python-Interpreter 
- gibt eine Tabelle mit dem Ausdruck und dem Auswertungsergebnis zurück.


## <a name="importing-existing-python-script-modules"></a>Importieren vorhandener Python-Skriptmodule

Ein häufiges Anwendungsbeispiel für viele Datenanalysten besteht darin, vorhandene Python-Skripts in Azure ML-Versuche zu integrieren. Statt den gesamten Code zu verketten und in ein einziges Skriptfeld einzufügen, akzeptiert das [Execute Python Script][execute-python-script]-Modul eine ZIP-Datei, die Python-Module am dritten Eingabeport enthält. Die Datei wird zur Laufzeit vom Ausführungs-Framework entpackt, und die Inhalte werden dem Bibliothekspfad des Python-Interpreters hinzugefügt. Die `azureml_main` -Einstiegspunktfunktion kann diese Module anschließend direkt importieren.

Stellen Sie sich beispielsweise die Datei "Hello.py" mit einer einfachen "Hello, World"-Funktion vor.

![Bild6](./media/execute-python-scripts/figure4.png)

Abbildung 4. Benutzerdefinierte Funktion in der Datei „Hello.py“.

Als Nächstes erstellen wir die Datei „Hello.zip“, die „Hello.py“ enthält:

![Bild7](./media/execute-python-scripts/figure5.png)

Abbildung 5. ZIP-Datei mit benutzerdefiniertem Python-Code.

Laden Sie die ZIP-Datei als Dataset in Azure Machine Learning Studio hoch. Erstellen und führen Sie anschließend ein Experiment aus, das den Python-Code in der Datei „Hello.zip“ verwendet, indem Sie ihn mit dem dritten Eingabeport des **Execute Python Script**-Moduls verbinden (siehe folgende Abbildung).

![Bild8](./media/execute-python-scripts/figure6a.png)

![Bild9](./media/execute-python-scripts/figure6b.png)

Abbildung 6. Beispielversuch mit benutzerdefiniertem Python-Code, der als ZIP-Datei hochgeladen wird.

Die Modulausgabe zeigt, dass die ZIP-Datei entpackt und die Funktion `print_hello` ausgeführt wurde.
 
![image10](./media/execute-python-scripts/figure7.png)

Abbildung 7. Eine benutzerdefinierte Funktion, die innerhalb des [Execute Python Script][execute-python-script]-Moduls verwendet wird.


## <a name="working-with-visualizations"></a>Arbeiten mit Visualisierungen

Mit MatplotLib erstellte Plots, die im Browser visualisiert werden können, können vom [Execute Python Script][execute-python-script]-Modul zurückgegeben werden. Die Plots werden jedoch nicht, wie bei R, automatisch in Bilder umgeleitet. Daher muss der Benutzer Plots explizit als PNG-Dateien speichern, wenn sie an Azure Machine Learning zurückgegeben werden sollen. 

Um Bilder aus MatplotLib zu generieren, müssen Sie das folgende Verfahren abschließen:

* Ändern Sie das Back-End vom Qt-basierten Standardrenderer in "AGG". 
* Erstellen Sie ein neues Abbildungsobjekt. 
* Rufen Sie die Achse ab, und generieren Sie alle zugehörigen Plots. 
* Speichern Sie die Abbildung als PNG-Datei. 

Dieser Prozess wird in der folgenden Abbildung 8 dargestellt, die mithilfe der „scatter_matrix“-Funktion in Pandas eine Punktdiagramm-Matrix erstellt.

![Bild1v](./media/execute-python-scripts/figure-v1-8.png)

Abbildung 8. Code zum Speichern von MatplotLib-Abbildungen in Bildern.

Abbildung 9 zeigt einen Versuch, bei dem das zuvor gezeigte Skript verwendet wird, um Plots über den zweiten Ausgabeport zurückzugeben.

![Bild2v](./media/execute-python-scripts/figure-v2-9a.png) 

![Bild2v](./media/execute-python-scripts/figure-v2-9b.png) 

Abbildung 9. Visualisieren von Plots, die aus Python-Code generiert wurden.

Es ist möglich, mehrere Abbildungen zurückzugeben, indem Sie sie in verschiedenen Bildern speichern. Die Azure Machine Learning-Laufzeit übernimmt alle Bilder und verkettet sie zur Visualisierung.


## <a name="advanced-examples"></a>Fortgeschrittene Beispiele

Die in Azure Machine Learning installierte Anaconda-Umgebung enthält gängige Pakete wie NumPy, SciPy und Scikits-Learn. Diese Pakete können effektiv für diverse Datenverarbeitungsaufgaben in einer Machine Learning-Pipeline verwendet werden. Als Beispiel veranschaulichen der folgende Versuch und das Skript die Verwendung von Ensemble-Learnern in Scikits-Learn, um Featurewichtigkeitsbewertungen für ein Dataset zu berechnen. Anhand der Ergebnisse kann dann eine überwachte Featureauswahl durchgeführt werden, bevor diese in ein anderes ML-Modell aufgenommen werden.

Dies ist die Python-Funktion zum Berechnen der Wichtigkeitsbewertungen und zum Sortieren der Features anhand der Ergebnisse:

![Bild11](./media/execute-python-scripts/figure8.png)

Abbildung 10. Funktion zum Klassifizieren von Features nach Bewertungen.
  Der folgende Versuch berechnet anschließend die Wichtigkeitsbewertungen der Features und gibt sie im Dataset „Pima Indian Diabetes“ in Azure Machine Learning aus:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Abbildung 11. Versuch zum Klassifizieren von Features im Dataset "Pima Indian Diabetes".

## <a name="limitations"></a>Einschränkungen
Für das [Execute Python Script][execute-python-script]-Modul gelten derzeit folgende Einschränkungen:

1. *Sandbox-Ausführung.* Die Python-Laufzeit befindet sich derzeit im Sandbox-Modus und ermöglicht daher keinen dauerhaften Zugriff auf das Netzwerk oder auf das lokale Dateisystem. Alle lokal gespeicherten Dateien sind isoliert und werden nach Abschluss des Moduls gelöscht. Auf dem Computer, auf dem er ausgeführt wird, kann der Python-Code nur auf das aktuelle Verzeichnis und die zugehörigen Unterverzeichnisse zugreifen.
2. *Keine differenzierte Unterstützung für Entwicklung und Debuggen.* IDE-Features wie Intellisense und Debuggen werden vom Python-Modul derzeit nicht unterstützt. Sollte zur Laufzeit ein Fehler des Moduls auftreten, ist die komplette Python-Stapelüberwachung verfügbar. Diese muss jedoch im Ausgabeprotokoll für das Modul angezeigt werden. Derzeit wird empfohlen, dass Sie Python-Skripts in einer Umgebung wie IPython entwickeln und debuggen und den Code anschließend in das Modul importieren.
3. *Ausgabe in einem einzelnen Datenrahmen.* Der Python-Einstiegspunkt kann nur einen einzelnen Datenrahmen als Ausgabe zurückgeben. Derzeit ist es nicht möglich, beliebige Python-Objekte wie z. B. trainierte Modelle direkt an die Azure Machine Learning-Laufzeit zurückzugeben. Es gilt zwar die gleiche Einschränkung wie beim [Execute R Script][execute-r-script]-Modul; es ist jedoch in vielen Fällen möglich, Objekte in ein Bytearray einzubetten und dieses innerhalb eines Datenrahmens zurückzugeben.
4. *Keine Möglichkeit zum Anpassen der Python-Installation*. Derzeit besteht die einzige Möglichkeit zum Hinzufügen benutzerdefinierter Python-Module über den weiter oben beschriebenen ZIP-Dateimechanismus. Während dies bei kleinen Modulen machbar ist, ist dieser Ansatz für große Module (vor allem solche mit systemeigenen DLLs) oder eine große Anzahl von Modulen umständlich. 

## <a name="conclusions"></a>Zusammenfassung
Das [Execute Python Script][execute-python-script]-Modul ermöglicht einem Datenanalysten die Einbettung von vorhandenem Python-Code in cloudgehostete Workflows für maschinelles Lernen in Azure Machine Learning sowie deren nahtlose Operationalisierung als Teil eines Webdiensts. Das Python Script-Modul interagiert auf natürliche Weise mit anderen Modulen in Azure Machine Learning. Das Modul kann für eine Vielzahl von Aufgaben von der Datenuntersuchung über die Vorverarbeitung und Featureextraktion bis hin zur anschließenden Auswertung und Nachbearbeitung der Ergebnisse verwendet werden. Die zur Ausführung verwendete Back-End-Laufzeit basiert auf Anaconda, einer umfassende getesteten und häufig verwendeten Python-Distribution. Mit diesem Back-End können Sie vorhandene Codeobjekte ganz einfach in die Cloud integrieren.

Wir werden in Kürze zusätzliche Funktionen für das [Execute Python Script][execute-python-script]-Modul bereitstellen – beispielsweise die Möglichkeit, die Modelle in Python zu trainieren und zu operationalisieren. Außerdem fügen wir eine bessere Unterstützung für das Entwickeln und Debuggen von Code in Azure Machine Learning Studio hinzu.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
