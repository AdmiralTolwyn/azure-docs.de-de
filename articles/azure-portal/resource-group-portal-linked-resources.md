---
title: "Verwandte und verknüpfte Ressourcen im Kachelkatalog"
description: "Informationen zu verwandten und verknüpften Ressourcen, die im Kachelkatalog des Azure-Vorschauportals angezeigt werden."
services: azure-portal
documentationcenter: 
author: adamabdelhamed
manager: wpickett
editor: 
ms.assetid: dba96d29-f518-49c8-bfd2-f14cecb44cbf
ms.service: azure-portal
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2015
ms.author: adamab
translationtype: Human Translation
ms.sourcegitcommit: ebc5dbf790ca6012cfe9a7ea9ccee9fdacb46ffd
ms.openlocfilehash: b4c8ad69674c553f8b521a85765edbad0ce1faf2


---
# <a name="related-and-linked-resources-in-the-tile-gallery"></a>Verwandte und verknüpfte Ressourcen im Kachelkatalog
Im Kachelkatalog können Sie Kacheln für eine bestimmte Ressource suchen und sie auf das aktuelle Blatt ziehen. Mit dem Kachelkatalog können Sie Verwaltungsansichten erstellen, die mehrere Ressourcen umfassen. Für eine angegebene Ressource umfassen die verwandten Ressourcen alle Ressourcen, die der gleichen Ressourcengruppe angehören, sowie alle Ressourcen, die mit der Ressource verknüpft sind.

## <a name="linked-resources-in-azure-resource-manager"></a>Verknüpfte Ressourcen im Azure-Ressourcen-Manager
Die Verknüpfung ist eine Funktion des Azure-Ressourcen-Managers.  Mit der Verknüpfung können Sie Beziehungen zwischen Ressourcen deklarieren, selbst wenn diese sich nicht in der gleichen Ressourcengruppe befinden. Die Verknüpfung wirkt sich weder auf die Laufzeit von Ressourcen, auf die Abrechnung noch auf den rollenbasierten Zugriff aus.  Es handelt sich einfach nur um eine Methode zum Darstellen von Beziehungen, sodass Tools wie der Kachelkatalog über vielfältige Verwaltungsfunktionen verfügen.  Die Tools können die Verknüpfungen mithilfe der Verknüpfungs-API prüfen und auch benutzerdefinierte Beziehungsverwaltungsfunktionen umfassen. 

## <a name="how-do-i-link-my-resources"></a>Wie können Ressourcen verknüpft werden?
Wenn Sie Ressourcen im Portal oder durch Bereitstellen einer Vorlage über Azure PowerShell oder die Azure-Befehlszeilenschnittstelle erstellen, werden für einige abhängige Ressourcen automatisch Verknüpfungen erstellt. Sie können Ressourcen zudem programmgesteuert verknüpfen, indem Sie die [REST-API "Linked Resources"](https://msdn.microsoft.com/library/azure/mt238499.aspx) verwenden oder die Beziehungen in der Vorlage deklarieren. Eine umfassende Beschreibung der Arbeit mit verknüpften Ressourcen finden Sie unter [Verknüpfen von Ressourcen im Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-link-resources.md).

## <a name="next-steps"></a>Nächste Schritte
* Eine Einführung in das Schreiben von Vorlagen für den Azure-Ressourcen-Manager finden Sie unter [Erstellen von Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md).
* Ausführliche Informationen zum Erstellen von Verknüpfungen zwischen Ressourcen finden Sie unter [Verknüpfen von Ressourcen im Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-link-resources.md).
* Weitere Informationen zum Arbeiten mit Ressourcengruppen über das Vorschauportal finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](../azure-resource-manager/resource-group-portal.md).




<!--HONumber=Feb17_HO2-->


