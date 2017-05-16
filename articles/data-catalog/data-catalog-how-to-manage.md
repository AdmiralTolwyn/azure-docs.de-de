---
title: Verwalten von Datenressourcen in Azure Data Catalog | Microsoft-Dokumentation
description: Gewusst-wie-Artikel mit Informationen dazu, wie Sie die Sichtbarkeit und den Besitz von Datenassets steuern, die in Azure Data Catalog registriert sind.
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 05/15/2017
ms.author: maroche
ms.translationtype: Human Translation
ms.sourcegitcommit: cd3184059a0970bb7c9b59803cf37125abbbd879
ms.openlocfilehash: 45a7b2df88a2edf2bccdfc9d2aaea932f2096f24
ms.contentlocale: de-de
ms.lasthandoff: 01/24/2017


---
# <a name="how-to-manage-data-assets-in-azure-data-catalog"></a>Verwalten von Datenressourcen in Azure Data Catalog
## <a name="introduction"></a>Einführung
**Azure Data Catalog** enthält Funktionen für die Datenquellenermittlung. Hiermit können Benutzer Datenquellen, die sie zum Durchführen von Analysen und Treffen von Entscheidungen benötigen, leicht ermitteln und verstehen. Diese Ermittlungsfunktionen haben den größten Nutzen, wenn alle Benutzer möglichst viele verfügbare Datenquellen finden und verstehen können. Daher sollte es beim Standardverhalten von Data Catalog darum gehen, dass alle registrierten Datenquellen für alle Katalogbenutzer sichtbar und ermittelbar sind.

Mit Data Catalog erhalten Benutzer keinen Zugriff auf die eigentlichen Daten. Der Datenzugriff wird vom Besitzer der Datenquelle kontrolliert. Mit Data Catalog können Benutzer Datenquellen ermitteln und die Metadaten anzeigen, die zu den im Katalog registrierten Quellen gehören.

Es kann aber zu Situationen kommen, in denen Datenquellen nur für bestimmte Benutzer oder Mitglieder bestimmter Gruppen sichtbar sein sollen. Für diese Szenarien ist es in Data Catalog möglich, dass Benutzer den Besitz von im Katalog registrierten Datenassets übernehmen und dann die Sichtbarkeit ihrer Assets kontrollieren.

> [!NOTE]
> Die in diesem Artikel beschriebenen Funktionen sind nur in der Standard Edition von Azure Data Catalog verfügbar. Die Free Edition bietet keine Funktionen für die Besitzübernahme und die Einschränkung der Sichtbarkeit von Datenassets.
>
>

## <a name="managing-ownership-of-data-assets"></a>Verwalten des Besitzes von Datenassets
Standardmäßig haben Datenassets, die in Data Catalog registriert sind, keinen Besitzer. Alle Benutzer mit der Berechtigung zum Zugreifen auf den Katalog können diese Assets ermitteln und mit Anmerkungen versehen. Benutzer können den Besitz von besitzerlosen Datenassets übernehmen und die Sichtbarkeit ihrer Assets dann einschränken.

Wenn ein Datenasset in Data Catalog einen Besitzer hat, können nur vom Besitzer (oder mehreren Besitzern) autorisierte Benutzer das Asset ermitteln und die dazugehörigen Metadaten anzeigen. Außerdem kann das Asset nur von den Besitzern aus Data Catalog gelöscht werden.

> [!NOTE]
> Der Besitz wirkt sich in Data Catalog nur auf die darin gespeicherten Metadaten aus. Es werden keine Berechtigungen auf die zugrunde liegende Datenquelle übertragen.
>
>

### <a name="taking-ownership"></a>Festlegen des Besitzes
Benutzer können den Besitz von Datenassets übernehmen, indem Sie im Data Catalog-Portal die Option „Besitz übernehmen“ wählen. Zum Übernehmen des Besitzes eines besitzerlosen Datenassets sind keine besonderen Berechtigungen erforderlich. Alle Benutzer können zum Besitzer eines besitzerlosen Datenassets werden.

### <a name="adding-owners-and-co-owners"></a>Hinzufügen von Besitzern und Mitbesitzern
Wenn ein Datenasset bereits einen Besitzer hat, können Benutzer nicht einfach den Besitz übernehmen. Stattdessen müssen sie von einem bestehenden Besitzer als Mitbesitzer hinzugefügt werden. Alle Besitzer können weitere Benutzer oder Sicherheitsgruppen als Mitbesitzer hinzufügen.

> [!NOTE]
> Es ist eine bewährte Methode, für Datenassets mindestens zwei Personen als Besitzer zu verwenden.
>
>

### <a name="removing-owners"></a>Entfernen von Besitzern
Genauso wie jeder Assetbesitzer Mitbesitzer hinzufügen kann, können Besitzer auch alle Mitbesitzer entfernen.

Wenn ein Assetbesitzer sich selbst als Besitzer entfernt, kann er das Asset nicht mehr verwalten. Sobald sich ein Assetbesitzer als Besitzer entfernt und keine anderen Mitbesitzer vorhanden sind, wird das Asset in den besitzerlosen Zustand versetzt.

## <a name="visibility"></a>Sichtbarkeit
Besitzer von Datenassets können die Sichtbarkeit der Datenassets in ihrem Besitz steuern. Zum Einschränken der Sichtbarkeit aus der Standardeinstellung, bei der alle Data Catalog-Benutzer das Datenasset ermitteln und anzeigen können, kann der Assetbesitzer die Sichtbarkeitseinstellung in den Eigenschaften des Assets von „Jeder“ in „Besitzer und diese Benutzer“ ändern. Besitzer können dann bestimmte Benutzer und Sicherheitsgruppen hinzufügen.

> [!NOTE]
> Nach Möglichkeit sollten der Assetbesitz und die Sichtbarkeitsberechtigungen Sicherheitsgruppen und nicht einzelnen Benutzern zugewiesen werden.
>
>

## <a name="catalog-administrators"></a>Catalog-Administratoren
Bei Data Catalog-Administratoren handelt es sich implizit um Mitbesitzer aller Assets im Katalog. Assetbesitzer können die Sichtbarkeit für Catalog-Administratoren nicht entfernen, und Administratoren können den Besitz und die Sichtbarkeit für alle Datenassets in Data Catalog verwalten.

## <a name="summary"></a>Zusammenfassung
Beim Crowdsourcing-Modell von Data Catalog für die Ermittlung von Metadaten und Datenassets können alle Data Catalog-Benutzer eine Beitrag leisten und die Ermittlung durchführen. Die Standard Edition von Data Catalog enthält Funktionen für Besitz und Verwaltung, damit die Sichtbarkeit und Nutzung bestimmter Datenassets eingeschränkt werden kann.

