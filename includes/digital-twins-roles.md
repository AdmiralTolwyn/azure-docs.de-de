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
ms.openlocfilehash: 53733a3fb33aeb99321d4c94a64225ffa84eb424
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307208"
---
Die folgende Tabelle beschreibt die in Azure Digital Twins verfügbaren Rollen:

| **Rolle** | **Beschreibung** | **Bezeichner** |
| --- | --- | --- |
| Space Administrator | *ERSTELLEN*-, *LESEN*-, *AKTUALISIEREN*- und *LÖSCHEN*-Berechtigung für den angegebenen Raum und alle Knoten darunter. Globale Berechtigung. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Benutzeradministrator| *ERSTELLEN*-, *LESEN*-, *AKTUALISIEREN*- und *LÖSCHEN*-Berechtigung für Benutzer und benutzerbezogene Objekte. *LESEN*-Berechtigung für Räume. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Device Administrator | *ERSTELLEN*-, *LESEN*-, *AKTUALISIEREN*- und *LÖSCHEN*-Berechtigung für Geräte und gerätebezogene Objekte. *LESEN*-Berechtigung für Räume. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Key Administrator | *ERSTELLEN*-, *LESEN*-, *AKTUALISIEREN*- und *LÖSCHEN*-Berechtigung für Zugriffsschlüssel. *LESEN*-Berechtigung für Räume. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token Administrator |  *ERSTELLEN*- und *AKTUALISIEREN*-Berechtigung für Zugriffsschlüssel. *LESEN*-Berechtigung für Räume. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Benutzer |  *LESEN*-Berechtigung für Räume, Sensoren und Benutzer, einschließlich der dazugehörigen Objekte. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Support Specialist |  *LESEN*-Berechtigung für alle Objekte außer Zugriffsschlüsseln. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Device Installer | *LESEN*- und *AKTUALISIEREN*-Berechtigung für Geräte und Sensoren, einschließlich der dazugehörigen Objekte. *LESEN*-Berechtigung für Räume. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Gatewaygerät | *ERSTELLEN*-Berechtigung für Sensoren. *LESEN*-Berechtigung für Geräte und Sensoren, einschließlich der dazugehörigen Objekte. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |