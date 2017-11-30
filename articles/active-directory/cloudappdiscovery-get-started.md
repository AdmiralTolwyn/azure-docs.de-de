---
title: Einrichten des Cloud App Discovery-Diensts in Azure Active Directory | Microsoft Docs
description: "Suchen Sie nach und verwalten Sie Anwendungen mit Cloud App Discovery, um aussagekräftige Informationen zur Cloudverwendung und Schatten-IT bereitzustellen."
services: active-directory
keywords: Cloud App Discovery, Verwalten von Anwendungen
documentationcenter: 
author: curtand
manager: femila
tags: ignite
ms.assetid: db968bf5-22ae-489f-9c3e-14df6e1fef0a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: curtand
ms.reviewer: nigu
ms.openlocfilehash: 92cafe24fc3a038f2acd68ec21ec845316ef46de
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/27/2017
---
# <a name="set-up-cloud-app-discovery-in-azure-ad"></a>Einrichten von Cloud App Discovery in Azure AD

Cloud App Discovery in Azure AD basiert jetzt auf der Integration von verfügbaren Daten aus Microsoft Cloud App Security. Cloud App Discovery vergleicht Ihre Datenverkehrsprotokolle mit dem Cloud App Security-Katalog, in dem mehr als 15.000 Cloud-Apps enthalten sind, um aktuelle Informationen zur Cloudverwendung und Schatten-IT für Sie bereitzustellen. In diesem Artikel wird der Einrichtungsvorgang beschrieben. Außerdem enthält er Links zu den detaillierten Informationen zu jedem Schritt. Zudem stellt er Firewall- und Proxyinformationen sowie Protokolldateiunterstützung bereit.

## <a name="prerequisites"></a>Voraussetzungen

* Ihre Organisation muss eine Azure AD Premium P1-Lizenz haben, um dieses Produkt nutzen zu können. Weitere Informationen finden Sie unter [Azure Active Directory – Preise](https://azure.microsoft.com/pricing/details/active-directory/).
* Um Cloud App Discovery einzurichten, müssen Sie ein globaler Administrator oder ein Benutzer mit Leseberechtigung für Sicherheitsfunktionen in Azure Active Directory sein.

## <a name="setup-steps"></a>Einrichtungsschritte

1. [Richten Sie Momentaufnahmeberichte ein](cloudappdiscovery-set-up-snapshots.md), um das Protokollformat zu überprüfen und sicherzustellen, dass Ihre Protokolle Cloud App Discovery verwendbare Informationen bereitstellen. Sie können auch für Ad-hoc-Einsicht in Datenverkehrsprotokolle sorgen, die Sie manuell von Firewalls und Proxyservern hochladen.

2. [Richten Sie fortlaufende Berichte ein](https://docs.microsoft.com/cloud-app-security/discovery-docker), um alle Protokolle zu analysieren, die über Ihr Netzwerk mithilfe des Cloud App Security-Protokollsammlers weitergeleitet werden. Damit können Sie neue Apps und Nutzungstrends identifizieren.

3. Wenn Ihre Protokolle derzeit nicht unterstützt werden, [richten Sie einen benutzerdefinierten Protokollparser ein](https://docs.microsoft.com/cloud-app-security/custom-log-parser), um die Analyse durch Cloud App Discovery zu ermöglichen.
  
## <a name="log-processing-flow"></a>Protokollverarbeitungsfluss

Je nach Datenmenge kann es ein paar Minuten oder bis zu mehreren Stunden dauern, bis Berichte erstellt werden. Folgendes wird analysiert:

* **Upload**: Web-Datenverkehrsprotokolle werden aus Ihrem Netzwerk ins Portal hochgeladen.
* **Analysen**: Cloud App Security analysiert und extrahiert Datenverkehrsdaten aus den Datenverkehrsprotokollen mit einem dedizierten Parser für jede Datenquelle.
* **Analysen**: Datenverkehrsdaten werden anhand des Cloud App Security-Katalogs analysiert, um mehr als 15.000 Cloud-Apps zu identifizieren. Auch aktive Benutzer und IP-Adressen werden im Rahmen der Analyse ermittelt.
* **Bericht erstellen**: Cloud App Security erstellt einen Bericht aus den Daten, die aus den Protokolldateien extrahiert und für Cloud App Discovery verfügbar gemacht wurden.

> [!NOTE]
> * Daten aus fortlaufenden Berichten werden zweimal täglich analysiert.
> * Der Protokollsammler komprimiert Daten, bevor sie hochgeladen werden. Der ausgehende Datenverkehr auf dem Protokollsammler beläuft sich auf ca. 10 % der Größe der empfangenen Datenverkehrsprotokolle.

## <a name="using-traffic-logs-for-cloud-app-discovery"></a>Verwenden von Datenverkehrsprotokollen für Cloud App Discovery

Cloud App Discovery verwendet die Daten aus Ihren Datenverkehrsprotokollen. Je mehr Details Sie dem Protokoll hinzufügen, desto besser ist der Einblick, den Sie erhalten. Cloud App Discovery erfordert Daten über den Webdatenverkehr mit den folgenden Attributen:

* Datum der Transaktion
* Quell-IP-Adresse
* Quellbenutzer **empfohlen**
* IP-Zieladresse
* Ziel-URL **empfohlen** (URLs sind bei der Erkennung von Cloud-Apps genauer als IP-Adressen)
* Gesamtdatenmenge
* Die Menge der hoch- oder heruntergeladen Daten erlaubt einen Einblick in Nutzungsmuster von Cloud-Apps
* Ausgeführte Aktion (zugelassen/blockiert)

Cloud App Discovery kann keine Attribute anzeigen oder analysieren, die nicht in Ihren Protokollen enthalten sind. Beispielsweise enthält das Standardprotokollformat **Cisco ASA Firewall** weder die **Menge der hochgeladenen Bytes pro Transaktion**, noch den **Benutzernamen** oder die **Ziel-URL**, sondern nur die Ziel-IP-Adresse. Daher haben Sie aus dieser Datenquelle möglicherweise weniger Einblicke in die Cloud-Apps. Legen Sie für Cisco ASA-Firewalls die Informationsebene auf 6.1 fest.

Um erfolgreich einen Cloud App Discovery-Bericht zu erstellen, müssen Ihre Datenverkehrsprotokolle den folgenden Bedingungen entsprechen:

1.  Die Datenquelle ist [eine unterstützte Firewall oder ein Proxyserver](#supported-firewalls-and-proxies).
2.  Das Protokollformat entspricht dem erwarteten Standardformat. Dies wird zum Zeitpunkt des Uploads überprüft. Informationen zum Optimieren des Protokollformats finden Sie unter [Create snapshot Cloud App Discovery reports (Erstellen von Cloud App Security-Berichten mit Momentaufnahmen)](cloudappdiscovery-set-up-snapshots.md).
3.  Die Ereignisse sind nicht älter als 90 Tage.
4.  Die Protokolldatei ist gültig und enthält Informationen zum ausgehenden Datenverkehr.

## <a name="supported-firewalls-and-proxy-servers"></a>Unterstützte Firewalls und Proxyserver

* Barracuda – Web-App-Firewall (W3C)
* Blue Coat Proxy SG – Zugriffsprotokoll (W3C)
* Check Point
* Cisco ASA Firewall (Legen Sie die Informationsebene für Cisco ASA-Firewalls auf 6 fest)
* Cisco IronPort WSA
* Cisco ScanSafe
* Cisco Meraki – URLs log
* Clavister NGFW (Syslog)
* Dell Sonicwall
* Fortinet Fortigate
* Juniper SRX
* Juniper SSG
* McAfee Secure Web Gateway
* Microsoft Forefront Threat Management Gateway (W3C)
* Palo Alto series Firewall
* Sophos SG
* Sophos Cyberoam
* Squid (Common)
* Squid (Native)
* Websense – Web Security Solutions – Detailbericht (CSV)
* Websense – Web Security Solutions – Internetaktivitätsprotokoll (CEF)
* Zscaler

> [!NOTE]
> Cloud App Discovery unterstützt sowohl IPv4- als auch IPv6-Adressen.

Wenn Ihr Protokoll nicht unterstützt wird, wählen Sie **Other** (Andere) als **Datenquelle** aus, und geben Sie das Gerät und das Protokoll an, die Sie hochladen möchten. Ihr Protokoll wird vom Cloud App Security-Cloudanalyseteam geprüft. Wenn der Support für Ihren Protokolltyp hinzugefügt wird, benachrichtigen wir Sie. Sie können stattdessen jedoch einen benutzerdefinierten Parser definieren, der Ihrem Protokollformat entspricht. Weitere Informationen finden Sie unter [Use a custom log parser (Verwenden eines benutzerdefinierten Protokollparsers)](https://docs.microsoft.com/cloud-app-security/custom-log-parser).

## <a name="data-attributes-according-to-vendor-documentation"></a>Datenattribute (gemäß der Herstellerdokumentation)

| Datenquelle         | URL der Ziel-App | IP-Adresse der Ziel-App | Benutzername | Ursprungs-IP-Adresse | Gesamter Datenverkehr | Hochgeladene Bytes |
|-----------------------------------------|----------------|---------------|----------|-----------|---------------|----------------|
| Barracuda                               | **Ja**        | **Ja**       | **Ja**  | **Ja**   | Nein            | Nein             |
| Blue Coat                               | **Ja**        | Nein            | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Prüfpunkt                              | Nein             | **Ja**       | Nein       | **Ja**   | Nein            | Nein             |
| Cisco ASA                               | Nein             | **Ja**       | Nein       | **Ja**   | **Ja**       | Nein             |
| Cisco FWSM                              | Nein             | **Ja**       | Nein       | **Ja**   | **Ja**       | Nein             |
| Cisco Ironport WSA                      | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Cisco Meraki                            | **Ja**        | **Ja**       | Nein       | **Ja**   | Nein            | Nein             |
| Clavister NGFW (Syslog)                 | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Dell SonicWall                          | **Ja**        | **Ja**       | Nein       | **Ja**   | **Ja**       | **Ja**        |
| Fortigate                               | Nein             | **Ja**       | Nein       | **Ja**   | **Ja**       | **Ja**        |
| Juniper SRX                             | Nein             | **Ja**       | Nein       | **Ja**   | **Ja**       | **Ja**        |
| Juniper SSG                             | Nein             | **Ja**       | Nein       | **Ja**   | **Ja**       | **Ja**        |
| McAfee SWG                              | **Ja**        | Nein            | Nein       | **Ja**   | **Ja**       | **Ja**        |
| MS TMG                                  | **Ja**        | Nein            | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Palo Alto Networks                      | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Sophos                                  | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | Nein             |
| Squid (Common)                          | **Ja**        | Nein            | **Ja**  | **Ja**   | Nein            | **Ja**        |
| Squid (Native)                          | **Ja**        | Nein            | **Ja**  | **Ja**   | Nein            | **Ja**        |
| Websense – Untersuchungsbericht (CSV)   | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Websense – Internetaktivitätsprotokoll (CEF)  | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |
| Zscaler                                 | **Ja**        | **Ja**       | **Ja**  | **Ja**   | **Ja**       | **Ja**        |


## <a name="next-steps"></a>Nächste Schritte
Setzen Sie mit den folgenden Links die Einrichtung von Cloud App Discovery in Azure AD fort:

* [Erstellen von Momentaufnahmenberichten](cloudappdiscovery-set-up-snapshots.md)
* [Konfigurieren des automatischen Uploads von Protokollen für kontinuierliche Berichte](https://docs.microsoft.com/cloud-app-security/discovery-docker)
* [Verwenden eines benutzerdefinierten Protokollparsers](https://docs.microsoft.comcommit/cloud-app-security/custom-log-parser)
