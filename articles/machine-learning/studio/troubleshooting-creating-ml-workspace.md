---
title: 'Problembehandlung: Erstellen eines Machine Learning Studio-Arbeitsbereichs und Verbindungsaufbau'
titleSuffix: Azure Machine Learning Studio
description: Dieser Leitfaden bietet Lösungen zu einigen häufig auftretenden Schwierigkeiten beim Einrichten von Azure Machine Learning Studio-Arbeitsbereichen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 3b2e2def075721b457775003e59d5217fd2e61b8
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509793"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Handbuch zur Problembehandlung: Erstellen eines Azure Machine Learning Studio-Arbeitsbereichs und Verbindungsherstellung
Dieser Leitfaden bietet Lösungen zu einigen häufig auftretenden Schwierigkeiten beim Einrichten von Azure Machine Learning Studio-Arbeitsbereichen.



## <a name="workspace-owner"></a>Besitzer des Arbeitsbereichs
Um einen Arbeitsbereich in Machine Learning Studio zu öffnen, müssen Sie bei dem Microsoft-Konto angemeldet sein, das zum Erstellen des Arbeitsbereichs verwendet wurde, oder Sie müssen eine Einladung des Besitzers zur Teilnahme am Arbeitsbereich erhalten haben. Sie können im Azure-Portal den Arbeitsbereich verwalten und haben dort u.a. die Möglichkeit zum Ändern des Besitzers und der Zugriffskonfiguration.

Weitere Informationen zum Verwalten eines Arbeitsbereichs finden Sie unter [Verwalten eines Azure Machine Learning-Arbeitsbereichs].

[Verwalten eines Azure Machine Learning-Arbeitsbereichs]: manage-workspace.md

## <a name="allowed-regions"></a>Zulässige Regionen
Machine Learning ist derzeit in einer begrenzten Anzahl an Regionen verfügbar. Falls Ihr Abonnement keine dieser Regionen beinhaltet, wird eventuell die Fehlermeldung „Sie verfügen über keine Abonnements für die zulässigen Regionen“ angezeigt.

Um anzufordern, dass Ihrem Abonnement eine Region hinzugefügt wird, erstellen Sie eine neue Microsoft-Supportanfrage im Azure-Portal und wählen **Abrechnung** als Problemtyp aus. Folgen Sie dann den Anweisungen, um Ihre Anfrage zu übermitteln.

## <a name="storage-account"></a>Speicherkonto
Der Machine Learning-Dienst benötigt ein Speicherkonto zum Speichern von Daten. Sie können ein bestehendes Speicherkonto verwenden oder ein neues Speicherkonto erstellen, wenn Sie den neuen Machine Learning-Arbeitsbereich einrichten (falls Ihr Kontingent ausreicht, um ein neues Speicherkonto zu erstellen).

Nachdem Sie den neuen Machine Learning-Arbeitsbereich erstellt haben, können Sie sich mit dem Microsoft-Konto in Machine Learning Studio anmelden, das Sie zum Erstellen des Arbeitsbereichs verwendet haben. Wenn die Fehlermeldung "Arbeitsbereich wurde nicht gefunden." (ähnlich wie im folgenden Screenshot) angezeigt wird, führen Sie die folgenden Schritte durch, um Ihre Browsercookies zu löschen.

![Arbeitsbereich nicht gefunden][screen3]

**So löschen Sie Ihre Browsercookies**

1. Falls Sie Internet Explorer verwenden, klicken Sie oben rechts auf **Extras** und anschließend auf **Internetoptionen**.  

![Internetoptionen][screen4]

2. Klicken Sie in der Registerkarte **Allgemein** auf **Löschen...**

![Registerkarte "Allgemein"][screen5]

3. Wählen Sie im Dialogfeld **Browserverlauf löschen** den Eintrag **Cookies und Websitedaten** aus und klicken Sie auf **Löschen**.

![Cookies löschen][screen6]

Starten Sie nach dem Löschen der Cookies den Browser neu, und wechseln Sie anschließend zur Seite [Microsoft Azure Machine Learning](https://studio.azureml.net) . Wenn Sie zur Eingabe von Benutzername und Kennwort aufgefordert werden, geben Sie das Microsoft-Konto ein, das Sie zum Erstellen des Arbeitsbereichs verwendet haben.

## <a name="comments"></a>Kommentare

Es ist unser Ziel, die Machine Learning-Erfahrung so glatt und nahtlos wie möglich zu gestalten. Bitte schreiben Sie uns Ihre Kommentare und Probleme im [Azure Machine Learning-Forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) , um uns zu helfen, den Dienst zu verbessern.

[screen1]:media/troubleshooting-creating-ml-workspace/screen1.png
[screen2]:media/troubleshooting-creating-ml-workspace/screen2.png
[screen3]:media/troubleshooting-creating-ml-workspace/screen3.png
[screen4]:media/troubleshooting-creating-ml-workspace/screen4.png
[screen5]:media/troubleshooting-creating-ml-workspace/screen5.png
[screen6]:media/troubleshooting-creating-ml-workspace/screen6.png
