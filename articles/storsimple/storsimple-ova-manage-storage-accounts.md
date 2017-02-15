---
title: Verwalten Ihres StorSimple-Speicherkontos | Microsoft Docs
description: "Beschreibt, wie Sie die Seite „Konfigurieren“ des StorSimple Manager-Diensts zum Hinzufügen, Bearbeiten oder Löschen von Speicherkonten verwenden und wie Sie die Sicherheitsschlüssel für ein Speicherkonto rotieren, das dem StorSimple Virtual Array zugeordnet ist."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: fb6459c7-a4b1-4b69-a000-72edd42e9478
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/29/2016
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: a2419943ab8591e397ad55bd400042436fdf0373


---
# <a name="use-the-storsimple-manager-service-to-manage-storage-accounts-for-storsimple-virtual-array"></a>Verwenden des StorSimple Manager-Diensts zum Verwalten von Speicherkonten für das StorSimple Virtual Array
## <a name="overview"></a>Übersicht
Auf der Seite **Konfigurieren** werden die globalen Dienstparameter angezeigt, die im StorSimple Manager-Dienst erstellt werden können. Diese Parameter können für alle mit dem Dienst verbundenen Geräte angewendet werden und umfassen Folgendes:

* Speicherkonten 
* Zugriffssteuerungsdatensätze 

In diesem Tutorial wird erläutert, wie Sie die Seite **Konfigurieren** zum Hinzufügen, Bearbeiten oder Löschen von Speicherkonten für Ihr StorSimple Virtual Array verwenden. Die Informationen in diesem Tutorial gelten nur für das StorSimple Virtual Array mit der GA-Version der Software aus dem März 2016.

 ![Konfigurieren, Seite](./media/storsimple-ova-manage-storage-accounts/configure_service_page.png)  

Speicherkonten enthalten die Anmeldeinformationen, die das Gerät für den Zugriff auf das Speicherkonto mit dem Clouddienstanbieter nutzt. Für Microsoft Azure-Speicherkonten sind dies die Anmeldeinformationen, wie z. B. Kontoname und primärer Zugriffsschlüssel. 

Auf der Seite **Konfigurieren** werden alle Speicherkonten, die für das Abrechnungsabonnement erstellt werden, in einem Tabellenformat mit den folgenden Informationen angezeigt:

* **Name** – der eindeutige Name für das Konto, der bei dessen Erstellung zugewiesen wurde.
* **SSL enabled** – zeigt an, ob SSL aktiviert ist und die Kommunikation zwischen Gerät und Cloud über einen sicheren Kanal verläuft.

Im Folgenden werden die häufigsten auf der Seite **Konfigurieren** ausgeführten Aufgaben im Zusammenhang mit Speicherkonten aufgeführt:

* Hinzufügen von Speicherkonten 
* Bearbeiten eines Speicherkontos 
* Löschen eines Speicherkontos 

## <a name="types-of-storage-accounts"></a>Speicherkontentypen
Es gibt drei Typen von Speicherkonten, die mit dem StorSimple-Gerät verwendet werden können.

* **Auto-generated storage accounts** – diese Art von Speicherkonto wird bei der ersten Erstellung des Dienstes automatisch generiert. Weitere Informationen zum Erstellen dieses Speicherkontos finden Sie unter [Erstellen eines neuen Diensts](storsimple-ova-manage-service.md#create-a-service). 
* **Storage accounts in the service subscription** – Azure-Speicherkonten, die demselben Abonnement zugeordnet sind wie der Dienst . Weitere Informationen zur Erstellung dieser Speicherkonten finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md). 
* **Storage accounts outside of the service subscription** – Azure-Speicherkonten, die nicht mit dem Dienst verknüpft sind und wahrscheinlich schon vorhanden waren, bevor der Dienst erstellt wurde.

Jedes StorSimple Virtual Array erstellt einen Container (mit dem Präfix „hcs“) im zugeordneten Speicherkonto. Dieser Container enthält alle Clouddaten für Ihr Gerät. Löschen Sie diesen Container nicht, indem Sie über den Azure Storage-Dienst darauf zugreifen, weil dieser Vorgang zu einem Datenverlust führt.

## <a name="add-a-storage-account"></a>Hinzufügen von Speicherkonten
Sie können ein Speicherkonto zu Ihrer Konfiguration für den StorSimple Manager-Dienst hinzufügen, indem Sie einen eindeutigen Anzeigenamen und Anmeldeinformationen für den Zugriff angeben, die mit dem Speicherkonto verknüpft sind. Sie haben außerdem die Möglichkeit, den SSL-Modus (Secure Sockets Layer) zu aktivieren, um einen sicheren Kanal für die Netzwerkkommunikation zwischen dem Gerät und der Cloud zu erstellen.

Sie können mehrere Konten für einen Clouddienstanbieter erstellen. Während das Speicherkonto gespeichert wird, versucht der Dienst mit dem Clouddienstanbieter zu kommunizieren. Die Anmelde- und Zugriffsinformationen, die Sie bereitgestellt haben, werden zu diesem Zeitpunkt authentifiziert. Ein Speicherkonto wird nur erstellt, wenn die Authentifizierung erfolgreich war. Wenn die Authentifizierung fehlschlägt, wird eine entsprechende Fehlermeldung angezeigt.

Resource Manager-Speicherkonten, die im Azure-Portal wurden, werden auch für StorSimple unterstützt. Die Resource Manager-Speicherkonten werden in der Dropdownliste nicht zur Auswahl angezeigt. Nur die im klassischen Azure-Portal erstellten Speicherkonten werden aufgeführt. Resource Manager-Speicherkonten müssen über das Verfahren zum Hinzufügen eines Speicherkontos hinzugefügt werden, wie unten beschrieben.

Das Verfahren zum Hinzufügen eines klassischen Azure-Speicherkontos wird im Folgenden beschrieben.

[!INCLUDE [add-a-storage-account](../../includes/storsimple-ova-configure-new-storage-account.md)]

## <a name="edit-a-storage-account"></a>Bearbeiten eines Speicherkontos
Sie können ein Speicherkonto bearbeiten, das von Ihrem Gerät verwendet wird. Wenn Sie ein derzeit verwendetes Speicherkonto bearbeiten, sind nur die Felder mit dem Zugriffsschlüssel und mit dem SSL-Modus für das Speicherkonto änderbar. Sie können einen neuen Speicherzugriffsschlüssel angeben oder die Auswahl für **SSL-Modus aktivieren** ändern und die aktualisierten Einstellungen speichern.

#### <a name="to-edit-a-storage-account"></a>So bearbeiten Sie ein Speicherkonto
1. Wählen Sie auf der Startseite des Diensts Ihren Dienst aus, doppelklicken Sie auf den Namen und klicken Sie dann auf **Konfigurieren**.
2. Klicken Sie auf **Speicherkonten hinzufügen/bearbeiten**.
3. Gehen Sie im Dialogfeld **Speicherkonten hinzufügen/bearbeiten** folgendermaßen vor:
   
   1. Wählen Sie in der Dropdownliste **Speicherkonten**ein vorhandenes Konto aus, das Sie ändern möchten. 
   2. Ändern Sie bei Bedarf die Auswahl für **SSL-Modus aktivieren**.
   3. Falls gewünscht, können Sie auch die Speicherkonto-Zugriffsschlüssel neu generieren. Weitere Informationen finden Sie unter [Erneutes Generieren von Speicherkontoschlüsseln](../storage/storage-create-storage-account.md#manage-your-storage-access-keys). Geben Sie den neuen Speicherkonto-Zugriffsschlüssel an. Für ein Azure-Speicherkonto ist dies der primäre Zugriffsschlüssel 
   4. Klicken Sie auf das Häkchensymbol ![Häkchensymbol](./media/storsimple-ova-manage-storage-accounts/checkicon.png) zum Speichern der Einstellungen. Die Einstellungen werden auf der Seite **Konfigurieren** aktualisiert. 
   5. Klicken Sie unten auf der Seite auf **Speichern** , um die gerade geänderten Einstellungen zu speichern. 
      
      ![Bearbeiten eines Speicherkontos](./media/storsimple-ova-manage-storage-accounts/modifyexistingstorageaccount.png)

## <a name="delete-a-storage-account"></a>Löschen eines Speicherkontos
> [!IMPORTANT]
> Sie können nur ein nicht verwendetes Speicherkonto löschen. Wenn ein Speicherkonto verwendet wird, werden Sie benachrichtigt.
> 
> 

#### <a name="to-delete-a-storage-account"></a>So löschen Sie ein Speicherkonto
1. Wählen Sie auf der Startseite des StorSimple-Manager-Diensts Ihren Dienst aus, doppelklicken Sie auf den Namen, und klicken Sie dann auf **Konfigurieren**.
2. Zeigen Sie in der tabellarischen Liste der Speicherkonten auf das Konto, das Sie löschen möchten.
3. Ein Löschsymbol (**x**) wird in der äußersten rechten Spalte für das Speicherkonto angezeigt. Klicken Sie auf das Symbol **x** , um die Anmeldeinformationen zu löschen.
4. Wenn Sie zur Bestätigung aufgefordert werden, klicken Sie auf **Ja** , um den Löschvorgang fortzusetzen. Die tabellarische Auflistung wird den Änderungen entsprechend aktualisiert.
5. Klicken Sie unten auf der Seite auf **Speichern** , um die gerade geänderten Einstellungen zu speichern.

## <a name="next-steps"></a>Nächste Schritte
* Erfahren Sie, wie Sie [Ihr StorSimple Virtual Array verwalten](storsimple-ova-web-ui-admin.md).




<!--HONumber=Nov16_HO3-->


