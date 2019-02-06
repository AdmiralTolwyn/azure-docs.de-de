---
title: Fehlerbehandlung bei einem Objekt, das nicht mit Azure AD synchronisiert wird | Microsoft-Dokumentation
description: 'Problembehandlung: Warum wird ein Objekt nicht mit Azure AD synchronisiert?'
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: 7b43b0e0676cc31938bf64cf84f9e6799c2dd3dd
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296596"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-to-azure-ad"></a>Problembehandlung: Ein Objekt wird nicht mit Azure AD synchronisiert

Wenn ein Objekt wie erwartet nicht mit Azure AD synchronisiert wird, kann das verschiedene Ursachen haben. Wenn Sie eine E-Mail mit Fehlerbenachrichtigung von Azure AD erhalten haben, oder Sie die Fehler im Azure AD Connect Health sehen, dann lesen Sie stattdessen [Beheben von Fehlern während der Synchronisierung](tshoot-connect-sync-errors.md). Wenn Sie ein Problem behandeln möchten, in dem das Objekt nicht in Azure AD ist, dann ist dieses Thema für Sie das Richtige. Es wird beschrieben, wie Fehler in der lokalen Komponente Azure AD Connect-Synchronisierung gefunden werden.

>[!IMPORTANT]
>Verwenden Sie für die Bereitstellung von Azure Active Directory (AAD) Connect mit Version 1.1.749.0 oder höher den [Problembehandlungstask](tshoot-connect-objectsync.md) im Assistenten, um Probleme bei der Objektsynchronisierung zu beheben. 

## <a name="synchronization-process"></a>Synchronisierungsvorgang

Bevor wir Synchronisationsprobleme untersuchen, müssen wir den Synchronisationsprozess von **Azure AD Connect** verstehen:

  ![Azure AD Connect-Synchronisierungsprozess](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**Terminologie**

* **CS:** Connectorbereich, eine Tabelle in der Datenbank.
* **MV:** Metaverse, eine Tabelle in der Datenbank.
* **AD:** Active Directory
* **AAD:** Azure Active Directory

### <a name="synchronization-steps"></a>**Synchronisierungsschritte**
Der Synchronisierungsprozess umfasst die folgenden Schritte:

1. **Aus AD importieren:** **Active Directory**-Objekte werden in **AD CS** eingefügt.

2. **Aus AAD importieren:** **Active Directory**-Objekte werden in **AAD CS** eingefügt.

3. **Synchronisierung:** Es werden **eingehende Synchronisierungsregeln** und **ausgehende Synchronisierungsregeln** in der Reihenfolge der Priorität von niedrig zu hoch ausgeführt. Informationen zu den Synchronisierungsregeln finden Sie im **Synchronisierungsregel-Editor** in der Desktopanwendungen. Die **eingehenden Synchronisierungsregeln** übertragen den Daten aus CS zu MV. Die **ausgehenden Synchronisierungsregeln** verschieben Daten aus MV zu CS.

4. **In AD exportieren:** Nach der Synchronisierung werden Objekte aus AD CS in das **Active Directory** exportiert.

5. **In AAD exportieren:** Nach der Synchronisierung werden Objekte aus AAD CS in das **Azure Active Directory** exportiert.

## <a name="troubleshooting"></a>Problembehandlung

Um den Fehler zu finden, müssen Sie an einigen verschiedenen Stellen in folgender Reihenfolge suchen:

1. Die [Vorgangsprotokolle](#operations) für das Suchen von Fehlern, die während des Importierens und Synchronisierens vom Synchronisierungsmoduls identifiziert werden.
2. Die [Connectorbereich](#connector-space-object-properties) zum Suchen von fehlenden Objekten und Synchronisierungsfehlern.
3. Die [Metaverse](#metaverse-object-properties) für die Suche nach Daten-basierten Problemen.

Starten Sie [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) vor dem Beginn der folgenden Schritte.

## <a name="operations"></a>Vorgänge
Sie sollten mit Ihrer Problembehandlung in der Registerkarte „Vorgänge“ im Synchronization Service Manager beginnen. Die Registerkarte „Vorgänge“ zeigt die Ergebnisse der letzten Vorgänge.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/operations.png)  

Die obere Hälfte zeigt alle Ausführungen in chronologischer Reihenfolge. Standardmäßig enthält das Vorgangsprotokoll Informationen der letzten sieben Tage. Diese Einstellung kann mit dem [Scheduler](how-to-connect-sync-feature-scheduler.md) geändert werden. Suchen Sie nach Ausführungen ohne Erfolgsstatus. Die Sortierung kann durch Klicken auf die Kopfzeilen geändert werden.

Die Spalte **Status** zeigt die wichtigste Information und das schwerwiegendste Problem einer Ausführung an. Hier folgt eine kurze Zusammenfassung der häufigsten Status, in der Reihenfolge ihrer Untersuchungspriorität (bei „*“ gibt es mehrere mögliche Fehlerzeichenfolgen).

| Status | Comment |
| --- | --- |
| stopped-* |Die Ausführung konnte nicht abgeschlossen werden. Beispielsweise, wenn das Remotesystem ausgefallen ist und nicht kontaktiert werden kann. |
| stopped-error-limit |Es gibt mehr als 5.000 Fehler. Die Ausführung wurde aufgrund der großen Anzahl von Fehlern automatisch beendet. |
| completed-\*-errors |Die Ausführung wurde abgeschlossen, es sind jedoch Fehler (weniger als 5.000) aufgetreten, die untersucht werden sollten. |
| completed-\*-warnings |Die Ausführung wurde abgeschlossen, einige Daten weisen jedoch einen unerwarteten Zustand auf. Wenn Fehler auftreten, ist diese Meldung in der Regel nur ein Symptom. Bis Sie die Fehler behoben haben, sollten Sie keine Warnungen untersuchen. |
| Erfolg |Keine Probleme |

Wenn Sie eine Zeile auswählen, wird der untere Bereich aktualisiert, und die Details dieser Ausführung werden angezeigt. Ganz links neben dem unteren Teil erscheint möglicherweise eine Liste mit der **Schrittnummer**. Diese Liste wird nur angezeigt, wenn Ihre Gesamtstruktur mehrere Domänen enthält und jede Domäne als einzelner Schritt dargestellt wird. Den Domänennamen finden Sie unter der Überschrift **Partition**. Unter **Synchronization Statistics**(Synchronisierungsstatistik) finden Sie weitere Informationen zur Anzahl verarbeiteter Änderungen. Sie können auf die Links klicken, um eine Liste mit den geänderten Objekten anzuzeigen. Sind Objekte mit Fehlern vorhanden, werden diese Fehler unter **Synchronisierungsfehler**angezeigt.

### <a name="troubleshoot-errors-in-operations-tab"></a>Fehlerbehandlung in der Registerkarte „Vorgänge“
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorsync.png)  
Wenn Fehler auftreten, werden sowohl das fehlerhafte Objekt als auch der Fehler selbst als Links dargestellt, über die weitere Informationen abgerufen werden können.

Klicken Sie zunächst auf die Fehlerzeichenfolge (**sync-rule-error-function-triggered** in der Abbildung). Eine Übersicht über das Objekt wird angezeigt. Klicken Sie zum Anzeigen des tatsächlichen Fehlers auf die Schaltfläche **Stapelüberwachung**. Dadurch werden für den Fehler Informationen der Debugebene angezeigt.

Sie können im Feld **Call Stack Information** (Aufruflisteninformationen) mit der rechten Maustaste auf **Select All** (Alle auswählen) klicken, und anschließend **Copy** (Kopieren) auswählen. Sie können dann den Stapel kopieren und den Fehler in Ihrem bevorzugten Editor, z. B. „Notepad“ betrachten.

* Wenn der Fehler aus **SyncRulesEngine**stammt, beginnen die Aufruflisteninformationen mit einer Liste aller Attribute für das Objekt. Scrollen Sie nach unten, bis Sie die Überschrift **InnerException = >** sehen.  
  ![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)  
   Die Zeile danach gibt Aufschluss über den Fehler. Der Fehler in der obigen Abbildung stammt aus einer benutzerdefinierten Synchronisierungsregel, die von Fabrikam erstellt wurde.

Wenn der Fehler selbst nicht genügend Informationen liefert, ist es an der Zeit, sich die Daten selbst anzusehen. Sie können auf den Link mit der Objekt-ID klicken und mit der Problembehandlung der [importierten Objekte des Connectorbereichs](#cs-import) fortfahren.

## <a name="connector-space-object-properties"></a>Eigenschaften der Objekte des Connectorbereichs
Wenn Sie in der Registerkarte [Vorgänge](#operations) keine Fehler gefunden haben, müssen Sie im nächsten Schritt dem Objekt des Connectorbereichs aus Active Directory zum Metaverse und Azure AD folgen. In diesem Pfad sollten Sie feststellen können, wo das Problem liegt.

### <a name="search-for-an-object-in-the-cs"></a>Ein Objekt in der CS suchen

Kicken Sie im **Synchronization Service Manager** auf **Connectors**, wählen Sie den Active Directory-Connector und **Connectorbereich durchsuchen** aus.

Wählen Sie im **Bereich** **RDN** (wenn Sie nach dem CN-Attribut suchen möchten) oder **DN oder Anker** (wenn Sie auf dem Attribut „distinguishedName“ suchen möchten) aus. Geben Sie einen Wert ein, und klicken Sie auf **Suchen**.  
![Suche des Connectorbereichs](./media/tshoot-connect-object-not-syncing/cssearch.png)  

Wenn Sie das gesuchte Objekt nicht finden, wurde es möglicherweise mit der [domänenbasierten Filterung](how-to-connect-sync-configure-filtering.md#domain-based-filtering) oder [Filterung basierend auf der Organisationseinheit](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering) gefiltert. Lesen Sie das Thema [Konfigurieren der Filterung](how-to-connect-sync-configure-filtering.md), um sicherzustellen, dass das Filtern wie erwartet konfiguriert ist.

Eine andere nützliche Suche besteht darin, den Azure AD-Connector auszuwählen. Wählen Sie dafür im **Bereich** **Pending Import**(ausstehender Import) aus und anschließend das Kontrollkästchen **Hinzufügen**. Bei dieser Suche werden Ihnen alle synchronisierten Objekte in Azure AD angezeigt, die nicht mit einem lokalen Objekt zugeordnet werden können.  
![Verwaiste Suche des Connectorbereichs](./media/tshoot-connect-object-not-syncing/cssearchorphan.png)  
Diese Objekte wurden von einem anderen Synchronisierungsmodul oder einem Synchronisierungsmodul mit einer anderen Filterkonfiguration erstellt. In dieser Ansicht wird eine Liste der **verwaisten** Objekte angezeigt, die nicht mehr verwaltet werden. Sie sollten diese Liste überprüfen, und diese Objekte mit den [Azure AD PowerShell](https://aka.ms/aadposh)-Cmdlets entfernen.

### <a name="cs-import"></a>CS Import
 Beim Öffnen eines Connectorbereichsobjekts befinden sich oben mehrere Registerkarten. Auf der Registerkarte **Importieren** werden die Daten angezeigt, die nach einem Import bereitgestellt werden.  
![CS-Objekt](./media/tshoot-connect-object-not-syncing/csobject.png)    
Unter **Alter Wert** wird dargestellt, was derzeit im System gespeichert ist, und unter **Neuer Wert** wird angezeigt, was aus dem Quellsystem empfangen, aber noch nicht angewendet wurde. Wenn ein Fehler auf dem Objekt vorhanden ist, werden die Änderungen nicht verarbeitet.

**Fehler**  
![CS-Objekt](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  
Die Registerkarte **Synchronisierungsfehler** wird nur angezeigt, wenn ein Problem mit dem Objekt besteht. Weitere Informationen finden Sie unter [Problembehandlung bei Synchronisierungsfehlern](#troubleshoot-errors-in-operations-tab).

### <a name="cs-lineage"></a>CS-Herkunft
 Auf der Registerkarte für die Herkunft wird gezeigt, wie das Objekt des Connectorbereichs mit dem Metaverseobjekt verknüpft ist. Sie sehen, wann der Connector zuletzt eine Änderung aus dem verbundenen System importiert hat und welche Regeln zum Auffüllen der Daten im Metaverse angewendet wurden.  
![CS-Herkunft](./media/tshoot-connect-object-not-syncing/cslineage.png)  
In der Spalte **Aktion** sehen Sie eine einzelne Synchronisierungsregel vom Typ **Eingehend** mit der Aktion **Bereitstellen**. Damit wird angegeben, dass das Metaverse-Objekt erhalten bleibt, solange dieses Objekt des Connectorbereichs vorhanden ist. Wenn die Liste mit den Synchronisierungsregeln hingegen eine Synchronisierungsregel mit der Richtung **Outbound** (Ausgehend) und der Aktion **Provision** (Bereitstellen) enthält, wird beim Löschen des Metaverseobjekts auch dieses Objekt gelöscht.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/cslineageout.png)  
Darüber hinaus sehen Sie in der Spalte für die Kennwortsynchronisierung (**PasswordSync**), dass der eingehende Connectorbereich Kennwortänderungen beitragen kann, da eine Synchronisierungsregel den Wert **TRUE** besitzt. Dieses Kennwort wird anschließend über die ausgehende Regel an Azure AD gesendet.

Auf der Registerkarte für die Herkunft gelangen Sie zum Metaverse, indem Sie auf [Metaverse Object Properties](#mv-attributes)(Metaverse-Objekteigenschaften) klicken.

Unterhalb der Registerkarten befinden sich zwei Schaltflächen: **Vorschau** und **Protokoll**.

### <a name="preview"></a>Vorschau
 Die Vorschauseite wird verwendet, um ein einzelnes Objekt zu synchronisieren. Sie ist nützlich, wenn Sie Probleme mit einigen benutzerdefinierten Synchronisierungsregeln behandeln und die Auswirkungen einer Änderung auf ein einzelnes Objekt sehen möchten. Sie können zwischen **Vollständige Synchronisierung** und **Deltasynchronisierung** auswählen. Außerdem haben Sie die Wahl zwischen **Generate Preview** (Vorschau generieren), um die Änderung nur im Arbeitsspeicher beizubehalten, und **Commit Preview** (Commitvorschau), um die Metaverse zu aktualisieren und alle Änderungen im Zielconnectorbereich bereitzustellen.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/preview.png)  
Sie können das Objekt und die für einen bestimmten Attributfluss angewendete Regel prüfen.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>Protokoll
Die Protokollseite wird verwendet, um den Kennwortsynchronisierungsstatus und den Verlauf anzuzeigen. Weitere Informationen finden Sie unter [Problembehandlung bei der Kennworthashsynchronisierung](tshoot-connect-password-hash-synchronization.md).

## <a name="metaverse-object-properties"></a>Objekteigenschaften der Metaverse
Es ist in der Regel besser, mit der Suche im Active Directory-[Connectorbereich](#connector-space) zu starten. Aber Sie können die Suche auch aus der Metaverse starten.

### <a name="search-for-an-object-in-the-mv"></a>Ein Objekt in der MV suchen
Klicken Sie im **Synchronization Service Manager** auf **Metaverse Search**(Metaverse-Suche). Erstellen Sie eine Abfrage, bei der Sie wissen, dass sie den Benutzer findet. Sie können nach allgemeinen Attributen, z.B. „accountName (sAMAccountName)“ und „userPrincipalName“ suchen. Weitere Informationen finden Sie unter [Metaversesuche](how-to-connect-sync-service-manager-ui-mvsearch.md).
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

Klicken Sie im Fenster **Suchergebnisse** auf das Objekt.

Wenn Sie das Objekt nicht gefunden haben, dann hat es die Metaverse noch nicht erreicht. Suchen Sie das Objekt weiter im **Active Directory**-[Connectorbereich](#connector-space-object-properties). Wenn Sie das Objekt im **Active Directory**-Connectorbereich finden, könnte ein Synchronisationsfehler vorliegen, der das Objekt daran hindert, zur Metaverse zu gelangen, oder es könnte ein Synchronisationsregelfilter angewendet werden.

### <a name="object-not-found-in-the-mv"></a>Objekt in MV nicht gefunden
Wenn sich das Objekt im **Active Directory** CS befindet, aber nicht im MV vorhanden ist, wird ein Bereichsfilter angewendet. 

* Um sich den Bereichsfilter anzusehen, rufen Sie das Menü der Desktopanwendung auf, und klicken Sie auf **Synchronisierungsregel-Editor**. Filtern Sie die für das Objekt geltenden Regeln anhand der Filter unten.

  ![Suche in den eingehenden Synchronisierungsregeln](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

* Zeigen Sie Regel in der Liste von oben nach unten an, und überprüfen Sie die **Bereichsfilter**. Wenn der Wert **isCriticalSystemObject** im Bereichsfilter unten „Null“ oder „False“ oder leer ist, gehört er zum Bereich.

  ![Suche in den eingehenden Synchronisierungsregeln](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

* Gehen Sie zur Attributliste [CS Import](#cs-import), und überprüfen Sie, welcher Filter das Objekt blockiert, um es in die MV zu verschieben. Abgesehen davon zeigt die Attributliste **Connector Space** nur Attribute an, die nicht Null oder leer sind. Wenn beispielsweise **isCriticalSystemObject** nicht in der Liste erscheint, bedeutet dies, dass der Wert dieses Attributs Null oder leer ist.

### <a name="object-not-found-in-the-aad-cs"></a>Objekt in AAD CS nicht gefunden
Das Objekt ist nicht im **Connectorbereich** von **Azure Active Directory** vorhanden. Allerdings ist das Objekt in der MV vorhanden. Schauen Sie sich also den Bereichsfilter der **Ausgangsregel** des entsprechenden **Connectorbereichs** an, und überprüfen Sie, ob das Objekt herausgefiltert wurde, da die [MV-Attribute](#mv-attributes) nicht die Kriterien erfüllen.

* Um den ausgehenden Bereichsfilter anzuzeigen, wählen Sie die für das Objekt anwendbaren Regeln aus, indem Sie den untenstehenden Filter anpassen. Zeigen Sie jede einzelne Regel an, und überprüfen Sie den entsprechenden Wert für das [MV-Attribut](#mv-attributes).

  ![Suche in den ausgehenden Synchronisierungsregeln](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV-Attribute
: Auf der Registerkarte „Attribute“ sehen Sie die Werte und den Connector, von dem sie stammen.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvobject.png)  

Wenn ein Objekt nicht synchronisiert ist, sehen Sie sich die folgenden Attribute im Metaverse an:
- Ist das Attribut **cloudFiltered** vorhanden, und wird es auf **TRUE** festgelegt? Wenn es so ist, dann wurde es anhand der Schritte in der [attributbasierten Filterung](how-to-connect-sync-configure-filtering.md#attribute-based-filtering) gefiltert.
- Ist das Attribut **sourceAnchor** vorhanden? Wenn dies nicht der Fall ist, haben Sie eine Topologie mit Kontoressourcengesamtstruktur? Wenn ein Objekt als ein verknüpftes Postfach identifiziert wird (das Attribut **msExchRecipientTypeDetails** hat den Wert 2), dann wird „sourceAnchor“ von einem aktivierten Konto für Active Directory-Gesamtstruktur bereitgestellt. Stellen Sie sicher, dass das Hauptkonto ordnungsgemäß importiert und synchronisiert wurde. Das Hauptkonto muss bei den [Connectors](#mv-connectors) für das Objekt aufgelistet sein.

### <a name="mv-connectors"></a>MV-Connectors
: Auf der Registerkarte „Connectors“ werden alle Connectorbereiche angezeigt, die über eine Darstellung des Objekts verfügen.  
![Synchronization Service Manager](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  
Sie müssen einen Connector haben für:

- Jede Active Directory-Gesamtstruktur, in der der Benutzer dargestellt wird. Diese Darstellung kann foreignSecurityPrincipals und Contact-Objekte enthalten.
- Ein Connector in Azure AD.

Wenn Ihnen der Azure AD-Connector fehlt, dann lesen Sie [MV-Attribute](#mv-attributes), um die Kriterien, die für Azure AD bereitgestellt werden, zu überprüfen.

Auf dieser Registerkarte können Sie auch zum [Connectorbereichsobjekt](#connector-space-object-properties) navigieren. Wählen Sie eine Zeile aus, und klicken Sie auf **Eigenschaften**.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zur Konfiguration der [Azure AD Connect-Synchronisierung](how-to-connect-sync-whatis.md) .

Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md).
