---
title: Excel-Add-In für Machine Learning-Webdienste | Microsoft Docs
description: Hier wird erläutert, wie Sie Azure Machine Learning-Webdienste direkt in Excel ohne Erstellung von Code verwenden.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 9618079d-502f-4974-a3e2-8f924042a23f
ms.service: machine-learning
ms.component: studio
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 2/1/2018
ms.openlocfilehash: 68e2f72dfd8cc58d42263f4b6378d89304aaaa4d
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/07/2018
ms.locfileid: "34834191"
---
# <a name="excel-add-in-for-azure-machine-learning-web-services"></a>Excel-Add-In für Azure Machine Learning-Webdienste
Mit Excel können Webdienste auf einfache Weise direkt aufgerufen werden, ohne dafür Code erstellen zu müssen.

## <a name="steps-to-use-an-existing-web-service-in-the-workbook"></a>Schritte zum Verwenden eines vorhandenen Webdiensts in einer Arbeitsmappe

1. Öffnen Sie die [Excel-Beispieldatei](http://aka.ms/amlexcel-sample-2), die das Excel-Add-In und Daten zu den Passagieren auf der Titanic enthält.
2. Wählen Sie den Webdienst aus, indem Sie darauf klicken – in diesem Beispiel ist das „Titanic Survivor Predictor (Excel-Add-In-Beispiel) [Ergebnis]“.
   
    ![Webdienst auswählen][01]
3. Dadurch gelangen Sie zum Abschnitt **Predict**.  Diese Arbeitsmappe enthält bereits Beispieldaten. In einer leeren Arbeitsmappe können Sie jedoch auch eine Zelle in Excel auswählen und auf **Use sample data** klicken.
4. Wählen Sie die Daten mit Überschriften aus, und klicken Sie auf das Symbol für den Eingabedatenbereich.  Stellen Sie sicher, dass das Kontrollkästchen „Daten haben Überschriften“ aktiviert ist.
5. Geben Sie unter **Ausgabe** die Zellennummer ein, in der die Ausgabe erfolgen soll. Bei diesem Beispiel „H1“.
6. Klicken Sie auf **Vorhersagen**.
   
    ![Abschnitt „Predict“][02]

Stellen Sie einen Webdienst bereit, oder verwenden Sie einen vorhandenen Webdienst. Weitere Informationen zum Bereitstellen eines Webdiensts finden Sie unter [Exemplarische Vorgehensweise – Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts](walkthrough-5-publish-web-service.md).

Rufen Sie den API-Schlüssel Ihres Webdiensts ab. Wo diese Aktion durchgeführt wird hängt davon ab, ob Sie einen klassischen oder neuen Machine Learning-Webdienst veröffentlicht haben.

**Verwenden eines klassischen Webdiensts** 

1. Klicken Sie in Machine Learning Studio im linken Bereich auf den Abschnitt **WEB SERVICES** , und wählen Sie den Webdienst aus.
   
    ![Studio – Webdienst auswählen][04]
2. Kopieren Sie den API-Schlüssel für den Webdienst.
   
    ![Studio-API-Schlüssel][05]
3. Klicken Sie auf der Registerkarte **DASHBOARD** für den Webdienst auf die Verknüpfung **ANFORDERUNG/ANTWORT**.
4. Wechseln Sie zum Abschnitt **Request URI** .  Kopieren und speichern Sie die URL.

> [!NOTE]
> Es ist jetzt möglich, sich beim Portal [Azure Machine Learning Web Services](https://services.azureml.net) anzumelden, um den API-Schlüssel für einen klassischen Machine Learning-Webdienst abzurufen.
> 
> 

**Verwenden eines neuen Webdiensts**

1. Klicken Sie im [Azure Machine Learning Web Services](https://services.azureml.net)-Portal auf **Web Services** (Webdienste), und wählen Sie Ihren Webdienst aus. 
2. Klicken Sie auf **Consume**.
3. Wechseln Sie zum Abschnitt **Basic consumption info** . Kopieren und speichern Sie den **Primary Key** und die URL für **Request-Response**.

## <a name="steps-to-add-a-new-web-service"></a>Schritte zum Hinzufügen eines neuen Webdiensts

1. Stellen Sie einen Webdienst bereit, oder verwenden Sie einen vorhandenen Webdienst. Weitere Informationen zum Bereitstellen eines Webdiensts finden Sie unter [Exemplarische Vorgehensweise – Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts](walkthrough-5-publish-web-service.md).
2. Klicken Sie auf **Consume**.
3. Wechseln Sie zum Abschnitt **Basic consumption info** . Kopieren und speichern Sie den **Primary Key** und die URL für **Request-Response**.
4. Navigieren Sie in Excel zum Abschnitt **Web Services** (wenn Sie sich im Abschnitt **Predict** befinden, klicken Sie auf den Zurück-Pfeil, um zur Liste der Webdienste zu gelangen).
   
    ![Webdienstauswahl aufrufen][03]
5. Klicken Sie auf **Webdienst hinzufügen**.
6. Fügen Sie die URL in das Textfeld des Excel-Add-Ins mit der Beschriftung **URL**ein.
7. Fügen Sie die API/den Primärschlüssel in das Textfeld mit der Beschriftung **API key**ein.
8. Klicken Sie auf **Hinzufügen**.
   
    ![URL und API-Schlüssel für einen klassischen Webdienst.][06]
9. Folgen Sie den vorherigen Anweisungen unter „Schritte zum Verwenden eines vorhandenen Webdiensts“, um den Webdienst zu verwenden.

## <a name="sharing-your-workbook"></a>Freigeben Ihrer Arbeitsmappe
Wenn Sie Ihre Arbeitsmappe speichern, werden auch die API/der Primärschlüssel der hinzugefügten Webdienste gespeichert. Das bedeutet, dass Sie die Arbeitsmappe nur für Personen freigeben sollten, denen Sie vertrauen.

Im nachstehenden Kommentarabschnitt oder in unserem [Forum](http://go.microsoft.com/fwlink/?LinkID=403669&clcid=0x409) können Sie Fragen stellen.

[01]: ./media/excel-add-in-for-web-services/image1.png
[02]: ./media/excel-add-in-for-web-services/image2.png
[03]: ./media/excel-add-in-for-web-services/image3.png
[04]: ./media/excel-add-in-for-web-services/image4.png
[05]: ./media/excel-add-in-for-web-services/image5.png
[06]: ./media/excel-add-in-for-web-services/image6.png
