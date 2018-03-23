---
title: Technische Voraussetzungen für das Erstellen eines VM-Images für den Azure Marketplace | Microsoft Docs
description: Grundlegendes zu den Anforderungen für das Erstellen und Bereitstellen eines VM-Images im Azure Marketplace, damit dieses von anderen Benutzern erworben werden kann.
services: marketplace-publishing
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: 63c16966-0304-4b17-a715-368a0a5ccb2c
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: cf1f061c28dd0c106823d34ad39aac5e577c8b41
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/16/2018
---
# <a name="technical-prerequisites-for-creating-a-virtual-machine-image-for-the-azure-marketplace"></a>Technische Voraussetzungen für das Erstellen eines VM-Images für den Azure Marketplace
Lesen Sie die Informationen zum Prozess vor Beginn sorgfältig durch, um nachvollziehen zu können, wo und warum die einzelnen Schritte ausgeführt werden. Bereiten Sie nach Möglichkeit Ihre Unternehmensinformationen und andere Daten vor, laden Sie die erforderlichen Tools herunter, und/oder erstellen Sie technische Komponenten, bevor Sie mit der Angebotserstellung beginnen. Diese Schritte werden in diesem Artikel erläutert.  

## <a name="download-needed-tools--applications"></a>Herunterladen der erforderlichen Tools und Anwendungen
Sie sollten die folgenden Aufgaben ausgeführt haben, bevor Sie mit dem hier beschriebenen Verfahren beginnen:

* Installieren Sie abhängig vom Zielbetriebssystem die [Azure PowerShell-Cmdlets](https://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/WindowsAzurePowershellGet.3f.3f.3fnew.appids) oder die [Linux-Befehlszeilenschnittstelle](https://go.microsoft.com/fwlink/?LinkId=253472&clcid=0x409) über die [Downloadseite von Azure](https://azure.microsoft.com/downloads/).
* Laden Sie den Azure Storage-Explorer von CodePlex herunter.
* Laden Sie „Certification Test Tool for Azure Certified“ herunter:
  * [http://go.microsoft.com/fwlink/?LinkID=526913](http://go.microsoft.com/fwlink/?LinkID=526913). Zum Ausführen des Zertifizierungstools benötigen Sie einen Windows-basierten Computer. Wenn kein Windows-basierter Computer zur Verfügung steht, können Sie das Tool unter Verwendung eines Windows-basierten virtuellen Computers in Azure ausführen.

## <a name="platforms-supported"></a>Unterstützte Plattformen
Sie können Azure-basierte virtuelle Computer unter Windows oder Linux erstellen. Für einige Aufgaben im Veröffentlichungsprozess – beispielsweise für das Erstellen einer mit Azure kompatiblen virtuellen Festplatte (Virtual Hard Disk, VHD) – werden je nach verwendetem Betriebssystem andere Tools und Schritte verwendet:  

* Bei Verwendung von Linux finden Sie weitere Informationen im [Leitfaden zum Veröffentlichen von VM-Images](marketplace-publishing-vm-image-creation.md)im Abschnitt „Erstellen einer mit Azure kompatiblen VHD (Linux-basiert)“.
* Bei Verwendung von Windows finden Sie weitere Informationen im [Leitfaden zum Veröffentlichen von VM-Images](marketplace-publishing-vm-image-creation.md)im Abschnitt „Erstellen einer mit Azure kompatiblen VHD (Windows-basiert)“.

> [!NOTE]
> Sie benötigen Zugriff auf einen Windows-basierten Computer, um folgende Aufgaben auszuführen:
> 
> * Ausführen des Tools für die Zertifizierungsprüfung.
> * Erstellen der VHD-SAS-URL zum Übermitteln der VHD-Zertifizierung.
> 
> 

## <a name="develop-your-vhd"></a>Entwickeln Ihrer virtuellen Festplatte
Azure-VHDs können in der Cloud oder lokal entwickelt werden:

* Bei der cloudbasierten Entwicklung werden alle Entwicklungsschritte remote ausgeführt, die VHDs befinden sich in Azure.
* Für die lokale Entwicklung müssen Sie eine VHD herunterladen und mithilfe der lokalen Infrastruktur entwickeln. Diese Vorgehensweise ist zwar möglich, wird jedoch nicht empfohlen. Beachten Sie, dass bei einer lokalen Entwicklung für Windows oder SQL geeignete lokale Lizenzschlüssel vorliegen müssen. SQL Server kann nach dem Erstellen eines virtuellen Computers nicht mehr eingeschlossen oder installiert werden. Außerdem muss Ihr Angebot auf einem genehmigten SQL-Image aus dem Azure-Portal basieren. Wenn Sie sich für eine lokale Entwicklung entscheiden, müssen einige Schritte anders ausgeführt werden als bei der Entwicklung in der Cloud. Informationen hierzu finden Sie unter [Erstellen eines lokalen VM-Image](marketplace-publishing-vm-image-creation-on-premise.md).

[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
