---
title: 'Tutorial: Verwenden eines ONNX-Modells mit Windows ML – Custom Vision Service'
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie eine Windows-UWP-App erstellen, die ein aus Azure Cognitive Services exportiertes ONNX-Modell verwendet.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 10/16/2018
ms.author: larryfr
ms.openlocfilehash: 7f123187ea869931b0701fd9d68372deb0b272e1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55879070"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Tutorial: Verwenden eines ONNX-Modells von Custom Vision mit Windows ML (Vorschauversion)

Es wird beschrieben, wie Sie ein aus Custom Vision exportiertes ONNX-Modell mit Windows ML (Vorschauversion) verwenden.

Anhand der Informationen in diesem Dokument wird veranschaulicht, wie Sie eine ONNX-Datei, die aus Custom Vision Service exportiert wurde, mit Windows ML nutzen. Es wird auch eine Windows-UWP-Beispielanwendung verwendet. Das Beispiel enthält ein für die Erkennung trainiertes Modell. Außerdem sind die Schritte angegeben, wie Sie Ihr eigenes Modell mit diesem Beispiel verwenden können.

> [!div class="checklist"]
> * Informationen zur Beispiel-App
> * Abrufen des Beispielcodes
> * Ausführen des Beispiels
> * Verwenden eines eigenen Modells

## <a name="prerequisites"></a>Voraussetzungen

* Windows 10, Build 17738 oder höher

* Windows SDK für Build 17738 oder höher

* Visual Studio 2017 Version 15.7 oder höher mit aktivierter Workload __Entwicklung für die universelle Windows-Plattform__.

* Aktivierter Entwicklermodus. Weitere Informationen finden Sie im Dokument [Aktivieren Ihres Geräts für die Entwicklung](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Informationen zur Beispiel-App

Die Anwendung ist eine generische Windows-UWP-Anwendung. Sie ermöglicht Ihnen die Auswahl eines Bilds von Ihrem Computer, das dann an das Modell gesendet wird. Die vom Modell zurückgegebenen Tags und Bewertungen werden neben dem Bild angezeigt.

## <a name="get-the-example-code"></a>Abrufen des Beispielcodes

Die Beispielanwendung finden Sie unter [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Ausführen des Beispiels

1. Verwenden Sie die Taste `F5`, um die Anwendung in Visual Studio zu starten. Unter Umständen werden Sie aufgefordert, den Entwicklermodus zu aktivieren. Weitere Informationen finden Sie im Dokument [Aktivieren Ihres Geräts für die Entwicklung](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

2. Wenn die Anwendung startet, verwenden Sie die Schaltfläche, um ein Bild zur Bewertung auszuwählen.

## <a name="use-your-own-model"></a>Verwenden eines eigenen Modells

Führen Sie die folgenden Schritte aus, um Ihr eigenes Modell zu verwenden:

1. [Erstellen und trainieren](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) Sie eine Klassifizierung mit dem Custom Vision Service. Wählen Sie zum Exportieren des Modells eine __compact__-Domäne z.B. **General (Compact)** aus. Zum Exportieren einer vorhandenen Klassifizierung konvertieren Sie die Domäne durch Auswählen des Zahnradsymbols rechts oben zu „compact“. Wählen Sie in __Einstellungen__ ein compact-Modell, und speichern und trainieren Sie Ihr Projekt.  

1. [Exportieren Sie Ihr Modell](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model) auf der Registerkarte „Leistung“. Wählen Sie eine Iteration aus, die mit einer compact-Domäne trainiert wurde, und die Schaltfläche „Exportieren“ wird angezeigt. Wählen Sie *Exportieren*, *ONNX*, *ONNX1.2* und dann *Exportieren* aus. Sobald die Datei bereit ist, wählen Sie die Schaltfläche *Herunterladen*.

1. Legen Sie die ONNX-Datei im Ordner __Ressourcen__ Ihres Projekts ab. 

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste in den Ordner „Ressourcen“, und wählen Sie __Vorhandenes Element hinzufügen__ aus. Wählen Sie die ONNX-Datei aus.

1. Wählen Sie im Projektmappen-Explorer die ONNX-Datei im Ordner „Ressourcen“ aus. Ändern Sie die folgenden Eigenschaften für die Datei: 
    
    * __Buildvorgang__ -> __Inhalt__
    * __In Ausgabeverzeichnis kopieren__ -> __Kopieren, wenn neuer__

1. Ändern Sie die `_onnxFileNames`-Variable in den Namen der ONNX-Datei. Ändern Sie auch `ClassLabel` in die Anzahl der Bezeichnungen, die das Modell enthält.

1. Erstellen Sie das Modell dann, und führen Sie es aus.

1. Klicken Sie auf die Schaltfläche, um ein Bild für die Auswertung auszuwählen.

## <a name="next-steps"></a>Nächste Schritte

Weitere Möglichkeiten zum Exportieren und Verwenden eines Custom Vision-Modells finden Sie in den folgenden Dokumenten:

* [Exportieren Ihres Modells](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Use exported Tensorflow model in an Android application](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample) (Verwenden des exportierten Tensorflow-Modells in einer Android-Anwendung)
* [Use exported CoreML model in a Swift iOS application](https://go.microsoft.com/fwlink/?linkid=857726) (Verwenden des exportierten CoreML-Modells in einer Swift iOS-Anwendung)
* [Use exported CoreML model in an iOS application with Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel) (Verwenden des exportierten CoreML-Modells in einer iOS-Anwendung mit Xamarin)

Weitere Informationen zur Verwendung von ONNX-Modellen mit Windows ML finden Sie im Dokument [Integrieren eines Modells in Ihre App mit Windows ML](https://docs.microsoft.com/windows/uwp/machine-learning/integrate-model).
