---
title: Verwalten von Entwicklerkonten in Azure API Management mithilfe von Gruppen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Entwicklerkonten in Azure API Management mithilfe von Konten verwalten.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 33660b45-442f-44be-9a4a-1899d7f699b0
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: 00322340d67fd064a0dc9149790e031b94390709
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Erstellen und Verwenden von Gruppen für Entwicklerkonten in Azure API Management
In API Management werden Gruppen verwendet, um die Sichtbarkeit von Produkten für Entwickler zu verwalten. Produkte gewähren Sichtbarkeit für Gruppen, und Entwickler können alle Produkte anzeigen und abonnieren, die für die Gruppen sichtbar sind, in denen sie Mitglied sind. 

API Management umfasst die folgenden unveränderlichen Systemgruppen.

* **Administratoren** – Azure-Abonnementadministratoren sind Mitglieder dieser Gruppe. Administratoren verwalten API Management-Dienstinstanzen und erstellen die APIs, Operationen und Produkte, die von den Entwicklern verwendet werden.
* **Entwickler** – Zu dieser Gruppe gehören authentifizierte Benutzer des Entwicklerportals. Entwickler sind die Kunden, die Anwendungen unter Verwendung Ihrer APIs erstellen. Entwickler erhalten Zugriff zum Entwicklerportal und erstellen Anwendungen, die die Operationen einer API aufrufen.
* **Gäste** – Nicht authentifizierte Benutzer wie z. B. potenzielle Kunden, die das Entwicklerportal einer API Management-Instanz besuchen, fallen in diese Gruppe. Sie können diesen Benutzern schreibgeschützten Zugriff gewähren, z. B. um die APIs anzuzeigen, jedoch nicht aufrufen zu können.

Zusätzlich zu diesen Systemgruppen können Administratoren benutzerdefinierte Gruppen erstellen oder [externe Gruppen in zugeordneten Azure Active Directory-Mandanten verwenden][leverage external groups in associated Azure Active Directory tenants]. Benutzerdefinierte und externe Gruppen können gemeinsam mit Systemgruppen verwendet werden, um API-Produkte für Entwickler sichtbar zu machen und ihnen den Zugriff auf die API-Produkte zu ermöglichen. Beispielsweise können Sie eine benutzerdefinierte Gruppe für Entwickler eines spezifischen Partnerunternehmens erstellen und diesen Entwicklern Zugriff auf die APIs über ein Produkt erteilen, das nur die relevanten APIs enthält. Ein Benutzer kann Mitglied von mehr als einer Gruppe sein.

Diese Anleitung beschreibt, wie Administrator einer API Management-Instanz neue Gruppen hinzufügen und diese zu Produkten und Entwicklern zuordnen können.

> [!NOTE]
> Zusätzlich zum Erstellen und Verwalten von Gruppen im Herausgeberportal können Sie Ihre Gruppen mithilfe der Entität [Gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx) der API Management-REST-API erstellen und verwalten.
> 
> 

## <a name="create-group"></a>Erstellen einer Gruppe
Klicken Sie im Azure-Portal für Ihren API Management-Dienst auf **Herausgeberportal**, um einen neue Gruppe zu erstellen. Daraufhin gelangen Sie zum API Management-Herausgeberportal.

![Herausgeberportal][api-management-management-console]

> Falls Sie noch keine API Management-Dienstinstanz erstellt haben, finden Sie weitere Informationen im Abschnitt [Erstellen einer API Management-Dienstinstanz][Create an API Management service instance] im Tutorial [Erste Schritte mit Azure API Management][Get started with Azure API Management].
> 
> 

Klicken Sie im Menü **API Management** auf der linken Seite auf **Gruppen**, und klicken Sie anschließend auf **Gruppe hinzufügen**.

![Neue Gruppe hinzufügen][api-management-add-group]

Geben Sie einen eindeutigen Namen und eine optionale Beschreibung für die Gruppe ein und klicken Sie auf **Speichern**.

![Neue Gruppe hinzufügen][api-management-add-group-window]

Die neue Gruppe wird auf der Registerkarte „Gruppen“ angezeigt. Klicken Sie auf den Namen der Gruppe in der Liste, um **Name** oder **Beschreibung** zu bearbeiten. Klicken Sie auf **Löschen**, um die Gruppe zu löschen.

![Gruppe hinzugefügt][api-management-new-group]

Nachdem Sie die Gruppe erstellt haben, können Sie sie zu Produkten und Entwicklern zuordnen.

## <a name="associate-group-product"></a>Zuordnen einer Gruppe zu einem Produkt
Um eine Gruppe zu einem Produkt zuzuordnen, klicken Sie im Menü **API Management** auf der linken Seite auf **Produkte** und anschließend auf den Namen des gewünschten Produkts.

![Sichtbarkeit einstellen][api-management-add-group-to-product]

Auf der Registerkarte **Sichtbarkeit** können Sie Gruppen hinzufügen oder entfernen und die aktuellen Gruppen für das Produkt anzeigen. Um Gruppen hinzuzufügen oder zu entfernen, markieren Sie die Kontrollkästchen für die jeweiligen Gruppen und klicken Sie auf **Speichern**.

![Sichtbarkeit einstellen][api-management-add-group-to-product-visibility]

> [!NOTE]
> Informationen zum Hinzufügen von Azure Active Directory-Gruppen finden Sie unter [Autorisieren von Entwicklerkonten mithilfe von Azure Active Directory in Azure API Management](api-management-howto-aad.md).
> 
> Klicken Sie auf der Registerkarte **Sichtbarkeit** auf **Gruppen verwalten**, um Gruppen für ein Produkt zu konfigurieren.
> 
> 

Sobald ein Produkt zu einer Gruppe zugeordnet ist, können Entwickler in dieser Gruppe das Produkt anzeigen und abonnieren.

## <a name="associate-group-developer"></a>Zuordnen von Entwicklern zu Gruppen
Klicken Sie zum Zuordnen von Entwicklern zu einer Gruppe im Menü **API Management** auf der linken Seite auf **Benutzer**, und aktivieren Sie das Kontrollkästchen neben den Entwicklern, die Sie der Gruppe zuordnen möchten.

![Entwickler zu einer Gruppe hinzufügen][api-management-add-group-to-developer]

Markieren Sie die gewünschten Entwickler und klicken Sie anschließend auf die entsprechende Gruppe in der Dropdownloste **Zu Gruppe hinzufügen** . Über die Dropdownliste **Aus Gruppe entfernen** können Sie Entwickler aus Gruppen entfernen. 

![Entwickler][api-management-add-group-to-developer-saved]

Sobald Sie die Zuordnung zwischen Entwickler und Gruppe erstellt haben, können Sie diese auf der Registerkarte **Benutzer** anzeigen.

## <a name="next-steps"></a>Nächste Schritte
* Sobald ein Entwickler zu einer Gruppe hinzugefügt wurde, können diese die zu dieser Gruppe zugeordneten Produkte anzeigen und abonnieren. Weitere Informationen finden Sie unter [Erstellen und Veröffentlichen eines Produkts in Azure API Management][How create and publish a product in Azure API Management].
* Zusätzlich zum Erstellen und Verwalten von Gruppen im Herausgeberportal können Sie Ihre Gruppen mithilfe der Entität [Gruppe](https://msdn.microsoft.com/library/azure/dn776329.aspx) der API Management-REST-API erstellen und verwalten.

[api-management-management-console]: ./media/api-management-howto-create-groups/api-management-management-console.png
[api-management-add-group]: ./media/api-management-howto-create-groups/api-management-add-group.png
[api-management-add-group-window]: ./media/api-management-howto-create-groups/api-management-add-group-window.png
[api-management-new-group]: ./media/api-management-howto-create-groups/api-management-new-group.png
[api-management-add-group-to-product]: ./media/api-management-howto-create-groups/api-management-add-group-to-product.png
[api-management-add-group-to-product-visibility]: ./media/api-management-howto-create-groups/api-management-add-group-to-product-visibility.png
[api-management-add-group-to-developer]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer.png
[api-management-add-group-to-developer-saved]: ./media/api-management-howto-create-groups/api-management-add-group-to-developer-saved.png

[api-management-]: ./media/api-management-howto-create-groups/api-management-.png

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md#how-to-add-an-external-azure-active-directory-group
