---
title: Azure Data Catalog-Versionshinweise | Microsoft Docs
description: "Versionshinweise für Azure Data Catalog"
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 3aca9c49-45a4-4352-92e6-bd25ee3eacf7
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 08/15/2017
ms.author: maroche
ms.openlocfilehash: d3db9bee0558cac5fb4f5b8fb4ab67a35ce0f141
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2017
---
# <a name="azure-data-catalog-release-notes"></a>Versionshinweise für Azure Data Catalog
## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Hinweise zur Azure Data Catalog-Version vom 20. November 2015
### <a name="opening-data-sources-in-power-bi-desktop"></a>Öffnen von Datenquellen in Power BI Desktop
Bei Verwendung der Option „Open in Power BI Desktop“ im Portal **Azure Data Catalog** tritt in Power BI Desktop unter Umständen eines von zwei Problemen auf:

* Es wird ein Dialogfeld mit dem Titel „Dokument konnte nicht geöffnet werden“ angezeigt.
* Power BI Desktop wird geöffnet, aber die Datei scheint leer zu sein.

In beiden Fällen lässt sich das Problem durch Herunterladen und Installieren der neuesten Version von Power BI Desktop von [PowerBI.com](https://powerbi.com)beheben.

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Hinweise zur Azure Data Catalog-Version vom 13. November 2015
### <a name="registering-and-connecting-to-teradata"></a>Registrieren und Herstellen einer Verbindung mit Teradata
Beim Herstellen einer Verbindung mit Teradata-Datenquellen müssen Benutzer den passenden Teradata-ODBC-Treiber für die verwendete Software (32 Bit oder 64 Bit) installieren.

Zum Zeitpunkt dieser ADC-Veröffentlichung ist der neueste [Teradata-ODBC-Treiber für Windows (Version 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) mit Office 2013, aber nicht mit Office 2016 kompatibel.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Hinweise für die Azure Data Catalog-Version vom 13. Juli 2015
### <a name="registering-and-connecting-to-oracle-database"></a>Registrieren bei Oracle Database und Herstellen einer Verbindung
Beim Herstellen einer Verbindung mit Oracle Database-Datenquellen müssen Benutzer die richtigen Oracle-Treiber installieren, die der Bitanzahl (32 Bit oder 64 Bit) der verwendeten Software entsprechen.

* Beim Registrieren von Oracle-Datenquellen auf einem 32-Bit-Windows-Computer werden die 32-Bit-Oracle-Treiber verwendet.
* Beim Registrieren von Oracle-Datenquellen auf einem Computer mit 64-Bit-Windows werden die 64-Bit-Oracle-Treiber verwendet.
* Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 32-Bit-Version von Microsoft Office (auch unter 64-Bit-Windows) werden die 32-Bit-Oracle-Treiber verwendet.
* Beim Verbinden mit Oracle-Datenquellen mithilfe von Excel auf einem Computer mit der 64-Bit-Version von Microsoft Office werden die 64-Bit-Oracle-Treiber verwendet.

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registrieren bei SQL Server Reporting Services und Herstellen einer Verbindung
Die Unterstützung von SQL Server Reporting Services (SSRS)-Datenquellen ist derzeit auf Server im systemeigenen Modus beschränkt. Die Unterstützung für SSRS im SharePoint-Modus wird in einer späteren Version implementiert.

### <a name="opening-data-assets-in-excel"></a>Öffnen von Datenassets in Excel
Beim Öffnen von Datenressourcen aus dem Portal **Azure Data Catalog** in Microsoft Excel wird Benutzern unter Umständen ein Dialogfeld mit einem **Sicherheitshinweis für Microsoft Excel** angezeigt. Hierbei handelt es sich um einen Standardvorgang. Benutzer können **Aktivieren** auswählen, um den Vorgang fortzusetzen.

Weitere Informationen finden Sie unter [Aktivieren oder Deaktivieren von Sicherheitswarnungen zu Verknüpfungen und Dateien von verdächtigen Websites](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Proxy- und Richtlinienkonfiguration und Datenquellenregistrierung
Es kann vorkommen, dass sich Benutzer beim Azure Data Catalog-Portal anmelden können, jedoch eine Fehlermeldung erhalten, die ihre Anmeldung beim Tool zum Registrieren von Datenquellen verhindert.

Für dieses Problem sind zwei Ursachen möglich:

**Ursache 1: Konfiguration der Active Directory-Verbunddienste** Das Tool zum Registrieren von Datenquellen verwendet die Formularauthentifizierung, um Benutzeranmeldungen anhand von Active Directory zu überprüfen. Für eine erfolgreiche Anmeldung muss die Formularauthentifizierung durch einen Active Directory-Administrator in der globalen Authentifizierungsrichtlinie aktiviert sein.

In manchen Fällen tritt dieser Fehler möglicherweise nur dann auf, wenn der Benutzer im Unternehmensnetzwerk ist oder wenn der Benutzer von außerhalb des Unternehmensnetzwerks eine Verbindung herstellt. Mit der globalen Authentifizierungsrichtlinie können separate Authentifizierungsmethoden für Intranet- und Extranetverbindungen aktiviert werden. Anmeldefehler können auftreten, wenn die Formularauthentifizierung nicht für das Netzwerk aktiviert ist, aus dem der Benutzer eine Verbindung herstellt.

Weitere Informationen finden Sie unter [Konfigurieren von Authentifizierungsrichtlinien](https://technet.microsoft.com/library/dn486781.aspx).

**Ursache 2: Netzwerkproxykonfiguration** Wenn das Unternehmensnetzwerk einen Proxyserver verwendet, kann das Registrierungstool möglicherweise aufgrund des Proxys keine Verbindung zu Azure Active Directory herstellen. Um die Funktion des Registrierungstools sicherzustellen, können Benutzer die Konfigurationsdatei bearbeiten , indem sie der Datei diesen Abschnitt hinzufügen:

      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Starten Sie das Registrierungstool, um die Datei "RegistrationTool.exe.config" zu suchen, und öffnen Sie den Windows Task-Manager. Klicken Sie im Task-Manager auf der Registerkarte "Details" mit der rechten Maustaste auf "RegistrationTool.exe", und wählen Sie im Popupmenü "Dateispeicherort öffnen" aus.
