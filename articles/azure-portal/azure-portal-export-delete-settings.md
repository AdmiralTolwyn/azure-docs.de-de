---
title: Exportieren oder Löschen von Azure-Portaleinstellungen
description: Erfahren Sie, wie Sie Ihre Benutzereinstellungen, privaten Dashboards und benutzerdefinierten Einstellungen im Azure-Portal exportieren oder löschen können.
services: azure-portal
keywords: ''
author: santhoshsomayajula
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-portal
ms.custom: ''
manager: mtillman
ms.author: mblythe
ms.openlocfilehash: accdfbd939fad73ca7d008450c358d366e7f8b70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76900752"
---
# <a name="export-or-delete-user-settings"></a>Exportieren oder Löschen von Benutzereinstellungen

Mithilfe der Einstellungen und Funktionen im Azure-Portal können Sie eine benutzerdefinierte Benutzeroberfläche erstellen. Die Informationen zu Ihren benutzerdefinierten Einstellungen werden in Azure gespeichert. Sie können folgende Benutzerdaten exportieren oder löschen:

* Private Dashboards im Azure-Portal
* Benutzereinstellungen wie bevorzugte Abonnements oder Verzeichnisse oder letztes Anmeldeverzeichnis
* Designs und andere benutzerdefinierte Portaleinstellungen

Es empfiehlt sich, Ihre Einstellungen zu exportieren und zu überprüfen, bevor Sie sie löschen. Das Neuerstellen von Dashboards oder das erneute Einrichten von benutzerdefinierten Einstellungen kann zeitaufwendig sein.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="export-or-delete-your-portal-settings"></a>Exportieren und Löschen Ihrer Portaleinstellungen

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

1. Wählen Sie ganz oben im Portal die Option ![Einstellungssymbol](media/azure-portal-export-delete-settings/settings-icon.png) **Einstellungen** aus.

1. Wählen Sie **Alle Einstellungen exportieren** oder **Alle Einstellungen und private Dashboards löschen** aus.

    ![Einstellungen des Azure-Portals und Optionen für Einstellungen](media/azure-portal-export-delete-settings/azure-portal-settings-with-export-delete.png)

      Diese Aktionen sind in der folgenden Tabelle beschrieben.

      | Action | BESCHREIBUNG |
      | --- | --- |
      | **Alle Einstellungen exportieren** | Erstellt eine *JSON*-Datei, die Ihre Benutzereinstellungen enthält, z. B. Farbdesign, Favoriten und private Dashboards.|
      | **Alle Einstellungen und private Dashboards löschen** | Löscht alle Verknüpfungen mit privaten Dashboards und anderen benutzerdefinierten Einstellungen, die Sie im Portal vorgenommen haben. |

> [!NOTE]
> Aufgrund der dynamischen Art der Benutzereinstellungen und des Risikos der Datenbeschädigung können Sie keine Einstellungen aus der *JSON*-Datei importieren.
>
>

## <a name="next-steps"></a>Nächste Schritte

* [Freigeben von Azure-Dashboards mit rollenbasierter Zugriffssteuerung](azure-portal-dashboard-share-access.md)
* [Hinzufügen, Entfernen und Neuanordnen von Favoriten](azure-portal-add-remove-sort-favorites.md)
