---
title: 'Azure AD Connect: Voraussetzungen und Hardware | Microsoft Docs'
description: "Dieses Thema beschreibt die Voraussetzungen und die Hardwareanforderungen für Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 91b88fda-bca6-49a8-898f-8d906a661f07
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: 1868e5fd0427a5e1b1eeed244c80a570a39eb6a9
ms.openlocfilehash: e09017cbd6c4060ea24bb17c751277b4f4c6daf8
ms.contentlocale: de-de
ms.lasthandoff: 09/19/2017

---
# <a name="prerequisites-for-azure-ad-connect"></a>Voraussetzungen für Azure AD Connect
Dieses Thema beschreibt die Voraussetzungen und die Hardwareanforderungen für Azure AD Connect.

## <a name="before-you-install-azure-ad-connect"></a>Vor der Installation von Azure AD Connect
Vor der Installation von Azure AD Connect gibt es einige Dinge, die Sie benötigen.

### <a name="azure-ad"></a>Azure AD
* Ein Azure-Abonnement oder ein [Azure-Testabonnement](https://azure.microsoft.com/pricing/free-trial/). Dieses Abonnement ist nur für den Zugriff auf das Azure-Portal und nicht für die Verwendung von Azure AD Connect erforderlich. Bei Verwendung von PowerShell oder Office 365 benötigen Sie für Azure AD Connect kein Azure-Abonnement. Wenn Sie über eine Office 365-Lizenz verfügen, können Sie auch das Office 365-Portal verwenden. Mit einer kostenpflichtigen Office 365-Lizenz können Sie auch über das Office 365-Portal auf das Azure-Portal zugreifen.
  * Sie können auch das [Azure-Portal](https://portal.azure.com) verwenden. Für dieses Portal ist keine Azure AD-Lizenz erforderlich.
* [Fügen Sie die Domäne hinzu](../active-directory-domains-add-azure-portal.md) , die Sie in Azure AD verwenden möchten, und überprüfen Sie sie. Wenn Sie beispielsweise planen, „contoso.com“ für Ihre Benutzer zu verwenden, sollten Sie sicherstellen, dass diese Domäne überprüft wurde und nicht nur die Standarddomäne „contoso.onmicrosoft.com“ verwendet wird.
* In einem Azure AD-Mandanten sind standardmäßig 50.000 Objekte zulässig. Wenn Sie Ihre Domäne verifizieren, wird der Grenzwert auf 300.000 Objekte erhöht. Wenn Sie noch mehr Objekte in Azure AD benötigen, müssen Sie eine Supportanfrage stellen, um den Grenzwert noch weiter zu erhöhen. Wenn Sie mehr als 500.000 Objekte verwalten müssen, benötigen Sie eine Lizenz, z.B. für Office 365, Azure AD Basic, Azure AD Premium oder Enterprise Mobility and Security.

### <a name="prepare-your-on-premises-data"></a>Vorbereiten Ihrer lokalen Daten
* Verwenden Sie [IdFix](https://support.office.com/article/Install-and-run-the-Office-365-IdFix-tool-f4bd2439-3e41-4169-99f6-3fabdfa326ac) zum Ermitteln von Fehlern wie Duplikaten und Formatierungsproblemen in Ihrem Verzeichnis, bevor Sie eine Synchronisierung mit Azure AD und Office 365 durchführen.
* Überprüfen Sie die [optionalen Synchronisierungsfunktionen, die Sie in Azure AD aktivieren können](active-directory-aadconnectsyncservice-features.md) , und bestimmen Sie, welche Funktionen Sie aktivieren sollten.

### <a name="on-premises-active-directory"></a>Lokales Active Directory
* Die AD-Schemaversion und Funktionsebene der Gesamtstruktur müssen Windows Server 2003 oder höher entsprechen. Die Domänencontroller können eine beliebige Version ausführen, solange die Anforderungen an Schema und Gesamtstrukturebene erfüllt werden.
* Wenn Sie das Feature **Kennwortrückschreiben** verwenden möchten, müssen die Domänencontroller unter Windows Server 2008 (mit dem neuesten Service Pack) oder höher ausgeführt werden. Falls Ihre Domaincontroller unter 2008 (vor R2) ausgeführt werden, müssen Sie auch den [Hotfix KB2386717](http://support.microsoft.com/kb/2386717) anwenden.
* Der von Azure AD verwendete Domänencontroller darf nicht schreibgeschützt sein. Die Verwendung eines schreibgeschützten Domänencontrollers wird **nicht unterstützt**, und Azure AD Connect folgt keinen Umleitungen für Schreibvorgänge.
* Die Verwendung lokaler Gesamtstrukturen/Domänen unter Einsatz einteiliger Domänen wird **nicht unterstützt**.
* Die Verwendung lokaler Gesamtstrukturen/Domänen mit NetBios-Namen, die einen Punkt (.) enthalten, wird **nicht unterstützt**.
* Es wird empfohlen, den [Active Directory-Papierkorb zu aktivieren](active-directory-aadconnectsync-recycle-bin.md).

### <a name="azure-ad-connect-server"></a>Azure AD Connect-Server
* Azure AD Connect kann nicht auf dem Small Business Server oder Windows Server Essentials installiert werden. Der Server muss Windows Server Standard oder höher verwenden.
* Auf dem Azure AD Connect Server muss eine vollständige GUI installiert sein. Eine Installation unter Server Core wird **nicht unterstützt**.
* Azure AD Connect muss unter Windows Server 2008 oder höher installiert werden. Dieser Server kann bei Verwendung der Expresseinstellungen ein Domänencontroller oder ein Mitgliedsserver sein. Wenn Sie benutzerdefinierte Einstellungen verwenden, kann der Server auch eigenständig sein und muss nicht in eine Domäne eingebunden werden.
* Wenn Sie Azure AD Connect unter Windows Server 2008 oder Windows Server 2008 R2 installieren, achten Sie darauf, die neuesten Hotfixes über Windows Update anzuwenden. Die Installation kann mit einem nicht gepatchten Server nicht gestartet werden.
* Wenn Sie die **Kennwortsynchronisierung**verwenden möchten, muss der Azure AD Connect-Server unter Windows Server 2008 R2 SP1 oder höher ausgeführt werden.
* Wenn Sie planen, ein **gruppenverwaltetes Dienstkonto** zu verwenden, muss auf dem Azure AD Connect-Server Windows Server 2012 oder höher ausgeführt werden.
* Der Azure AD Connect-Server muss über [.NET Framework 4.5.1](#component-prerequisites) oder höher verfügen, und es muss [Microsoft PowerShell 3.0](#component-prerequisites) oder höher installiert sein.
* Auf dem Azure AD Connect-Server darf nicht die Gruppenrichtlinie für die PowerShell-Aufzeichnung aktiviert sein.
* Wenn Active Directory-Verbunddienste bereitgestellt werden, müssen die Server, auf denen die Active Directory-Verbunddienste oder der Webanwendungsproxy installiert werden, Windows Server 2012 R2 oder höher ausführen. [Windows-Remoteverwaltung](#windows-remote-management) muss auf diesen Servern zur Remoteinstallation aktiviert werden.
* Wenn Active Directory-Verbunddienste bereitgestellt werden, benötigen Sie [SSL-Zertifikate](#ssl-certificate-requirements).
* Wenn Active Directory-Verbunddienste bereitgestellt werden, müssen Sie die [Namensauflösung](#name-resolution-for-federation-servers)konfigurieren.
* Wenn Ihre globalen Administratoren MFA aktiviert haben, muss die URL **https://secure.aadcdn.microsoftonline-p.com** in der Liste vertrauenswürdiger Websites enthalten sein. Sie werden aufgefordert, diese Website der Liste vertrauenswürdigen Websites hinzuzufügen, wenn Sie zu einem MFA-Captcha aufgefordert werden und diese zuvor noch nicht hinzugefügt wurde. Sie können dafür den Internet Explorer verwenden.

### <a name="sql-server-used-by-azure-ad-connect"></a>Von Azure AD Connect verwendete SQL Server-Datenbank
* Azure AD Connect erfordert eine SQL Server-Datenbank zum Speichern von Identitätsdaten. Standardmäßig wird SQL Server 2012 Express LocalDB (eine einfache Version von SQL Server Express) installiert. Für SQL Server Express gilt ein 10-GB-Limit, mit dem Sie etwa 100.000 Objekte verwalten können. Wenn Sie eine höhere Anzahl von Verzeichnisobjekten verwalten möchten, müssen Sie im Installations-Assistenten auf eine andere Version von SQL Server verweisen.
* Wenn Sie eine separate SQL Server-Instanz verwenden, gelten die folgenden Anforderungen:
  * Azure AD Connect unterstützt sämtliche Versionen von Microsoft SQL Server – von SQL Server 2008 (mit dem neuesten Service Pack) bis SQL Server 2016 SP1. Microsoft Azure SQL-Datenbank wird als Datenbank **nicht unterstützt**.
  * Sie müssen eine SQL-Sortierung ohne Berücksichtigung der Groß- und Kleinschreibung verwenden. Diese Sortierungen werden durch „\_CI_“ in ihrem Namen bestimmt. Eine Sortierung mit Berücksichtigung der Groß- und Kleinschreibung und „\_CS_“ im Namen wird **nicht unterstützt**.
  * Sie können jeweils nur ein Synchronisationsmodul pro SQL-Instanz haben. Die Freigabe einer SQL-Instanz mit FIM/MIM Sync, DirSync oder Azure AD Sync wird **nicht unterstützt** .

### <a name="accounts"></a>Konten
* Ein globales Azure AD-Administratorkonto für den Azure AD-Mandanten, in den die Integration erfolgen soll. Bei diesem Konto muss es sich um ein **Geschäfts-, Schul- oder Unikonto** handeln, und es darf kein **Microsoft-Konto** sein.
* Wenn Sie Expresseinstellungen verwenden oder ein Upgrade von DirSync durchführen, müssen Sie über ein Enterprise-Administratorkonto für Ihr lokales Active Directory verfügen.
* [Konten in Active Directory](active-directory-aadconnect-accounts-permissions.md), wenn Sie den Installationspfad für benutzerdefinierte Einstellungen verwenden.

### <a name="connectivity"></a>Konnektivität
* Der Azure AD Connect-Server benötigt die DNS-Auflösung sowohl für das Intranet als auch für das Internet. Der DNS-Server muss Namen sowohl zu Ihrem lokalen Active Directory als auch zu den Azure AD-Endpunkten auflösen können.
* Wenn Sie in Ihrem Intranet Firewalls verwenden und die Ports zwischen den Azure AD Connect-Servern und Ihren Domänencontrollern öffnen müssen, finden Sie Informationen hierzu unter [Azure AD Connect-Ports](active-directory-aadconnect-ports.md).
* Wenn Ihr Proxy oder Ihre Firewall den Zugriff auf bestimmte URLs beschränkt, müssen die unter [URLs und IP-Adressbereiche von Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) dokumentierten URLs geöffnet werden.
  * Wenn Sie die Microsoft Cloud in Deutschland oder die Microsoft Azure Government-Cloud verwenden, finden Sie die URLs unter [Azure AD Connect: besondere Überlegungen zu Synchronisierungsdienstinstanzen](active-directory-aadconnect-instances.md) .
* Azure AD Connect (Version 1.1.614.0 und höher) verwendet standardmäßig TLS 1.2 für die Verschlüsselung der Kommunikation zwischen dem Synchronisierungsmodul und Azure AD. Wenn TLS 1.2 auf dem zugrunde liegenden Betriebssystem nicht verfügbar ist, greift Azure AD Connect schrittweise auf älter Protokolle zurück (TLS 1.1 und TLS 1.0). Azure AD Connect, das auf Windows Server 2008 ausgeführt wird, verwendet z.B. TLS 1.0, da Windows Server 2008 TLS 1.1 oder TLS 1.2 nicht unterstützt.
* Vor der Version 1.1.614.0 verwendet Azure AD Connect standardmäßig TLS 1.0 für die Verschlüsselung der Kommunikation zwischen dem Synchronisierungsmodul und Azure AD. Folgen Sie zur Änderung in TLS 1.2 den Schritten in [Aktivieren von TLS 1.2 für Azure AD Connect](#enable-tls-12-for-azure-ad-connect).
* Wenn Sie einen ausgehenden Proxy für die Verbindung mit dem Internet verwenden, muss die folgende Einstellung in der Datei **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** für den Installations-Assistenten und die Azure AD Connect-Synchronisierung hinzugefügt werden, um die Verbindung mit dem Internet und Azure AD zu ermöglichen. Dieser Text muss am Ende der Datei eingegeben werden. In diesem Codeabschnitt steht &lt;PROXYADDRESS&gt; für die Proxy-IP-Adresse oder den Hostnamen.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Wenn für den Proxyserver eine Authentifizierung erforderlich ist, muss sich das [Dienstkonto](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) in der Domäne befinden, und Sie müssen den Installationspfad aus den benutzerdefinierten Einstellungen zur Angabe eines [benutzerdefinierten Dienstkontos](active-directory-aadconnect-get-started-custom.md#install-required-components) verwenden. Außerdem müssen Sie in „machine.config“ eine weitere Änderung vornehmen. Durch diese Änderung in „machine.config“ antworten der Installations-Assistent und das Synchronisierungsmodul auf Authentifizierungsanfragen des Proxyservers. Auf allen Seiten des Installations-Assistenten mit Ausnahme der Seite **Konfigurieren** werden die Anmeldeinformationen des angemeldeten Benutzers verwendet. Auf der Seite **Konfigurieren** am Ende des Installations-Assistenten wird der Kontext in das von Ihnen erstellte [Dienstkonto](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account) geändert. Im Abschnitt „machine.config“ sollte wie folgt aussehen.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

* Wenn Azure AD Connect im Rahmen der Verzeichnissynchronisierung eine Webanforderung an Azure AD sendet, kann es bis zu 5 Minuten dauern, bis Azure AD antwortet. In der Regel ist auf Proxyservern das Leerlauftimeout für Verbindungen konfiguriert. Stellen Sie sicher, dass die Konfiguration auf mindestens 6 Minuten festgelegt ist.

Weitere Informationen zum [defaultProxy](https://msdn.microsoft.com/library/kd3cf2ex.aspx)-Element finden Sie bei MSDN.  
Informationen zu Problemen mit der Konnektivität finden Sie unter [Beheben von Konnektivitätsproblemen](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Sonstige
* Optional: Ein Testbenutzerkonto zur Überprüfung der Synchronisierung.

## <a name="component-prerequisites"></a>Voraussetzungen an Komponenten
### <a name="powershell-and-net-framework"></a>PowerShell und .NET Framework
Azure AD Connect ist abhängig von Microsoft PowerShell und .NET Framework 4.5.1. Auf dem Server muss diese Version oder eine neuere Version installiert sein. Führen Sie abhängig von Ihrer Windows Server-Version folgende Schritte aus:

* Windows Server 2012 R2
  * Microsoft PowerShell ist standardmäßig installiert. Es ist keine Aktion erforderlich.
  * .NET Framework 4.5.1 und neuere Versionen werden über Windows Update angeboten. Stellen Sie in der Systemsteuerung sicher, dass die neuesten Updates von Windows Server installiert sind.
* Windows Server 2008 R2 und Windows Server 2012
  * Die neueste Version von Microsoft PowerShell steht im **Microsoft Download Center**unter [Windows Management Framework 4.0](http://www.microsoft.com/downloads)zur Verfügung.
  * .NET Framework 4.5.1 und neuere Versionen sind im [Microsoft Download Center](http://www.microsoft.com/downloads)verfügbar.
* Windows Server 2008
  * Die neueste unterstützte Version von PowerShell steht im **Windows Management Framework 3.0**im [Microsoft Download Center](http://www.microsoft.com/downloads)zur Verfügung.
  * .NET Framework 4.5.1 und neuere Versionen sind im [Microsoft Download Center](http://www.microsoft.com/downloads)verfügbar.

### <a name="enable-tls-12-for-azure-ad-connect"></a>Aktivieren von TLS 1.2 für Azure AD Connect
Vor der Version 1.1.614.0 verwendet Azure AD Connect standardmäßig TLS 1.0 für die Verschlüsselung der Kommunikation zwischen dem Server mit dem Synchronisierungsmodul und Azure AD. Sie können dies ändern, indem Sie Ihre .NET-Anwendungen auf dem Server für die standardmäßige Verwendung von TLS 1.2 konfigurieren. Weitere Informationen zu TLS 1.2 finden Sie in der [Microsoft-Sicherheitsempfehlung 2960358](https://technet.microsoft.com/security/advisory/2960358).

1. TLS 1.2 kann unter Windows Server 2008 nicht aktiviert werden. Sie benötigen Windows Server 2008 R2 oder höher. Vergewissern Sie sich, dass der .NET 4.5.1-Hotfix für Ihr Betriebssystem installiert wurde. Informationen hierzu finden Sie in der [Microsoft-Sicherheitsempfehlung 2960358](https://technet.microsoft.com/security/advisory/2960358). Möglicherweise ist dieser Hotfix oder eine neuere Version bereits auf dem Server installiert.
2. Wenn Sie Windows Server 2008 R2 verwenden, stellen Sie sicher, dass TLS 1.2 aktiviert ist. Unter Windows Server 2012-Server und neueren Versionen sollte TLS 1.2 bereits aktiviert sein.
   ```
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
   ```
3. Legen Sie für alle Betriebssysteme diesen Registrierungsschlüssel fest, und starten Sie den Server neu.
   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
   "SchUseStrongCrypto"=dword:00000001
   ```
4. Wenn Sie TLS 1.2 auch zwischen dem Server mit dem Synchronisierungsmodul und einer SQL Server-Remoteinstanz aktivieren möchten, vergewissern Sie sich, dass die erforderlichen Versionen für die [TLS 1.2-Unterstützung für Microsoft SQL Server](https://support.microsoft.com/kb/3135244)installiert sind.

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Voraussetzungen für die Verbundinstallation und -konfiguration
### <a name="windows-remote-management"></a>Windows-Remoteverwaltung
Überprüfen Sie bei Verwendung von Azure AD Connect für die Bereitstellung von Active Directory-Verbunddiensten oder des Webanwendungsproxys die nachfolgenden Anforderungen:

* Wenn der Zielserver zu einer Domäne gehört, stellen Sie sicher, dass die Windows-Remoteverwaltung aktiviert ist.
  * Verwenden Sie in einem PSH-Befehlsfenster mit erhöhten Rechten den Befehl `Enable-PSRemoting –force`
* Wenn der Zielserver kein mit in die Domäne eingebundener WAP-Computer ist, gibt es einige zusätzliche Anforderungen.
  * Auf dem Zielcomputer (WAP-Computer): 
    * Stellen Sie sicher, dass der WinRM-Dienst (Windows-Remoteverwaltung/WS-Verwaltung) über das Dienste-Snap-In ausgeführt wird.
    * Verwenden Sie in einem PSH-Befehlsfenster mit erhöhten Rechten den Befehl `Enable-PSRemoting –force`
  * Auf dem Computer, auf dem der Assistent ausgeführt wird (wenn der Zielcomputer nicht der Domäne beigetreten ist oder sich in einer nicht vertrauenswürdigen Domäne befindet):
    * Verwenden Sie in einem PSH-Befehlsfenster mit erhöhten Rechten den Befehl `Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
    * In Server Manager:
      * Fügen Sie den DMZ-WAP-Host zum Computerpool hinzu (Server-Manager -> "Verwalten" -> "Server hinzufügen"-> Registerkarte "DNS verwenden").
      * Server-Manager, Registerkarte "Alle Server": Klicken Sie mit der rechten Maustaste auf "WAP-Server", und wählen Sie "Verwalten als" aus. Geben Sie dann die Anmeldeinformationen für den lokalen WAP-Computer ein (nicht für die Domäne).
      * Um die PSH-Remotekonnektivität zu überprüfen, klicken Sie auf der Registerkarte "Alle Server" mit der rechten Maustaste auf "WAP-Server", und wählen Sie "Windows PowerShell" aus. Daraufhin sollte sich eine PSH-Remotesitzung öffnen, um sicherzustellen, dass PowerShell-Remotesitzungen hergestellt werden können.

### <a name="ssl-certificate-requirements"></a>SSL-Zertifikatanforderungen
* Es wird dringend empfohlen, dasselbe SSL-Zertifikat für alle Knoten der AD FS-Farm sowie alle Webanwendungsproxy-Server zu verwenden.
* Das Zertifikat muss ein X.509-Zertifikat sein.
* Sie können ein selbstsigniertes Zertifikat für Verbundserver in einer Testumgebung verwenden. Bei einer Produktionsumgebung wird jedoch empfohlen, dass Sie das Zertifikat von einer öffentlichen Zertifizierungsstelle beziehen.
  * Wenn Sie ein Zertifikat verwenden, das nicht öffentlich vertrauenswürdig ist, stellen Sie sicher, dass dem auf jedem Webanwendungsproxy-Server installierten Zertifikat sowohl auf dem lokalen Server als auch auf allen Verbundservern vertraut wird.
* Die Identität des Zertifikats muss mit dem Namen des Verbunddiensts (z.B. sts.contoso.com) übereinstimmen.
  * Die Identität ist die Erweiterung eines  alternativen Antragstellernamens (SAN) des Typs "dNSName". Wenn keine SAN-Einträge vorhanden sind, wird der Name des Antragstellers als allgemeiner Name angegeben.  
  * Im Zertifikat können mehrere SAN-Einträge hinterlegt werden, sofern einer von ihnen mit dem Namen des Verbunddiensts übereinstimmt.
  * Wenn Sie die Arbeitsbereichverknüpfung verwenden möchten, ist ein zusätzliches SAN mit dem Wert **enterpriseregistration.** gefolgt vom Suffix UPN (User Principal Name, Benutzerprinzipalname) Ihrer Organisation erforderlich, z.B. **enterpriseregistration.contoso.com**.
* Auf CNG-Schlüsseln (CryptoAPI Next Generation) und Schlüsselspeicheranbietern basierende Zertifikate werden nicht unterstützt. Dies bedeutet, dass Sie ein CSP-basiertes Zertifikat (Kryptografie-Service Provider) verwenden müssen, kein Zertifikat von einem Schlüsselspeicheranbieter.
* Platzhalterzertifikate werden unterstützt.

### <a name="name-resolution-for-federation-servers"></a>Namensauflösung für Verbundserver
* Richten Sie DNS-Einträge für den AD FS-Verbunddienstnamen (z.B. sts.contoso.com) für das Intranet (Ihr interner DNS-Server) sowie für das Extranet (öffentlicher DNS über Ihre Domänenregistrierungsstelle) ein. Stellen Sie sicher, dass Sie für den Intranet-DNS-Eintrag A-Einträge und keine CNAME-Einträge verwenden. Dies ist notwendig, damit die Windows-Authentifizierung ordnungsgemäß von dem mit der Domäne verknüpften Computer funktioniert.
* Wenn Sie mehr als einen AD FS-Server oder Webanwendungs-Proxyserver bereitstellen, vergewissern Sie sich, dass Sie den Lastenausgleich konfiguriert haben und dass die DNS-Einträge für den AD FS-Verbunddienstnamen (z.B. „sts.contoso.com“) auf den Lastenausgleich verweisen.
* Damit die integrierte Windows-Authentifizierung für Browseranwendungen funktioniert, die in Ihrem Intranet in Internet Explorer verwendet werden, müssen Sie sicherstellen, dass der AD FS-Verbunddienstname (z.B. „sts.contoso.com“) der Intranetzone in Internet Explorer hinzugefügt wurde. Diese kann über die Gruppenrichtlinie gesteuert und für alle Ihre mit der Domäne verknüpften Computer bereitgestellt werden.

## <a name="azure-ad-connect-supporting-components"></a>Azure AD Connect unterstützende Komponenten
Nachfolgend finden Sie eine Liste der Komponenten, die Azure AD Connect auf dem Server installiert, auf dem Azure AD Connect installiert ist. Diese Liste ist für eine einfache Expressinstallation. Wenn Sie auf der Installationsseite für Synchronisierungsdienste eine andere SQL Server-Version auswählen, wird SQL Express LocalDB nicht lokal installiert.

* Azure AD Connect Health
* Microsoft Online Services-Anmelde-Assistent für IT-Experten (installiert, aber ohne Abhängigkeit)
* Microsoft SQL Server 2012 – Befehlszeilenprogramme
* Microsoft SQL Server 2012 Express LocalDB
* Microsoft SQL Server 2012 Native Client
* Microsoft Visual C++ 2013 Redistributionspaket

## <a name="hardware-requirements-for-azure-ad-connect"></a>Hardwareanforderungen für Azure AD Connect
Die folgende Tabelle zeigt die Mindestanforderungen für den Azure AD Connect-Synchronisierungscomputer.

| Anzahl der Objekte in Active Directory | CPU | Arbeitsspeicher | Festplattengröße |
| --- | --- | --- | --- |
| Weniger als 10.000 |1,6 GHz |4 GB |70 GB |
| 10.000 bis 50.000 |1,6 GHz |4 GB |70 GB |
| 50.000 bis 100.000 |1,6 GHz |16 GB |100 GB |
| Für 100.000 oder mehr Objekte ist die Vollversion von SQL Server erforderlich | | | |
| 100.000 bis 300.000 |1,6 GHz |32 GB |300 GB |
| 300.000 bis 600.000 |1,6 GHz |32 GB |450 GB |
| Mehr als 600.000 |1,6 GHz |32 GB |500 GB |

Im Folgenden sind die Mindestanforderungen für Computer mit AD FS oder Webanwendungsservern aufgeführt:

* CPU: Doppelkern mit 1,6 GHz oder mehr
* Arbeitsspeicher: mindestens 2 GB
* Azure-VM: A2-Konfiguration oder höher

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md).

