---
title: "Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace und Visual Studio | Microsoft Docs"
description: "Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace und Visual Studio"
services: visual-studio-online
documentationcenter: n/a
author: TomArcher
manager: douge
editor: 
ms.assetid: 5e6662fc-b917-43ea-bf2b-4f2fc3d213dc
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 8a7b2f3053be09e8cb6e768796b6969eba725885


---
# <a name="debugging-a-published-cloud-service-with-intellitrace-and-visual-studio"></a>Debuggen eines veröffentlichten Clouddiensts mit IntelliTrace und Visual Studio
## <a name="overview"></a>Übersicht
Mit IntelliTrace können Sie umfangreiche Debuginformationen für eine Rolleninstanz protokollieren, wenn sie in Azure ausgeführt wird. Wenn Sie die Ursache eines Problems finden möchten, können Sie mithilfe der IntelliTrace-Protokolle den Code aus Visual Studio schrittweise so durchlaufen, als ob er in Azure ausgeführt würde. Tatsächlich zeichnet IntelliTrace während der Ausführung der Azure-Anwendung als Clouddienst in Azure wichtige Codeausführungs- und Umgebungsdaten auf und ermöglicht die Wiedergabe der aufgezeichneten Daten von Visual Studio aus. Alternativ dazu können Sie das Remotedebugging verwenden, das direkt an einen in Azure ausgeführten Clouddienst angefügt wird. Siehe [Debuggen von Clouddiensten](http://go.microsoft.com/fwlink/p/?LinkId=623041).

> [!IMPORTANT]
> IntelliTrace ist nur für Debugszenarien bestimmt und sollte nicht für eine Produktionsbereitstellung verwendet werden.
> 
> [!NOTE]
> Sie können IntelliTrace verwenden, wenn Visual Studio Enterprise installiert ist und die Azure-Anwendung .NET Framework 4 oder höher als Zielversion verwendet. IntelliTrace sammelt Informationen für Ihre Azure-Rollen. Die virtuellen Computer für diese Rollen führen immer 64-Bit-Betriebssysteme aus.
> 
> 

## <a name="to-configure-an-azure-application-for-intellitrace"></a>So konfigurieren Sie eine Azure-Anwendung für IntelliTrace
Wenn Sie IntelliTrace für eine Azure-Anwendung aktivieren möchten, müssen Sie die Anwendung in einem Visual Studio Azure-Projekt erstellen und von dort veröffentlichen. Sie müssen IntelliTrace für die Azure-Anwendung konfigurieren, bevor Sie die Anwendung in Azure veröffentlichen. Wenn Sie Ihre Anwendung ohne konfiguriertes IntelliTrace veröffentlichen und später entscheiden, dass Sie IntelliTrace konfigurieren möchten, müssen Sie die Anwendung erneut über Visual Studio veröffentlichen. Weitere Informationen finden Sie unter [Veröffentlichen eines Clouddiensts mit Azure Tools](http://go.microsoft.com/fwlink/p/?LinkId=623012).

1. Wenn Sie bereit sind, die Azure-Anwendung bereitzustellen, überprüfen Sie, ob die Projektbuildziele auf **Debuggen**festgelegt sind.
2. Öffnen Sie im Projektmappen-Explorer das Kontextmenü für das Azure-Projekt, und wählen Sie anschließend **Veröffentlichen**aus.
   
    Der Assistent zur Veröffentlichung einer Azure-Anwendung wird geöffnet.
3. Aktivieren Sie zum Erfassen von IntelliTrace-Protokollen für Ihre Anwendung bei der Veröffentlichung in der Cloud das Kontrollkästchen **IntelliTrace aktivieren** .
   
   > [!NOTE]
   > Sie können entweder IntelliTrace oder Profilerstellung aktivieren, wenn Sie Ihre Azure-Anwendung veröffentlichen. Sie können nicht beide Verfahren aktivieren.
   > 
   > 
4. Wenn Sie die IntelliTrace-Basiskonfiguration anpassen möchten, klicken Sie auf den Hyperlink **Einstellungen** .
   
    Das Dialogfeld mit den IntelliTrace-Einstellungen wird angezeigt, wie in der folgenden Abbildung zu sehen. Sie können angeben, welche Ereignisse protokolliert werden sollen, ob Aufrufinformationen erfasst werden sollen, für welche Module und Prozesse Protokolle erfasst werden sollen und wie viel Speicherplatz der Erfassung zugeordnet werden soll. Weitere Informationen zu IntelliTrace finden Sie unter [Debuggen mit IntelliTrace](http://go.microsoft.com/fwlink/?LinkId=214468).
   
    ![VST_IntelliTraceSettings](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC519063.png)

Das IntelliTrace-Protokoll ist eine zirkuläre Protokolldatei, welche die in den IntelliTrace-Einstellungen angegebene maximale Größe besitzt (die Standardgröße beträgt 250 MB). IntelliTrace-Protokolle werden in einer Datei im Dateisystem des virtuellen Computers gesammelt. Wenn Sie die Protokolle anfordern, wird zu diesem Zeitpunkt eine Momentaufnahme erstellt und auf den lokalen Computer heruntergeladen.

Nachdem die Azure-Anwendung in Azure veröffentlicht wurde, können Sie über den Azure-Computerknoten im Server-Explorer ermitteln, ob IntelliTrace aktiviert wurde, wie in der folgenden Abbildung gezeigt:

![VST_DeployComputeNode](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC744134.png)

## <a name="downloading-intellitrace-logs-for-a-role-instance"></a>Herunterladen von IntelliTrace-Protokollen für eine Rolleninstanz
Sie können IntelliTrace-Protokolle für eine Rolleninstanz im **Server-Explorer** aus dem Knoten **Cloud Services** herunterladen. Erweitern Sie den Knoten **Cloud Services**, bis Sie die gewünschte Instanz finden, öffnen Sie das Kontextmenü für diese Instanz, und wählen Sie dann **IntelliTrace-Protokolle anzeigen** aus. Die IntelliTrace-Protokolle werden in eine Datei in einem Verzeichnis auf dem lokalen Computer heruntergeladen. Bei jeder Anforderung der IntelliTrace-Protokolle wird eine neue Momentaufnahme erstellt.

Wenn die Protokolle heruntergeladen werden, zeigt Visual Studio den Status des Vorgangs im Fenster "Azure-Aktivitätsprotokoll" an. Wie in der folgenden Abbildung gezeigt, können Sie die Zeile des Vorgangs erweitern, um weitere Details anzuzeigen.

![VST_IntelliTraceDownloadProgress](./media/vs-azure-tools-intellitrace-debug-published-cloud-services/IC745551.png)

Sie können weiterhin in Visual Studio arbeiten, während die IntelliTrace-Protokolle heruntergeladen werden. Wenn das Protokoll vollständig heruntergeladen wurde, wird es automatisch in Visual Studio geöffnet.

> [!NOTE]
> Die IntelliTrace-Protokolle enthalten möglicherweise vom Framework generierte und anschließend verarbeitete Ausnahmen. Der interne Frameworkcode generiert diese Ausnahmen ganz normal im Rahmen des Starts einer Rolle, sodass sie gefahrlos ignoriert werden können.
> 
> 

## <a name="see-also"></a>Weitere Informationen
[Debuggen von Cloud-Diensten.](https://msdn.microsoft.com/library/ee405479.aspx)




<!--HONumber=Nov16_HO3-->


