---
title: Oracle-Lösungen in Microsoft Azure | Microsoft-Dokumentation
description: Informationen zu Optionen zum Bereitstellen von Oracle-Anwendungen und -Lösungen in Microsoft Azure, einschließlich der vollständigen Ausführung in einer Azure-Infrastruktur oder der Verwendung von cloudübergreifender Konnektivität mit einer Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743369"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Übersicht über Oracle-Anwendungen und -Lösungen in Azure

Dieser Artikel führt Funktionen zum Ausführen von Oracle-Lösungen mithilfe der Azure-Infrastruktur ein. Außerdem finden Sie detaillierte Einführungen zu den im Azure Marketplace verfügbaren [Oracle VM-Images](oracle-vm-solutions.md) sowie zur Vorschaufunktion zum [Verbinden von Azure mit der Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Oracle-Datenbanken in einer Azure-Infrastruktur

Oracle-Datenbanken führen Sie in einer Azure-Infrastruktur mithilfe von Linux-Images aus, die im Azure Marketplace verfügbar sind:

* Oracle Database 12.1, 12.2 und 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 und 18.3 Standard Edition 

Sie können als Basis für ihre Lösungen auch ein benutzerdefiniertes Image verwenden, das sie in Azure von Grund auf neu erstellen, oder sie können ein benutzerdefiniertes Image aus ihrer lokalen Umgebung hochladen.

Sie können sie optional mit mehreren angefügten Datenträgern konfigurieren und die Datenbankleistung verbessern, indem Sie Oracle Automated Storage Management (ASM) installieren.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Anwendungen unter Oracle Linux und WebLogic Server

Unternehmensanwendungen führen Sie in Azure unter unterstützten Oracle-Betriebssystemen aus. Die folgenden Images sind im Azure Marketplace erhältlich:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 und 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Hochverfügbarkeits- und Notfallwiederherstellungsoptionen

* Für Hochverfügbarkeit konfigurieren Sie Oracle Data Guard, Active Data Guard oder GoldenGate in der Azure-Infrastruktur in Verbindung mit [Verfügbarkeitszonen](../../../availability-zones/az-overview.md).

* Verwenden Sie [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md), um die Notfallwiederherstellung für Ihre Oracle Linux-VMs in Azure sowie für Ihre lokalen oder physischen Server zu orchestrieren und zu verwalten. 

* Aktivieren Sie Oracle Real Application Cluster (RAC) in Azure mithilfe von [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integration von Azure in OCI (Vorschau)

Führen Sie Oracle-Anwendungen in der Azure-Infrastruktur aus mit bestehenden Verbindungen zu Back-End-Datenbanken in Oracle Cloud Infrastructure (OCI). Diese Lösung verwendet die folgenden Funktionen: 

* **Cloudübergreifende Netzwerke**: Verwenden Sie die direkte Verbindungen zwischen Azure ExpressRoute und Oracle FastConnect, um hohe Bandbreite und private Verbindungen mit niedrigen Wartezeiten zwischen der Anwendung und der Datenbankschicht herzustellen.
* **Integrierte Identität**: Richten Sie eine Verbundidentität zwischen Azure AD und Oracle IDCS ein, um eine einzige Identitätsquelle für die Lösungen zu erstellen. Aktivieren Sie einmaliges Anmelden, um Ressourcen in OCI und Azure zu verwalten.

### <a name="deploy-oracle-applications-on-azure"></a>Bereitstellen von Oracle-Anwendungen in Azure

Verwenden Sie Terraform-Vorlagen, um die Azure-Infrastruktur einzurichten, und installieren Sie Oracle-Anwendungen, die für die Ausführung in der cloudübergreifenden Konfiguration überprüft wurden und unterstützt werden:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Oracle Retail-Anwendungen
* Oracle Hyperion Financial Management

Stellen Sie außerdem benutzerdefinierte Anwendungen in Azure bereit, die eine Verbindung mit OCI und anderen Azure-Diensten herstellen.

### <a name="set-up-oracle-databases-in-oci"></a>Einrichten von Oracle-Datenbanken in OCI

Verwenden Sie Oracle Database Cloud Services (Autonomous Database, RAC, Exadata, DBaaS, Single Node) in Verbindung mit Oracle-Anwendungen, die in Azure ausgeführt werden. Weitere Informationen zu [OCI-Datenbankoptionen](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Lizenzierung

Die Bereitstellung von Oracle-Anwendungen in Azure basiert auf einem BYOL-Modell (Bring Your Own License). Es wird vorausgesetzt, dass Sie über eine ordnungsgemäße Lizenz für die Verwendung von Oracle-Software und über einen aktuellen Supportvertrag mit Oracle verfügen. Oracle bietet eine garantierte Lizenzmobilität von einer lokalen Verwendung zu Azure. Siehe in den [Häufig gestellten Fragen](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) zu Oracle und Azure.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Bereitstellen von [Oracle VM-Images](oracle-vm-solutions.md) in der Azure-Infrastruktur.

* Weitere Informationen zum [Verbinden von Azure mit OCI](oracle-oci-overview.md).