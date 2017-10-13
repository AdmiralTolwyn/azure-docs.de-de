---
title: "Auflisten aller Azure Import/Export-Aufträge | Microsoft Docs"
description: "Erfahren Sie, wie Sie alle Aufträge des Azure Import/Export-Diensts in einem Abonnement auflisten."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: f2e619be-1bbd-4a54-9472-9e2f70a83b64
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 1977bfc0e516088310f45ecdd960287eeed2c2d8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/11/2017
---
# <a name="enumerating-jobs-in-the-azure-importexport-service"></a>Auflisten von Aufträgen im Azure Import/Export-Dienst
Rufen Sie den Vorgang [List Jobs](/rest/api/storageimportexport/jobs#Jobs_List) auf, um alle Aufträge in einem Abonnement aufzulisten. `List Jobs` gibt eine Liste der Aufträge sowie die folgenden Attribute zurück:

-   Typ des Auftrags (Import oder Export)

-   Aktueller Auftragsstatus

-   Zugeordnetes Speicherkonto des Auftrags

## <a name="next-steps"></a>Nächste Schritte

* [Verwenden der REST-API des Import/Export-Diensts](storage-import-export-using-the-rest-api.md)
