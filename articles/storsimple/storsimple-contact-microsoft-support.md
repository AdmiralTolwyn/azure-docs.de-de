---
title: "Anmelden eines Supporttickets für die StorSimple 8000-Serie | Microsoft-Dokumentation"
description: "Hier erfahren Sie, wie Sie eine Supportanfrage erstellen und eine Supportsitzung auf Ihrem StorSimple-Gerät initiieren."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 2ebc20fe-f490-4749-8e43-c9fac86f1676
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/27/2017
ms.author: alkohli;anbacker
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cecc2566b432e897b5eff0c12e66598f0518ed80
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 07/11/2017
---
# <a name="contact-microsoft-support-for-your-storsimple"></a>Wenden Sie sich an den Microsoft Support für StorSimple
Wenn Probleme mit Ihrer Microsoft Azure StorSimple-Lösung auftreten, können Sie eine Serviceanfrage für den technischen Support erstellen. In einer Onlinesitzung mit dem Supporttechniker müssen Sie darüber hinaus eine Supportsitzung auf Ihrem StorSimple-Gerät starten. In diesem Artikel wird Folgendes beschrieben:

* Erstellen einer Supportanfrage
* Starten einer Supportsitzung in der Windows PowerShell-Schnittstelle Ihres StorSimple-Geräts

Lesen Sie die [Support-SLAs und Informationen für die StorSimple 8000-Serie](https://msdn.microsoft.com/library/mt433077.aspx) , bevor Sie eine Supportanfrage erstellen.

## <a name="create-a-support-request"></a>Erstellen einer Supportanfrage
Führen Sie die folgenden Schritte aus, um eine Supportanfrage zu erstellen.

#### <a name="to-create-a-support-request"></a>So erstellen Sie eine Supportanfrage
1. Klicken Sie oben rechts im [klassischen Azure-Portal](https://manage.windowsazure.com/)auf den Kontonamen, und klicken Sie dann auf **Microsoft-Support kontaktieren**.
   
    ![Kontaktieren des Supports von MS über das Verwaltungsportal](./media/storsimple-contact-microsoft-support/Ibiza1.png)
2. Sie werden zum neuen Azure-Portal (portal.azure.com) weitergeleitet. Klicken Sie auf die Kachel **Neue Supportanfrage** .
   
    ![Kontaktieren des Supports von MS über das neue Portal](./media/storsimple-contact-microsoft-support/Ibiza2.png)
   
    Auf der rechten Seite des Bildschirms wird die **Neue Supportanfrage** angezeigt. 
   
    ![Neue Supportanfrage (Bereich)](./media/storsimple-contact-microsoft-support/Ibiza3a.png)
3. Führen Sie im Dialogfeld **Grundlagen** Folgendes aus:                                
   
   1. Wählen Sie aus der Dropdownliste **Problemtyp** den Eintrag **Technisch** aus.
   2. Wählen Sie aus der Dropdownliste ein **Abonnement** aus.
   3. Wählen Sie aus der Dropdownliste **Dienst** den Eintrag **StorSimple** aus. 
   4. Wählen Sie aus der Dropdownliste einen **Supportplan** aus. Sie benötigen einen kostenpflichtigen Supportplan, um technischen Support zu erhalten.
4. Klicken Sie auf **Weiter**.
 Das Dialogfeld **Problem** wird angezeigt.
   
    ![Neue Supportanfrage (Bereich)](./media/storsimple-contact-microsoft-support/Ibiza5a.png) 
5. Führen Sie im Dialogfeld **Problem** Folgendes aus:
   
   1. Wählen Sie aus der Dropdownliste einen **Schweregrad** aus.
   2. Wählen Sie aus der Dropdownliste einen **Problemtyp** aus.
   3. Wählen Sie aus der Dropdownliste eine **Kategorie** aus. 
   4. Beschreiben Sie Ihr Problem im Feld **Details** .
   5. Geben Sie im Feld **Zeitraum** das Datum, die Uhrzeit und die Zeitzone an, die dem letzten Auftreten von Ihrem Problem entsprechen.
   6. Klicken Sie unter **Dateiupload**auf das Ordnersymbol, um Ihr Supportpaket zu suchen.
   7. Aktivieren Sie das Kontrollkästchen **Diagnoseinformationen freigeben** .
6. Klicken Sie auf **Weiter**.
 Das Dialogfeld **Kontaktinformationen** wird angezeigt.
   
    ![Neue Supportanfrage (Bereich)](./media/storsimple-contact-microsoft-support/Ibiza6a.png) 
7. Geben Sie Ihre Kontaktinformationen ein, und wählen Sie eine Kontaktmethode (Telefon oder E-Mail) aus. 
8. Aktivieren Sie das Kontrollkästchen **Kontaktänderungen für zukünftige Supportanfragen speichern** .
9. Klicken Sie auf **Erstellen**.

Nach dem Übermitteln Ihrer Anfrage setzt sich baldmöglichst ein Supporttechniker mit Ihnen in Verbindung, um Ihre Anfrage zu bearbeiten.

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Starten einer Supportsitzung in Windows PowerShell für StorSimple
Um Probleme zu beheben, die auf Ihrem StorSimple-Gerät unter Umständen auftreten, müssen Sie sich mit dem Supportteam von Microsoft in Verbindung setzen. Der Microsoft-Support muss sich möglicherweise über eine Supportsitzung bei Ihrem Gerät anmelden. 

Führen Sie zum Starten einer Supportsitzung die folgenden Schritte aus:

#### <a name="to-start-a-support-session"></a>So starten Sie eine Supportsitzung
1. Greifen Sie auf das Gerät direkt über die serielle Konsole oder von einem Remotecomputer über eine Telnet-Sitzung zu. Führen Sie hierzu die Schritte unter [Verwenden von PuTTY für das Herstellen einer Verbindung mit der seriellen Gerätekonsole](storsimple-deployment-walkthrough.md#use-putty-to-connect-to-the-device-serial-console)aus.
2. Drücken Sie in der Sitzung, die geöffnet wird, die **EINGABETASTE** , um eine Eingabeaufforderung zu öffnen.
3. Wählen Sie im Menü der seriellen Konsole Option 1 aus, d.h. die **Anmeldung mit Vollzugriff**.
4. Geben Sie an der Eingabeaufforderung das folgende Kennwort ein: 
   
    `Password1`
5. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:
   
    `Enable-HcsSupportAccess`
6. Eine verschlüsselte Zeichenfolge wird angezeigt. Kopieren Sie diese Zeichenfolge in einen Text-Editor wie beispielsweise Notepad.
7. Speichern Sie diese Zeichenfolge, und senden Sie sie in einer E-Mail-Nachricht an den Microsoft-Support. 

> [!IMPORTANT]
> Sie können den Supportzugriff deaktivieren, indem Sie `Disable-HcsSupportAccess` deaktivieren. Das StorSimple-Gerät versucht acht Stunden nach dem Initiieren der Sitzung ebenfalls, den Supportzugriff zu deaktivieren. Es wird empfohlen, die Anmeldeinformationen für das StorSimple-Gerät nach dem Initiieren einer Supportsitzung zu ändern.
> 
> 

