---
title: 'Azure DocumentDB-Portaltool: Skript-Explorer | Microsoft Docs'
description: "Erfahren Sie mehr über den DocumentDB-Skript-Explorer, ein Azure-Portaltool zum Verwalten von serverseitigen DocumentDB-Programmierartefakten, z.B. gespeicherte JavaScript-Prozeduren, Trigger und benutzerdefinierte Funktionen."
keywords: JavaScript-Editor
services: documentdb
author: kirillg
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 9d0620da-2449-4c17-82a4-24aaa46e9b3e
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: kirillg
translationtype: Human Translation
ms.sourcegitcommit: 429687c6e5a196a3b489dc4dd79ae886b7ad9c38
ms.openlocfilehash: ccff673996d53d2b3b2c177bfb6fff01613b7097
ms.lasthandoff: 02/15/2017


---
# <a name="create-and-run-stored-procedures-triggers-and-user-defined-functions-using-the-documentdb-script-explorer"></a>Erstellen und Ausführen von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen mit dem DocumentDB-Skript-Explorer
Dieser Artikel bietet eine Übersicht über den [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/)-Skript-Explorer, einen JavaScript-Editor im Azure-Portal, der Ihnen die Anzeige von serverseitigen DocumentDB-Programmierartefakten ermöglicht, z.B. gespeicherte Prozeduren, Trigger und benutzerdefinierte Funktionen. Im Artikel [DocumentDB-serverseitige Programmierung: gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen](documentdb-programming.md) erfahren Sie mehr über DocumentDB-serverseitige Programmierung.

## <a name="launch-script-explorer"></a>Starten des Skript-Explorers 
1. Klicken Sie im [Azure-Portal](https://portal.azure.com) im linken Navigationsbereich auf **NoSQL (DocumentDB)** ![Azure DocumentDB-Symbol](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png). 

    Wenn **NoSQL (DocumentDB)** nicht angezeigt wird, klicken Sie unten auf **Weitere Dienste** und dann auf **NoSQL (DocumentDB)** ![Azure DocumentDB-Symbol](./media/documentdb-query-collections-query-explorer/nosql-documentdb-portal-icon.png).
2. Klicken Sie im Ressourcenmenü auf **Skript-Explorer**.
   
    ![Screenshot des Skript-Explorer-Befehls](./media/documentdb-view-scripts/scriptexplorercommand.png)
   
    Die Dropdownlisten **Datenbank** und **Sammlung** werden basierend auf dem Kontext, in dem der Skript-Explorer gestartet wird, automatisch ausgefüllt.  Wenn Sie diesen z. B. aus einem Datenbankblatt starten, sind die Felder der aktuellen Datenbank bereits ausgefüllt.  Wenn Sie diesen aus einem Auflistungsblatt starten, sind die Felder der aktuellen Auflistung ausgefüllt.
3. Mit den Dropdownlistenfeldern **Datenbank** und **Sammlung** können Sie die Sammlung, aus der aktuell Skripte angezeigt werden, auf einfache Weise ändern, ohne den Skript-Explorer beenden oder erneut starten zu müssen.  
4. Der Skript-Explorer unterstützt auch das Filtern des aktuell geladenen Skriptsets nach der id-Eigenschaft.  Nehmen Sie einfach eine Eingabe im Filterfeld vor, und die Ergebnisse werden in der Skript-Explorer-Liste basierend auf den angegebenen Kriterien gefiltert.
   
    ![Screenshot des Skript-Explorers mit gefilterten Ergebnissen](./media/documentdb-view-scripts/scriptexplorerfilterresults.png)

    > [!IMPORTANT] 
    > Die Filterfunktion vom Skript-Explorer filtert nur aus dem ***aktuell*** geladenen Satz an Skripten und aktualisiert die aktuell ausgewählte Sammlung nicht automatisch.

1. Zum Aktualisieren der vom Skript-Explorer geladenen Skriptliste klicken Sie einfach auf den Befehl **Aktualisieren** im oberen Bereich des Blatts.
   
    ![Screenshot des Befehls Aktualisieren in Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerrefresh.png)

## <a name="create-view-and-edit-stored-procedures-triggers-and-user-defined-functions"></a>Erstellen, Anzeigen und Bearbeiten von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen
Mit dem Skript-Explorer können Sie problemlos CRUD-Vorgänge an serverseitigen Programmierartefakten in DocumentDB durchführen.  

* Um ein Skript zu erstellen, klicken Sie im Skript-Explorer auf den entsprechenden Erstellungsbefehl, geben Sie eine ID an, geben Sie den Inhalt des Skripts ein, und klicken Sie auf **Speichern**.
  
    ![Screenshot der Option zum Erstellen des Skript-Explorers, der den JavaScript-Editor zeigt](./media/documentdb-view-scripts/scriptexplorercreatecommand.png)
* Wenn Sie einen Trigger erstellen, müssen Sie auch Triggertyp und Triggervorgang angeben.
  
    ![Screenshot der Option zum Erstellen eines Triggers im Skript-Explorer](./media/documentdb-view-scripts/scriptexplorercreatetrigger.png)
* Klicken Sie zum Anzeigen eines Skripts einfach auf das für Sie relevante Skript.
  
    ![Screenshot der Ansicht Skript-Erfahrung in Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerviewscript.png)
* Um ein Skript zu bearbeiten, nehmen Sie einfach die gewünschten Änderungen im JavaScript-Editor vor, und klicken Sie auf **Speichern**.
  
    ![Screenshot der Ansicht Skript-Erfahrung in Skript-Explorer](./media/documentdb-view-scripts/scriptexplorereditscript.png)
* Um alle ausstehenden Änderungen an einem Skript zu verwerfen, klicken Sie einfach auf den Befehl **Verwerfen** .
  
    ![Screenshot der Skript-Explorer-Ansicht zum Verwerfen von Änderungen](./media/documentdb-view-scripts/scriptexplorerdiscardchanges.png)
* Mit dem Skript-Explorer können außerdem problemlos die Systemeigenschaften des aktuell geladenen Skripts durch Klicken auf den Befehl **Eigenschaften** angezeigt werden.
  
    ![Screenshot der Ansicht Skript-Eigenschaften in Skript-Explorer](./media/documentdb-view-scripts/scriptproperties.png)
  
  > [!NOTE]
  > Die Zeitstempeleigenschaft (_ts) wird intern als Epochenzeit dargestellt, im Skript-Explorer wird der Wert jedoch in einem vom Menschen lesbaren GMT-Format angezeigt.
  > 
  > 
* Um ein Skript zu löschen, wählen Sie dieses im Skript-Explorer aus, und klicken Sie auf den Befehl **Löschen** .
  
    ![Screenshot des Befehls "Löschen" im Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerdeletescript1.png)
* Bestätigen Sie den Löschvorgang mit **Ja**, oder brechen Sie den Löschvorgang ab, indem Sie auf **Nein** klicken.
  
    ![Screenshot des Befehls "Löschen" im Skript-Explorer](./media/documentdb-view-scripts/scriptexplorerdeletescript2.png)

## <a name="execute-a-stored-procedure"></a>Ausführen einer gespeicherten Prozedur
> [!WARNING]
> Das Ausführen von gespeicherten Prozeduren im Skript-Explorer wird für serverseitige partitionierte Sammlungen noch nicht unterstützt. Weitere Informationen finden Sie unter [Partitionieren und Skalieren von Daten in DocumentDB](documentdb-partition-data.md).
> 
> 

Mit dem Skript-Explorer können Sie serverseitige gespeicherte Prozeduren über das Azure-Portal ausführen.

* Beim Öffnen eines Blatts einer neu zu erstellenden gespeicherten Prozedur wird bereits ein Standardskript (*Präfix*) bereitgestellt. Fügen Sie zum Ausführen des *Präfixskripts* oder Ihres eigenen Skripts eine *ID* und *Eingaben* hinzu. Für gespeicherte Prozeduren, die mehrere Parameter akzeptieren, müssen alle Eingaben in einem Array liegen (z.B. *["foo", "bar"]*).
  
    ![Screenshot des Skript-Explorer-Blatts „Gespeicherte Prozeduren“ zum Hinzufügen der Eingabe und Ausführen einer gespeicherten Prozedur](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-input.png)
* Um eine gespeicherte Prozedur auszuführen, klicken Sie im Skript-Editor-Bereich auf den Befehl **Speichern und ausführen**.
  
  > [!NOTE]
  > Mit dem Befehl **Speichern und ausführen** wird die gespeicherte Prozedur vor der Ausführung gespeichert, d.h., die zuvor gespeicherte Version der gespeicherten Prozedur wird überschrieben.
  > 
  > 
* Erfolgreiche Ausführungen gespeicherter Prozeduren erhalten den Status *Die gespeicherte Prozedur wurde erfolgreich gespeichert und ausgeführt*, und die zurückgegebenen Ergebnisse werden im Bereich *Ergebnisse* eingetragen.
  
    ![Screenshot des Skript-Explorer-Blatts „Gespeicherte Prozeduren“ zum Ausführen einer gespeicherten Prozedur](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure.png)
* Wenn bei der Ausführung ein Fehler auftritt, wird der Fehler im Bereich *Ergebnisse* eingetragen.
  
    ![Screenshot der Skripteigenschaftenansicht im Skript-Explorer Ausführen einer gespeicherten Prozedur mit Fehlern](./media/documentdb-view-scripts/documentdb-execute-a-stored-procedure-error.png)

## <a name="work-with-scripts-outside-the-portal"></a>Arbeiten mit Skripts außerhalb des Portals
Der Skript-Explorer im Azure-Portal ist nur eine Möglichkeit, mit gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen in DocumentDB zu arbeiten. Sie können mit Skripts auch arbeiten, indem Sie die REST-API und die [Client-SDKs](documentdb-sdk-dotnet.md)verwenden. Die REST-API-Dokumentation enthält Beispiele für die Arbeit mit [gespeicherten Prozeduren mithilfe von REST](https://msdn.microsoft.com/library/azure/mt489092.aspx), [benutzerdefinierten Funktionen mithilfe von REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) und [Triggern mithilfe von REST](https://msdn.microsoft.com/library/azure/mt489116.aspx). Es sind auch Beispiele verfügbar, die das [Arbeiten mit Skripts mit C#](documentdb-dotnet-samples.md#server-side-programming-examples) und das [Arbeiten mit Skripts mithilfe von Node.js](documentdb-nodejs-samples.md#server-side-programming-examples) zeigen.

## <a name="next-steps"></a>Nächste Schritte
Im Artikel [Gespeicherte Prozeduren, Datenbanktrigger und benutzerdefinierte Funktionen](documentdb-programming.md) erfahren Sie mehr über DocumentDB-serverseitige Programmierung.

Der [Lernpfad](https://azure.microsoft.com/documentation/learning-paths/documentdb/) ist ebenfalls eine hilfreiche Ressource, um mehr über DocumentDB zu erfahren.  


