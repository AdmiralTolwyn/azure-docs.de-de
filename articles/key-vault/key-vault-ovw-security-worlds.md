---
ms.assetid: ''
title: Azure Key Vault-Sicherheitsumgebungen (Security Worlds) | Microsoft Docs
ms.service: key-vault
ms.topic: conceptual
author: bryanla
ms.author: bryanla
manager: barbkess
ms.date: 07/03/2017
ms.openlocfilehash: 3dea506958bbe41f1c387959bb1188696e57043a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107531"
---
# <a name="azure-key-vault-security-worlds-and-geographic-boundaries"></a>Sicherheitsumgebungen und geografische Grenzen von Azure Key Vault

Azure Key Vault ist ein mehrinstanzenfähiger Dienst, der einen Pool von Hardwaresicherheitsmodulen (Hardware Security Modules, HSMs) an jedem Azure-Standort verwendet. 

Alle HSMs an Azure-Standorten in der gleichen geografischen Region verwenden dieselbe kryptografische Grenze (Thales Security World). Beispielsweise verwenden „USA, Osten“ und „USA, Westen“ dieselbe Sicherheitsumgebung, weil sie dem geografischen Standort USA angehören. Ebenso verwenden alle Azure-Standorte in Japan und alle Azure-Standorte in Australien, Indien und so weiter jeweils die gleiche Sicherheitsumgebung. 

## <a name="backup-and-restore-behavior"></a>Sichern und Wiederherstellen – Verhalten

Eine Sicherung, die von einem Schlüssel aus einem Schlüsseltresor an einem Azure-Standort erstellt wurde, kann in einem Schlüsseltresor an einem anderen Azure-Standort wiederhergestellt werden, sofern die zwei folgenden Bedingungen erfüllt sind:

- Beide Azure-Standorte gehören derselben geografischen Region an.
- Beide Schlüsseltresore gehören zum selben Azure-Abonnement.

Beispiel: Eine Sicherung, die von einem bestimmten Abonnement von einem Schlüssel in einem Schlüsseltresor in Westindien erstellt wurde, kann nur in einem anderen Schlüsseltresor im selben Abonnement und in derselben geografischen Region (Westindien, Zentralindien oder Südindien) wiederhergestellt werden.

## <a name="regions-and-products"></a>Regionen und Produkte

- [Azure-Regionen](https://azure.microsoft.com/regions/)
- [Microsoft-Produkte nach Region](https://azure.microsoft.com/regions/services/)

Regionen sind Sicherheitsumgebungen zugeordnet, die als Hauptüberschriften in den Tabellen angezeigt werden:

Im Artikel zu Produkten nach Region enthält z.B. die Registerkarte **Amerika** Registerkarte „USA, OSTEN“, „USA, MITTE“ und „USA, WESTEN“, die alle der Region „Amerika“ zugeordnet sind. 

>[!NOTE]
>Ausnahmen bilden USA DOD OSTEN und USA DOD MITTE, die jeweils über eigene Sicherheitsumgebungen verfügen. 

Ähnlich sind auf der Registerkarte **Europa** sowohl „EUROPA, NORDEN“ als auch „EUROPA, WESTEN“ der Region „Europe“ zugeordnet. Dasselbe gilt auch für die Registerkarte **Asien-Pazifik**.



