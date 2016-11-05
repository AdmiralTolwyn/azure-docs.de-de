---
title: Erstellen von benutzerdefinierten Artefakten für Ihre DevTest Labs-VM | Microsoft Docs
description: Erfahren Sie, wie Sie Ihre eigenen Artefakte für die Verwendung mit DevTest Labs erstellen.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2016
ms.author: tarcher

---
# Erstellen von benutzerdefinierten Artefakten für Ihre DevTest Labs-VM
> [AZURE.VIDEO how-to-author-custom-artifacts] 
> 
> 

## Übersicht
**Artefakte** werden zum Bereitstellen und Konfigurieren Ihrer Anwendung nach der Bereitstellung eines virtuellen Computers verwendet. Ein Artefakt umfasst eine Artefaktdefinitionsdatei und andere Skriptdateien, die in einem Ordner in einem Git-Repository gespeichert sind. Artefaktdefinitionsdateien bestehen aus JSON und Ausdrücken, mit denen Sie angeben können, was Sie auf einem virtuellen Computer installieren möchten. Beispielsweise können Sie den Namen des Artefakts und den auszuführenden Befehl definieren sowie Parameter, die beim Ausführen des Befehls verfügbar gemacht werden. Sie können auf andere Skriptdateien innerhalb der Artefaktdefinitionsdatei anhand ihres Namens verweisen.

## Format von Artefaktdefinitionsdateien
Das folgende Beispiel zeigt die Abschnitte, die die grundlegende Struktur einer Definitionsdatei bilden.

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Elementname | Erforderlich | Beschreibung |
| --- | --- | --- |
| $schema |Nein |Speicherort der JSON-Schemadatei, die beim Testen der Gültigkeit der Definitionsdatei hilft. |
| title |Ja |Der Name des im Lab angezeigten Artefakts. |
| description |Ja |Die Beschreibung des im Lab angezeigten Artefakts. |
| iconUri |Nein |Der URI des im Lab angezeigten Symbols. |
| targetOsType |Ja |Das Betriebssystem des virtuellen Computers, auf dem das Artefakt installiert werden soll. Unterstützte Optionen sind: „Windows“ und „Linux“. |
| parameters |Nein |Werte, die bereitgestellt werden, wenn der Artefaktinstallationsbefehl auf einem Computer ausgeführt wird. Dies hilft beim Anpassen Ihres Artefakts. |
| runCommand |Ja |Artefaktinstallationsbefehl, der auf einem virtuellen Computer ausgeführt wird. |

### Artefaktparameter
Im Parameterabschnitt der Vorlage geben Sie an, welche Werte ein Benutzer beim Installieren eines Artefakts eingeben kann. Auf diese Werte können Sie im Artefaktinstallationsbefehl verweisen.

Sie definieren Parameter mit der folgenden Struktur.

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Elementname | Erforderlich | Beschreibung |
| --- | --- | --- |
| Typ |Ja |Der Typ des Parameterwerts. Die nachstehende Liste zeigt die zulässigen Typen: |
| displayName Ja |Der Name des Parameters, der einem Benutzer im Labor angezeigt wird. | |
| description |Ja |Die Beschreibung des Parameters, der im Labor angezeigt wird. |

Folgende Typen sind zulässig:

* „string“: eine beliebige gültige JSON-Zeichenfolge
* „int“: eine gültige JSON-Ganzzahl
* „bool“: ein gültiger boolescher JSON-Wert
* „array“: ein gültiges JSON-Array

## Ausdrücke und Funktionen für Artefakte
Sie können zum Erstellen des Artefaktinstallationsbefehls Ausdrücke und Funktionen verwenden. Ausdrücke werden in Klammern eingeschlossen ([ und ]) und beim Installieren des Artefakts ausgewertet. Ausdrücke können an beliebiger Stelle in einem JSON-Zeichenfolgenwert auftreten und geben immer einen anderen JSON-Wert zurück. Wenn Sie ein Zeichenfolgenliteral verwenden müssen, das mit einer eckigen Klammer [ beginnt, müssen Sie zwei eckige Klammern verwenden [[. In der Regel verwenden Sie Ausdrücke mit Funktionen, um einen Wert zu erstellen. Genau wie in JavaScript haben Funktionsaufrufe das Format „functionName(arg1,arg2,arg3)“.

Die folgende Liste zeigt häufig verwendete Funktionen.

* parameters(Parametername): gibt einen Parameterwert zurück, der beim Ausführen des Artefaktbefehls bereitgestellt wird.
* concat(arg1,arg2,arg3, ...): kombiniert mehrere Zeichenfolgenwerte. Diese Funktion kann eine beliebige Anzahl an Argumenten entgegennehmen.

Das folgende Beispiel zeigt, wie Sie mit Ausdrücken und Funktionen einen Wert erstellen.

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## Erstellen eines benutzerdefinierten Artefakts
Erstellen Sie Ihr benutzerdefiniertes Artefakt anhand der nachstehend aufgeführten Schritte:

1. Installieren eines JSON-Editors: Sie benötigen zum Bearbeiten von Artefaktdefinitionsdateien einen JSON-Editor. Empfehlenswert ist [Visual Studio Code](https://code.visualstudio.com/), der für Windows, Linux und OS X verfügbar ist.
2. Abrufen einer Beispieldatei „artifactfile.json“: Sehen Sie sich die vom Azure DevTest Labs-Team erstellten Artefakte in unserem [GitHub-Repository](https://github.com/Azure/azure-devtestlab) (in englischer Sprache) an, in dem wir eine umfangreiche Bibliothek von Artefakten erstellt haben, die Ihnen beim Erstellen eigener Artefakte helfen. Laden Sie eine Artefaktdefinitionsdatei herunter, und nehmen Sie an dieser Änderungen vor, um eigene Artefakte zu erstellen.
3. Nutzen von IntelliSense: Zeigen Sie mithilfe von IntelliSense gültige Elemente an, die zum Erstellen einer Artefaktdefinitionsdatei verwendet werden können. Sie können auch die verschiedenen Optionen für die Werte eines Elements sehen. Beispielsweise zeigt IntelliSense beim Bearbeiten des **TargetOsType**-Elements die beiden Wahlmöglichkeiten Windows und Linux an.
4. Speichern des Artefakts in einem Git-Repository
   
   1. Erstellen Sie ein separates Verzeichnis für jedes Artefakt, wobei der Verzeichnisname mit dem Artefaktnamen identisch ist.
   2. Speichern Sie die Artefaktdefinitionsdatei („artifactfile.json“) in dem erstellten Verzeichnis.
   3. Speichern Sie die Skripts, auf die im Artefaktinstallationsbefehl verwiesen wird.
      
      Ein Beispiel für den möglichen Inhalt eines Artefaktordners:
      
      ![Beispiel-Git-Repository für Artefakte](./media/devtest-lab-artifact-author/git-repo.png)
5. Fügen Sie das Artefaktrepository dem Lab hinzu. Weitere Informationen hierzu finden Sie im Artikel [Hinzufügen eines Git-Artefaktrepositorys zu einem Lab](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Verwandte Blogbeiträge
* [How to troubleshoot failing Artifacts in AzureDevTestLabs (Gewusst wie: Problembehandlung für fehlerhafte Artefakte in AzureDevTestLabs)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)
* [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab (Hinzufügen eines virtuellen Computers zu einer vorhandenen AD-Domäne mithilfe einer ARM-Vorlage in Azure Dev Test Lab)](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## Nächste Schritte
* Erfahren Sie, wie Sie einem [ein Git-Artefaktrepository zu einem Lab hinzufügen](devtest-lab-add-artifact-repo.md).

<!---HONumber=AcomDC_0831_2016-->