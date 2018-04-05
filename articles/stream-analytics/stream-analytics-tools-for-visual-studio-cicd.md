---
title: Verwenden von Continuous Integration und Continuous Deployment mithilfe der Stream Analytics Visual Studio-Tools | Microsoft-Dokumentation
description: Tutorial zum Einrichten von Continuous Integration und Continuous Deployment mithilfe der Stream Analytics Visual Studio-Tools
keywords: Visual Studio, NuGet, DevOps, CI/CD
documentationcenter: ''
services: stream-analytics
author: su-jie
manager: ''
ms.assetid: a473ea0a-3eaa-4e5b-aaa1-fec7e9069f20
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 9/27/2017
ms.author: sujie
ms.openlocfilehash: 14bb15f19b517b55281959f0de970e3f5e0d360b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2018
---
# <a name="use-stream-analytics-visual-studio-tools-to-set-up-a-continuous-integration-and-deployment-process"></a>Einrichten von Continuous Integration und Continuous Deployment mithilfe der Stream Analytics Visual Studio-Tools
In diesem Tutorial erfahren Sie, wie Sie Continuous Integration und Continuous Deployment mithilfe der Azure Stream Analytics Visual Studio-Tools einrichten.

In der neuesten Version (2.3.0000.0 oder höher) der [Stream Analytics-Tools für Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) wurde Unterstützung für MSBuild hinzugefügt.

Es gibt auch ein neu veröffentlichtes NuGet-Paket, [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Es bietet Tools für MSBuild, lokales Ausführen und Bereitstellung zur Unterstützung von Continuous Integration und Continuous Deployment für Stream Analytics Visual Studio-Projekte. 
> [!NOTE] 
Das NuGet-Paket kann nur mit Version 2.3.0000.0 oder höher der Stream Analytics-Tools für Visual Studio verwendet werden. Wenn einige Ihrer Projekte in früheren Versionen von Visual Studio-Tools erstellt wurden, öffnen Sie sie einfach mit Version 2.3.0000.0 oder einer höheren Version, und speichern Sie sie. Dann werden die neuen Funktionen aktiviert. 

Erfahren Sie, wie Sie die [Stream Analytics-Tools für Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) verwenden.

## <a name="msbuild"></a>MSBuild
Wie bei der standardmäßigen Visual Studio MSBuild-Benutzeroberfläche können Sie zwischen zwei Optionen wählen, um ein Projekt zu erstellen. Sie können mit der rechten Maustaste auf das Projekt klicken und dann **Erstellen** wählen. Sie können auch **MSBuild** im NuGet-Paket von der Befehlszeile aus verwenden.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Wenn ein Stream Analytics Visual Studio-Projekt erfolgreich erstellt wurde, generiert es die folgenden beiden Azure Resource Manager-Vorlagendateien im Ordner **bin/[Debug/Retail]/Deploy**: 

*  Resource Manager-Vorlagendatei

       [ProjectName].JobTemplate.json 

*  Resource Manager-Parameterdatei

       [ProjectName].JobTemplate.parameters.json   

Die Standardparameter in der „parameters.json“-Datei werden aus den Einstellungen im Visual Studio-Projekt entnommen. Wenn Sie die Bereitstellung in einer anderen Umgebung ausführen möchten, ersetzen Sie die Parameter entsprechend.

> [!NOTE] 
Für alle Anmeldeinformationen sind die Standardwerte auf NULL festgelegt. Sie *müssen* die Werte festlegen, bevor Sie eine Bereitstellung in der Cloud ausführen.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Erfahren Sie mehr über das [Bereitstellen von Ressourcen mit Azure Resource Manager-Vorlagen und Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Weitere Informationen finden Sie unter [Use an object as a parameter in an Azure Resource Manager template](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters) (Verwenden eines Objekts als Parameter in einer Azure Resource Manager-Vorlage).


## <a name="command-line-tool"></a>Befehlszeilentool

### <a name="build-the-project"></a>Erstellen des Projekts
Im NuGet-Paket befindet sich ein Befehlszeilentool mit dem Namen **SA.exe**. Es unterstützt Projektbuilds und lokale Tests auf beliebigen Computern. Dies können Sie in Ihrem Continuous Integration- und Continuous Deployment-Verfahren nutzen. 

Die Bereitstellungsdateien befinden sich standardmäßig unter dem aktuellen Verzeichnis. Sie können den Ausgabepfad mithilfe des folgenden OutputPath-Parameters angeben:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Lokales Testen des Skripts

Wenn in Ihrem Projekt lokale Eingabedateien in Visual Studio angegeben werden, können Sie mit dem Befehl *localrun* einen automatischen Skripttest ausführen. Das Ausgabeergebnis wird im aktuellen Verzeichnis abgelegt.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Generieren Sie eine Auftragsdefinitionsdatei für die Verwendung mit der Stream Analytics-PowerShell-API.

Der Befehl *arm* nimmt die generierte Auftragsvorlage und die Parameterdateien für die Auftragsvorlage als Eingabe entgegen. Kombinieren Sie diese dann in einer Auftragsdefinition-JSON-Datei, die mit der Stream Analytics-PowerShell-API verwendet werden kann.

```
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Beispiel:
```
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


