---
title: Verwenden einer benutzerdefinierten Markdown-Kachel in Azure-Dashboards
description: Erfahren Sie, wie Sie einem Azure-Dashboard eine Markdown-Kachel zum Anzeigen von statischen Inhalten hinzufügen.
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640141"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Verwenden einer Markdown-Kachel in Azure-Dashboards zum Anzeigen von benutzerdefinierten Inhalten

Sie können in Ihren Azure-Dashboards eine Markdown-Kachel hinzufügen, um benutzerdefinierte statische Inhalte anzuzeigen. Beispielsweise können Sie mit einer Markdown-Kachel allgemeine Anweisungen, ein Bild oder eine Gruppe von Hyperlinks anzeigen.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Hinzufügen einer Markdown-Kachel in Ihrem Dashboard

1. Wählen Sie auf der Seitenleiste des Azure-Portals die Option **Dashboard** aus. Wenn Sie benutzerdefinierte Dashboards erstellt haben, wählen Sie in der Dashboardansicht in der Dropdownliste das Dashboard aus, in dem die benutzerdefinierte Markdown-Kachel angezeigt werden soll. Wählen Sie das Bearbeitungssymbol aus, um den **Kachelkatalog** zu öffnen.

   ![Screenshot: Bearbeitungsansicht des Dashboards](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Suchen Sie im **Kachelkatalog** die Kachel mit der Bezeichnung **Markdown**, und klicken Sie auf **Hinzufügen**. Die Kachel wird dem Dashboard hinzugefügt, und der Bereich **Markdown bearbeiten** wird geöffnet.

1. Bearbeiten Sie die Felder **Titel**, **Untertitel** und **Inhalt**, um die Kachel anzupassen. Im hier gezeigten Beispiel wurde die Markdown-Kachel so bearbeitet, dass benutzerdefinierte Helpdeskinformationen angezeigt werden.

   ![Screenshot: Bearbeitungsansicht der Markdown-Kachel](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Wählen Sie **Fertig** aus, um den Bereich **Markdown bearbeiten** zu schließen. Der benutzerdefinierte Inhalt wird in der Markdown-Kachel angezeigt, deren Größe dann durch Ziehen des Ziehpunkts in der rechten unteren Ecke geändert werden kann.

   ![Screenshot: benutzerdefinierte Markdown-Kachel](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Funktionen und Einschränkungen von Markdown-Inhalten

Auf der Markdown-Kachel können Sie eine beliebige Kombination von Nur-Text, Markdown-Syntax und HTML-Inhalten verwenden. Im Azure-Portal werden Ihre Inhalte mithilfe der Open-Source-Bibliothek _marked_ in HTML-Code transformiert, der auf der Kachel angezeigt wird. Der über _marked_ generierte HTML-Code wird im Portal vor dem Rendern vorab verarbeitet. Mit diesem Schritt wird sichergestellt, dass die Sicherheit und das Layout des Portals durch Ihre Anpassung nicht beeinträchtigt werden. Bei dieser Vorverarbeitung werden Teile des HTML-Codes entfernt, die eine potenzielle Bedrohung darstellen. Folgende Inhaltstypen sind im Portal nicht zulässig:

* JavaScript: `<script>`-Tags und JavaScript-Inline-Auswertungen werden entfernt.
* iFrames: `<iframe>`-Tags werden entfernt.
* style: `<style>`-Tags werden entfernt. Attribute von Inlineformatvorlagen in HTML-Elementen werden offiziell nicht unterstützt. Möglicherweise können Sie einige Inlineelemente von Formatvorlagen verwenden, wenn diese jedoch das Layout des Portals beeinträchtigen, können sie jederzeit nicht mehr funktionieren. Die Markdown-Kachel ist für allgemeine statische Inhalte vorgesehen, für die die Standardformatvorlagen des Portals verwendet werden.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Erstellen eines benutzerdefinierten Dashboards finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](../azure-portal/azure-portal-dashboards.md).
