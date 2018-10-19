---
title: Aufheben der Sperrung von Benutzern mit Azure Active Directory Identity Protection | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die Blockierung von Benutzern aufheben, die durch eine Azure Active Directory Identity Protection-Richtlinie blockiert wurden.
services: active-directory
keywords: Azure Active Directory Identity Protection, Benutzerblockierung aufheben
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: a953d425-a3ef-41f8-a55d-0202c3f250a7
ms.service: active-directory
ms.component: conditional-access
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: f8bf983033407bbf597af15f18f28ecf33b7558f
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631683"
---
# <a name="how-to-unblock-users"></a>Gewusst wie: Aufheben der Sperrung von Benutzern

Mit Azure Active Directory Identity Protection können Sie Richtlinien konfigurieren, um Benutzer zu blockieren, wenn die konfigurierten Bedingungen erfüllt sind. Ein blockierter Benutzer wendet sich zur Aufhebung der Blockierung in der Regel an den Helpdesk. In diesem Artikel erfahren Sie Schritt für Schritt, wie Sie die Blockierung eines Benutzers aufheben.

## <a name="determine-the-reason-for-blocking"></a>Ermitteln der Ursache für die Blockierung
Wenn Sie die Blockierung eines Benutzers aufheben möchten, müssen Sie zunächst ermitteln, durch welche Art von Richtlinie der Benutzer blockiert wurde, da hiervon die weitere Vorgehensweise abhängt.
Bei Azure Active Directory Identity Protection kann die Blockierung des Benutzers entweder auf eine Anmelderisikorichtlinie oder auf eine Benutzerrisikorichtlinie zurückzuführen sein.

Die Art der Richtlinie, durch die ein Benutzer blockiert wurde, können Sie der Überschrift des Dialogfelds entnehmen, das dem Benutzer bei der Anmeldung angezeigt wurde:

| Richtlinie | Benutzerdialogfeld |
| --- | --- |
| Anmelderisiko |![Blockierte Anmeldung](./media/howto-unblock-user/02.png) |
| Benutzerrisiko |![Blockiertes Konto](./media/howto-unblock-user/104.png) |

Dabei gilt Folgendes:

* Ein Benutzer, der durch eine Anmelderisikorichtlinie blockiert wird, wird auch als verdächtige Anmeldung bezeichnet.
* Ein Benutzer, der durch eine Benutzerrisikorichtlinie blockiert wird, wird auch als gefährdetes Konto bezeichnet.

## <a name="unblocking-suspicious-sign-ins"></a>Aufheben der Blockierung verdächtiger Anmeldungen
Zur Aufheben der Blockierung verdächtiger Anmeldungen stehen folgende Optionen zur Verfügung:

1. **Anmelden über einen bekannten Ort oder ein bekanntes Gerät:** Die Blockierung einer verdächtigen Anmeldung ist häufig auf Anmeldeversuche über einen unbekannten Ort oder ein unbekanntes Gerät zurückzuführen. Um zu ermitteln, ob dies die Ursache für die Blockierung ist, muss sich der betroffene Benutzer lediglich über einen bekannten Ort oder über ein bekanntes Gerät anmelden.
2. **Ausschließen von der Richtlinie:** Wenn Sie vermuten, dass die aktuelle Konfiguration Ihrer Anmelderichtlinie Probleme für bestimmte Benutzer verursacht, können Sie diese Benutzer von der Richtlinie ausschließen. Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Deaktivieren der Richtlinie:** Wenn Sie vermuten, dass Ihre Richtlinienkonfiguration Probleme für alle Benutzer verursacht, können Sie die Richtlinie deaktivieren. Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Aufheben der Blockierung gefährdeter Konten
Zur Aufheben der Blockierung gefährdeter Konten stehen folgende Optionen zur Verfügung:

1. **Zurücksetzen des Kennworts:** Sie können das Kennwort des Benutzers zurücksetzen. 
2. **Schließen aller Risikoereignisse:** Die Benutzerrisikorichtlinie blockiert einen Benutzer, wenn die konfigurierte Benutzerrisikostufe für die Blockierung des Zugriffs erreicht wurde. Sie können die Risikostufe eines Benutzers verringern, indem Sie gemeldete Risikoereignisse manuell schließen. 
3. **Ausschließen von der Richtlinie:** Wenn Sie vermuten, dass die aktuelle Konfiguration Ihrer Anmelderichtlinie Probleme für bestimmte Benutzer verursacht, können Sie diese Benutzer von der Richtlinie ausschließen. Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Deaktivieren der Richtlinie:** Wenn Sie vermuten, dass Ihre Richtlinienkonfiguration Probleme für alle Benutzer verursacht, können Sie die Richtlinie deaktivieren. Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Nächste Schritte
 
Sie möchten mehr über Azure AD Identity Protection erfahren? Weitere Informationen finden Sie unter [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
