---
title: include file
description: include file
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/20/2019
ms.custom: include file
ms.openlocfilehash: e22ca39536e8e5d216d786c9d3c473f13431859f
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307242"
---
Rollenbasierte Zugriffssteuerung ist eine vererbungsbasierte Sicherheitsstrategie zum Verwalten von Zugriffen, Berechtigungen und Rollen. Untergeordnete Rollen erben Berechtigungen von übergeordneten Rollen. Berechtigungen können auch zugewiesen werden, ohne dass sie von einer übergeordneten Rolle geerbt wurden. Sie können auch zugewiesen werden, um eine Rolle nach Bedarf anzupassen.

Beispielsweise benötigt ein Bereichsadministrator möglicherweise globalen Zugriff zum Ausführen aller Vorgänge für einen angegebenen Bereich. Der Zugriff schließt alle Knoten unterhalb oder innerhalb des Bereichs ein. Ein Geräte-Installationsprogramm benötigt möglicherweise nur Berechtigungen zum *Lesen* und *Aktualisieren* für Geräte und Sensoren.

In jedem Fall werden Rollen *genau die Zugriffsberechtigungen* erteilt, die sie zum Erfüllen ihrer Aufgaben benötigen und die dem Prinzip der geringsten Rechte entsprechen. Nach diesem Prinzip gilt für eine Identität *nur Folgendes*:

* Der Umfang an Zugriff, den sie zum Ausführen ihrer Aufgabe benötigt.
* Eine Rolle, die für das Ausführen ihrer Aufgabe geeignet und darauf beschränkt ist.

>[!IMPORTANT]
> Befolgen Sie immer das Prinzip der geringsten Rechte.

Zwei weitere wichtige Aspekte einer rollenbasierten Zugriffssteuerung, die zu beachten sind:

> [!div class="checklist"]
> * Überprüfen Sie in regelmäßigen Abständen Rollenzuweisungen, um sicherzustellen, dass jede Rolle die richtigen Berechtigungen hat.
> * Bereinigen Sie Rollen und Zuweisungen, sobald Rollen oder Zuweisungen von Personen geändert wurden.