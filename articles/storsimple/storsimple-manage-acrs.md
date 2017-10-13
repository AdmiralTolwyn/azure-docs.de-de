---
title: "Verwalten von Zugriffssteuerungsdatensätzen in StorSimple | Microsoft Docs"
description: "Beschreibt, wie Sie mit Zugriffssteuerungsdatensätzen (Access Control Record, ACR) angeben, welche Hosts Verbindungen mit einem Volume auf dem StorSimple-Gerät herstellen können."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2f1475d8-36a5-4cc4-84b9-adf8a310b60c
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: a87624b5706c1d9b8c2b9926e5580996a89ce984
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-manage-access-control-records"></a>Verwalten von Zugriffssteuerungsdatensätzen mithilfe des StorSimple Manager-Diensts
## <a name="overview"></a>Übersicht
Mit Zugriffssteuerungsdatensätzen (Access Control Record, ACR) können Sie angeben, welche Hosts Verbindungen mit einem Volume auf dem StorSimple-Gerät herstellen können. ACRs sind auf ein bestimmtes Volume festgelegt. Sie enthalten die qualifizierten iSCSI-Namen (IQN) der Hosts. Wenn ein Host versucht, eine Verbindung mit einem Volume herzustellen, überprüft das Gerät den ACR zu diesem Volume anhand des IQN-Namens. Bei einer Übereinstimmung wird eine Verbindung hergestellt. Im Abschnitt mit den Zugriffssteuerungsdatensätzen auf der Seite **Konfigurieren** werden alle Zugriffssteuerungsdatensätze zu den IQNs der Hosts angezeigt.

In diesem Tutorial werden die folgenden häufig durchgeführten ACR-bezogenen Aufgaben erläutert:

* Hinzufügen von Zugriffssteuerungsdatensätzen 
* Bearbeiten von Zugriffssteuerungsdatensätzen 
* Löschen von Zugriffssteuerungsdatensätzen 

> [!IMPORTANT]
> * Achten Sie beim Zuweisen eines ACR zu einem Volume darauf, dass dabei nicht gleichzeitig von mehreren nicht gruppierten Hosts auf das Volume zugegriffen wird, da dadurch das Volume beschädigt werden könnte. 
> * Stellen Sie beim Löschen eines ACR von einem Volume sicher, dass der entsprechende Host nicht auf das Volume zugreift, da das Löschen zu einer Unterbrechung des Lese-/ Schreibzugriff führen kann.
> 
> 

## <a name="add-an-access-control-record"></a>Hinzufügen von Zugriffssteuerungsdatensätzen
ACRs werden auf der Seite **Konfigurieren** des StorSimple Manager-Diensts hinzugefügt. In der Regel ordnen Sie einem Volume einen ACR zu.

Führen Sie die folgenden Schritte aus, um einen ACR hinzuzufügen:

#### <a name="to-add-an-access-control-record"></a>So fügen Sie einen Zugriffssteuerungsdatensatz hinzu
1. Wählen Sie auf der Startseite des Diensts Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfigurieren** .
2. Geben Sie in der Tabelle unter **Zugriffssteuerungsdatensätze** einen **Namen** für Ihren ACR ein.
3. Geben Sie unter **iSCSI-Initiatorname**den qualifizierten iSCSI-Namen (IQN) des Windows-Hosts an. Gehen Sie zum Abrufen des IQN des Windows Server-Hosts folgendermaßen vor:
   
   * Starten Sie den Microsoft iSCSI-Initiator auf dem Windows-Host.
   * Wählen Sie im Fenster **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Konfiguration** die Zeichenfolge im Feld **Initiatorname** aus, und kopieren Sie sie.
   * Fügen Sie diese Zeichenfolge im klassischen Azure-Portal in das Feld **iSCSI-Initiatorname** der ACR-Tabelle ein.
4. Klicken Sie auf **Speichern** , um den neu erstellten ACR zu speichern. Die tabellarische Auflistung wird mit dem hinzugefügten Eintrag aktualisiert.

## <a name="edit-an-access-control-record"></a>Bearbeiten von Zugriffssteuerungsdatensätzen
Navigieren Sie im klassischen Azure-Portal zur Seite **Konfigurieren** , um ACRs zu bearbeiten. 

> [!NOTE]
> Sie können nur die ACRs ändern, die derzeit nicht verwendet werden. Zum Bearbeiten eines ACR, der einem das momentan verwendeten Volume zugeordnet ist, müssen Sie zunächst das Volume offline schalten.
> 
> 

Führen Sie die folgenden Schritte aus, um einen ACR zu bearbeiten:

#### <a name="to-edit-an-access-control-record"></a>So bearbeiten Sie einen Zugriffssteuerungsdatensatz
1. Wählen Sie auf der Startseite des Diensts Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfigurieren** .
2. Zeigen Sie in der tabellarischen Auflistung der Zugriffssteuerungsdatensätze auf den zu ändernden ACR.
3. Geben Sie einen neuen Namen und/oder IQN für den ACR an.
4. Klicken Sie auf **Speichern** , um den geänderten ACR zu speichern. Die tabellarische Auflistung wird den Änderungen entsprechend aktualisiert.

## <a name="delete-an-access-control-record"></a>Löschen von Zugriffssteuerungsdatensätzen
Navigieren Sie im klassischen Azure-Portal zur Seite **Konfigurieren** , um ACRs zu löschen. 

> [!NOTE]
> Sie können nur die ACRs löschen, die derzeit nicht verwendet werden. Zum Löschen eines ACR, der einem momentan verwendeten Volume zugeordnet ist, müssen Sie zunächst das Volume offline schalten.
> 
> 

Führen Sie die folgenden Schritte aus, um einen Zugriffssteuerungsdatensatz zu löschen.

#### <a name="to-delete-an-access-control-record"></a>So löschen Sie einen Zugriffssteuerungsdatensatz
1. Wählen Sie auf der Startseite des Diensts Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf die Registerkarte **Konfigurieren** .
2. Zeigen Sie in der tabellarischen Auflistung der Zugriffssteuerungsdatensätze (ACRs) auf den zu löschenden ACR.
3. Ein Löschsymbol (**x**) wird in der äußersten rechten Spalte für den ausgewählten ACR angezeigt. Klicken Sie auf das Symbol **x** , um den ACR zu löschen.
4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja** , um den Löschvorgang fortzusetzen. Die tabellarische Auflistung wird mit dem gelöschten Eintrag aktualisiert.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie mehr über das [Verwalten von StorSimple-Volumes](storsimple-manage-volumes.md).
* Erfahren Sie mehr über das [Verwenden Ihres StorSimple-Geräts mithilfe des StorSimple Manager-Diensts](storsimple-manager-service-administration.md).

