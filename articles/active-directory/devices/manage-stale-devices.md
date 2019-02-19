---
title: Verwalten von veralteten Geräten in Azure AD | Microsoft-Dokumentation
description: Aufgrund von verloren gegangenen, gestohlenen und beschädigten Geräten oder Neuinstallationen des Betriebssystems enthält Ihre Umgebung normalerweise veraltete Geräte. Erfahren Sie, wie veraltete Geräte aus der Datenbank der registrierten Geräte in Azure Active Directory (Azure AD) entfernt werden.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: c47b68cb173769eb677a5b1028d48c62f6edb4e9
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201634"
---
# <a name="how-to-manage-stale-devices-in-azure-ad"></a>Anleitung: Verwalten von veralteten Geräte in Azure AD

Idealerweise sollte die Registrierung von registrierten Geräten aufgehoben werden, wenn sie nicht mehr benötigt werden, um den Lebenszyklus abzuschließen. Ihre Umgebung enthält normalerweise aber noch veraltete Geräte, z.B. aufgrund von verloren gegangenen, gestohlenen und beschädigten Geräten oder Neuinstallationen des Betriebssystems. Als IT-Administrator wünschen Sie sich wahrscheinlich eine Methode zum Entfernen von veralteten Geräten, damit Sie Ihre Ressourcen für die Verwaltung der Geräte einsetzen können, für die dies tatsächlich erforderlich ist.

In diesem Artikel wird beschrieben, wie Sie veraltete Geräte in Ihrer Umgebung effizient verwalten.
  

## <a name="what-is-a-stale-device"></a>Was ist ein veraltetes Gerät?

Ein veraltetes Gerät ist ein Gerät, das für Azure AD registriert, aber dann nicht genutzt wurde, um über einen bestimmten Zeitraum auf Cloud-Apps zuzugreifen. Veraltete Geräte wirken sich aus folgenden Gründen auf die Verwaltung und Unterstützung Ihrer Geräte und Benutzer im Mandanten aus: 

- Doppelte Geräte können für Ihre Helpdesk-Mitarbeiter die Identifizierung des derzeit aktiven Geräts erschweren.

- Bei einer erhöhten Anzahl von Geräten werden unnötige Geräterückschreiben-Vorgänge erstellt, sodass sich die Dauer von AAD-Verbindungssynchronisierungen erhöht.

- Als allgemeine Hygienemaßnahme und zur Erfüllung der Konformität ist es ratsam, über einen bereinigten Gerätestand zu verfügen. 


Veraltete Geräte in Azure AD können eine Störung der allgemeinen Lebenszyklusrichtlinien für Geräte in Ihrer Organisation darstellen.



## <a name="detect-stale-devices"></a>Erkennen von veralteten Geräten

Da ein veraltetes Gerät als ein registriertes Gerät definiert ist, das nicht für den Zugriff auf Cloud-Apps über einen bestimmten Zeitraum verwendet wurde, ist für die Erkennung von veralteten Geräten eine zeitstempelbezogene Eigenschaft erforderlich. In Azure AD wird diese Eigenschaft als **ApproximateLastLogonTimestamp** oder **Aktivitätszeitstempel** bezeichnet. Wenn das Delta zwischen dem Jetzt-Wert und dem Wert des **Aktivitätszeitstempels** den Zeitrahmen überschreitet, den Sie für aktive Geräte definiert haben, wird ein Gerät als veraltet angesehen. Dieser **Aktivitätszeitstempel** befindet sich jetzt in der öffentlichen Vorschauphase.

## <a name="how-is-the-value-of-the-activity-timestamp-managed"></a>Wie wird der Wert des Aktivitätszeitstempels verwaltet?  

Die Auswertung des Aktivitätszeitstempels wird durch einen Authentifizierungsversuch eines Geräts ausgelöst. Azure AD wertet den Aktivitätszeitstempel aus, wenn Folgendes gilt:

- Eine Richtlinie für bedingten Zugriff, für die [verwaltete Geräte](../conditional-access/require-managed-devices.md) oder [genehmigte Client-Apps](../conditional-access/app-based-conditional-access.md) benötigt werden, wurde ausgelöst.

- Windows 10-Geräte, die entweder in Azure AD oder Hybrid-Azure AD eingebunden sind, sind im Netzwerk aktiv. 

- Mit Intune verwaltete Geräte wurden in den Dienst eingecheckt.


Wenn das Delta zwischen dem vorhandenen Wert des Aktivitätszeitstempels und dem aktuellen Wert größer als 14 Tage ist, wird der vorhandene Wert durch den neuen Wert ersetzt.
    

## <a name="how-do-i-get-the-activity-timestamp"></a>Wie erhalte ich den Aktivitätszeitstempel?

Sie haben zwei Möglichkeiten, um den Wert des Aktivitätszeitstempels abzurufen:

- Spalte **Aktivität** auf der [Seite „Geräte“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/Devices) im Azure-Portal

    ![Aktivitätszeitstempel](./media/manage-stale-devices/01.png)


- Cmdlet [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

    ![Aktivitätszeitstempel](./media/manage-stale-devices/02.png)



## <a name="plan-the-cleanup-of-your-stale-devices"></a>Planen der Bereinigung Ihrer veralteten Geräte

Sie sollten eine entsprechende Richtlinie definieren, um veraltete Gerät in Ihrer Umgebung effizient zu bereinigen. Mit dieser Richtlinie können Sie sicherstellen, dass Sie in Bezug auf veraltete Geräte alle Aspekte erfassen. Die folgenden Abschnitte enthalten Beispiele zu den allgemeinen Aspekten von Richtlinien. 

### <a name="cleanup-account"></a>Konto für Bereinigung

Zum Aktualisieren eines Geräts in Azure AD benötigen Sie ein Konto, dem die folgenden Rollen zugewiesen sind:

- Globaler Administrator

- Cloudgeräteadministrator (Neue Rolle verfügbar!)

- Intune-Dienstadministrator

Wählen Sie in der Bereinigungsrichtlinie Konten aus, denen die erforderlichen Rollen zugewiesen sind. 


### <a name="timeframe"></a>Zeitraum

Definieren Sie einen Zeitraum, der als Indikator für ein veraltetes Gerät dient. Rechnen Sie beim Definieren des Zeitraums das 14-Tage-Zeitfenster zum Aktualisieren des Aktivitätszeitstempels in Ihren Wert ein. Beispielsweise sollten Sie einen Zeitstempel, der jünger als 14 Tage ist, nicht als Indikator für ein veraltetes Gerät ansehen. Es gibt Szenarien, in denen ein Gerät veraltet erscheint, obwohl dies noch nicht der Fall ist. Der Besitzer des betroffenen Geräts kann beispielsweise im Urlaub oder krank sein,  sodass der Zeitraum für das Veralten von Geräten überschritten wird.

### <a name="disable-devices"></a>Deaktivieren von Geräten

Es ist nicht ratsam, ein Gerät sofort zu löschen, das veraltet erscheint. Falls es fälschlicherweise als veraltet gekennzeichnet wurde, können Sie einen Löschvorgang nämlich nicht mehr rückgängig machen. Die bewährte Methode besteht darin, ein Gerät für einen Karenzzeitraum zu deaktivieren, bevor Sie es löschen. Definieren Sie in Ihrer Richtlinie einen Zeitraum, in dem ein Gerät deaktiviert werden kann, bevor der Löschvorgang durchgeführt wird.


### <a name="mdm-controlled-devices"></a>Geräte mit MDM-Steuerung

Wenn Ihr Gerät per Intune oder mit einer anderen MDM-Lösung gesteuert wird, sollten Sie das Gerät im Verwaltungssystem ausmustern, bevor Sie es deaktivieren oder löschen.


### <a name="system-managed-devices"></a>Vom System verwaltete Geräte

Löschen Sie keine vom System verwalteten Geräte. Hierbei handelt es sich normalerweise um Geräte wie Autopilot. Nach dem Löschen können diese Geräte nicht erneut bereitgestellt werden. Das neue `get-msoldevice`-Cmdlet schließt vom System verwaltete Geräte standardmäßig aus. 


### <a name="hybrid-azure-ad-joined-devices"></a>In Azure AD eingebundene Hybridgeräte

Ihre in Hybrid-Azure AD eingebundenen Geräte sollten an Ihren Richtlinien für die Verwaltung von lokalen veralteten Geräten ausgerichtet sein. 

Bereinigen Sie Azure AD wie folgt:

- **Windows 10-Geräte**: Deaktivieren oder löschen Sie Windows 10-Geräte auf Ihrer lokalen AD-Instanz, und lassen Sie den geänderten Gerätestatus von Azure AD Connect mit Azure AD synchronisieren.

- **Windows 7/8**: Deaktivieren oder löschen Sie Windows 7/8-Geräte in Azure AD. Sie können Azure AD Connect nicht verwenden, um Windows 7/8-Geräte in Azure AD zu deaktivieren oder zu löschen.



### <a name="azure-ad-joined-devices"></a>In Azure AD eingebundene Geräte

Deaktivieren oder löschen Sie in Azure AD eingebundene Geräte in Azure AD.


### <a name="azure-ad-registered-devices"></a>Bei Azure AD registrierte Geräte

Deaktivieren oder löschen Sie in Azure AD registrierte Geräte in Azure AD.



## <a name="clean-up-stale-devices-in-the-azure-portal"></a>Bereinigen veralteter Geräte im Azure-Portal  

Sie können veraltete Geräte zwar im Azure-Portal bereinigen, aber es ist effizienter, für diesen Vorgang ein PowerShell-Skript zu verwenden. Verwenden Sie das aktuelle PowerShell V1-Modul, um den Zeitstempelfilter zu nutzen und vom System verwaltete Geräte herauszufiltern, z.B. Autopilot. An diesem Punkt ist die Nutzung von PowerShell V2 nicht zu empfehlen.


Eine typische Routine umfasst die folgenden Schritte:

1. Herstellen einer Verbindung mit Azure Active Directory über das Cmdlet [Connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0)

2. Abrufen der Liste mit den Geräten

3. Deaktivieren Sie das Gerät, indem Sie das Cmdlet [Disable-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/disable-msoldevice?view=azureadps-1.0) verwenden. 

4. Warten Sie unabhängig von der ausgewählten Anzahl von Tagen vor dem Löschen des Geräts die Toleranzperiode ab.

5. Entfernen Sie das Gerät mit dem Cmdlet [Remove-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/remove-msoldevice?view=azureadps-1.0).

### <a name="get-the-list-of-devices"></a>Abrufen der Liste mit den Geräten

Gehen Sie wie folgt vor, um alle Geräte abzurufen und die zurückgegebenen Daten in einer CSV-Datei zu speichern:

```powershell
Get-MsolDevice -all | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, Approxi
mateLastLogonTimestamp | export-csv devicelist-summary.csv
```

Falls Ihr Verzeichnis eine größere Zahl von Geräten enthält, können Sie den Zeitstempelfilter verwenden, um die Anzahl von zurückgegebenen Geräten einzugrenzen. Rufen Sie wie folgt alle Geräte mit einem Zeitstempel ab, der älter als ein bestimmtes Datum ist, und speichern Sie die zurückgegebenen Daten in einer CSV-Datei: 

```powershell
$dt = [datetime]’2017/01/01’
Get-MsolDevice -all -LogonTimeBefore $dt | select-object -Property Enabled, DeviceId, DisplayName, DeviceTrustType, ApproximateLastLogonTimestamp | export-csv devicelist-olderthan-Jan-1-2017-summary.csv
```


## <a name="what-you-should-know"></a>Wichtige Informationen

### <a name="why-is-the-timestamp-not-updated-more-frequently"></a>Warum wird der Zeitstempel nicht häufiger aktualisiert?

Der Zeitstempel wird aktualisiert, um Gerätelebenszyklus-Szenarien zu unterstützen. Dies ist keine Überwachung. Verwenden Sie die Überwachungsprotokolle der Anmeldung für häufigere Updates auf dem Gerät.

### <a name="why-should-i-worry-about-my-bitlocker-keys"></a>Warum sollte ich mir wegen meiner BitLocker-Schlüssel Gedanken machen?

Wenn sie konfiguriert sind, werden BitLocker-Schlüssel für Windows 10-Geräte auf dem Geräteobjekt in Azure AD gespeichert. Wenn Sie ein veraltetes Gerät löschen, löschen Sie auch die BitLocker-Schlüssel, die auf dem Gerät gespeichert sind. Sie sollten ermitteln, ob Ihre Bereinigungsrichtlinie am tatsächlichen Lebenszyklus Ihres Geräts ausgerichtet ist, bevor Sie ein veraltetes Gerät löschen. 

### <a name="how-do-i-know-all-the-type-of-devices-joined"></a>Wie kann ich ermitteln, welche Gerätetypen eingebunden sind?

Weitere Informationen zu den unterschiedlichen Typen finden Sie in der [Übersicht über die Geräteverwaltung](overview.md).

### <a name="what-happens-when-i-disable-a-device"></a>Was passiert, wenn ich ein Gerät deaktiviere?

Alle Authentifizierungen, bei denen ein Gerät für die Authentifizierung gegenüber Azure AD genutzt wird, werden abgelehnt. Typische Beispiele:

- **In Hybrid-Azure AD eingebundenes Gerät**: Benutzer verwenden das Gerät ggf., um sich an ihrer lokalen Domäne anzumelden. Sie können aber nicht auf Azure AD-Ressourcen, z.B. Office 365, zugreifen.

- **In Azure AD eingebundenes Gerät**: Benutzer können das Gerät nicht verwenden, um sich anzumelden. 

- **Mobile Geräte**: Der Benutzer kann nicht auf Azure AD-Ressourcen, z.B. Office 365, zugreifen. 



## <a name="next-steps"></a>Nächste Schritte

Einen Überblick über die Verwaltung von Geräten im Azure-Portal finden Sie unter [Managing devices using the Azure portal - preview](device-management-azure-portal.md) (Verwalten von Geräten mit dem Azure-Portal – Vorschauversion).



