---
title: Multi-Factor Authentication – Azure SQL | Microsoft-Dokumentation
description: Azure SQL-Datenbank und Azure SQL Data Warehouse unterstützen Verbindungen aus SQL Server Management Studio (SSMS) mithilfe der universellen Active Directory-Authentifizierung.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: 938d1b820bbc85824138d77b81b0f922fd494d0d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58003377"
---
# <a name="universal-authentication-with-sql-database-and-sql-data-warehouse-ssms-support-for-mfa"></a>Universelle Authentifizierung bei SQL-Datenbank und SQL Data Warehouse (SSMS-Unterstützung für MFA)
Azure SQL-Datenbank und Azure SQL Data Warehouse unterstützen Verbindungen aus SQL Server Management Studio (SSMS) mithilfe der *universellen Active Directory-Authentifizierung*. 
**Herunterladen der aktuellen Version von SSMS:** Laden Sie die neueste Version von SSMS unter [Herunterladen von SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) auf den Clientcomputer herunter. Verwenden Sie für alle Features in diesem Artikel mindestens die Version von Juli 2017, Version 17.2.  Das aktuelle Verbindungsdialogfeld sieht folgendermaßen aus: ![1mfa-universal-connect](./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png "Füllt das Feld für den Benutzernamen aus.")  

## <a name="the-five-authentication-options"></a>Die fünf Authentifizierungsoptionen  
- Die universelle Active Directory-Authentifizierung unterstützt die zwei nicht interaktiven Authentifizierungsmethoden (Authentifizierung per `Active Directory - Password` und Authentifizierung per `Active Directory - Integrated`). Die nicht interaktiven Authentifizierungsmethoden – `Active Directory - Password` und `Active Directory - Integrated` – können in vielen verschiedenen Anwendungen (ADO.NET, JDBC, ODBC usw.) eingesetzt werden. Bei diesen beiden Methoden werden niemals Popupdialogfelder angezeigt.

- Die Authentifizierung per `Active Directory - Universal with MFA` ist eine interaktive Methode, die auch *Azure Multi-Factor Authentication* (MFA) unterstützt. Azure MFA hilft beim Schützen des Zugriffs auf Daten und Anwendungen und erfüllt gleichzeitig die Anforderungen von Benutzern an ein einfaches Anmeldeverfahren. Diese Lösung ermöglicht eine sichere Authentifizierung über eine Reihe einfacher Überprüfungsoptionen: Telefonanruf, SMS, Smartcards mit PIN oder Benachrichtigung in einer mobilen App. Benutzer können ihre bevorzugte Methode wählen. Bei der interaktiven MFA mit Azure AD kann ein Popupdialogfeld zur Überprüfung geöffnet werden.

Eine Beschreibung von Multi-Factor Authentication finden Sie unter [Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md).
Konfigurationsschritte finden Sie unter [Konfigurieren der Multi-Factor Authentication in Azure SQL-Datenbank für SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Name der Azure AD-Domäne oder Mandanten-ID-Parameter   

Ab [SSMS Version 17](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) können Benutzer, die aus anderen Azure Active Directorys als Gastbenutzer in Active Directory importiert wurden, beim Herstellen einer Verbindung den Namen der Azure AD-Domäne oder die Mandanten-ID angeben. Zu den Gastbenutzern zählen Benutzer, die von anderen Azure ADs eingeladen wurden, Microsoft-Konten wie outlook.com, hotmail.com, live.com oder andere Konten wie gmail.com. Durch diese Informationen kann bei der **universellen Active Directory-Authentifizierung mit mehrstufiger Authentifizierung** die richtige Authentifizierungsstelle identifiziert werden. Diese Option ist für die Unterstützung von Microsoft-Konten (MSA) wie outlook.com, hotmail.com oder live.com oder Nicht-MSA-Konten erforderlich. Alle diese Benutzer, die mithilfe der universellen Authentifizierung authentifiziert werden sollen, müssen den Namen ihrer Azure AD-Domäne oder ihre Mandanten-ID eingeben. Dieser Parameter stellt den aktuellen Namen der Azure AD-Domäne/die aktuelle Mandanten-ID dar, mit denen der Azure-Server verknüpft ist. Wenn Azure Server beispielsweise mit der Azure AD-Domäne `contosotest.onmicrosoft.com` verbunden ist, in der Benutzer `joe@contosodev.onmicrosoft.com` als importierter Benutzer aus Azure AD-Domäne `contosodev.onmicrosoft.com` gehostet wird, lautet der erforderliche Domänenname für die Authentifizierung dieses Benutzers `contosotest.onmicrosoft.com`. Wenn der Benutzer ein nativer Benutzer des mit Azure Server verknüpften Azure AD ist und kein MSA-Konto darstellt, sind weder Domänenname noch Mandanten-ID erforderlich. Um den Parameter einzugeben (ab SSMS Version 17.2) füllen Sie das Dialogfeld **Verbindung mit Datenbank herstellen** aus. Wählen Sie dazu die Authentifizierung **Active Directory: universell mit MFA-Unterstützung** aus, klicken Sie auf **Optionen**, füllen Sie das Feld **Benutzername** aus, und klicken Sie dann auf die Registerkarte **Verbindungseigenschaften**. Aktivieren Sie das Kontrollkästchen **AD-Domänenname oder Mandanten-ID**, und geben Sie die Authentifizierungsstelle an, z.B. den Domänennamen (**contosotest.onmicrosoft.com**) oder die GUID der Mandanten-ID.  
   ![mfa-tenant-ssms](./media/sql-database-ssms-mfa-auth/mfa-tenant-ssms.png)   

### <a name="azure-ad-business-to-business-support"></a>Azure AD-Business-to-Business-Unterstützung   
Azure AD-Benutzer, die für Azure AD-B2B-Szenarien als Gastbenutzer unterstützt werden (siehe [Was ist Azure B2B-Zusammenarbeit](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md)), können nur als Mitglieder einer Gruppe im aktuellen Azure AD eine Verbindung mit SQL-Datenbank und SQL Data Warehouse herstellen und müssen dann mit der Transact-SQL-Anweisung `CREATE USER` in einer bestimmten Datenbank manuell zugeordnet werden. Wenn beispielsweise `steve@gmail.com` zu Azure AD `contosotest` (mit der Azure AD-Domäne `contosotest.onmicrosoft.com`) eingeladen wird, muss eine Azure AD-Gruppe, z.B. `usergroup` in dem Azure AD erstellt werden, das `steve@gmail.com` als Mitglied enthält. Diese Gruppe muss dann durch einen Azure AD-SQL-Administrator oder Azure AD-DBO durch Ausführen der Transact-SQL-Anweisung `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` für eine bestimmte Datenbank (z.B. MyDatabase) erstellt werden. Nach dem Erstellen des Datenbankbenutzers kann der Benutzer `steve@gmail.com` sich mithilfe der SSMS-Authentifizierungsoption `Active Directory – Universal with MFA support` bei `MyDatabase` anmelden. Die Benutzergruppe verfügt standardmäßig nur über die Verbindungsberechtigung, und alle weiteren Datenzugriffe müssen auf normale Weise gewährt werden. Beachten Sie, dass der Benutzer `steve@gmail.com` als Gastbenutzer das Kontrollkästchen aktivieren und den AD-Domänennamen `contosotest.onmicrosoft.com` im SSMS-Dialogfeld **Verbindungseigenschaften** hinzufügen muss. Die Option **AD-Domänenname oder Mandanten-ID** wird nur für Optionen der Reihe „universell mit MFA-Unterstützung“ unterstützt, andernfalls wird sie abgeblendet dargestellt.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Einschränkungen der universellen Authentifizierung bei SQL-Datenbank und SQL Data Warehouse
- SSMS und SqlPackage.exe sind die einzigen Tools, die derzeit für MFA über die universelle Active Directory-Authentifizierung aktiviert sind.
- SSMS Version 17.2 unterstützt den gleichzeitigen Zugriff durch mehrere Benutzer mithilfe der universellen Authentifizierung mit mehrstufiger Authentifizierung. In Version 17.0 und 17.1 konnte sich nur ein einziges Azure Active Directory-Konto über die universelle Authentifizierung bei einer SSMS-Instanz anmelden. Für die Anmeldung mit einem anderen Azure AD-Konto müssen Sie eine andere SSMS-Instanz verwenden. (Diese Einschränkung gilt nur für die universelle Active Directory-Authentifizierung. Mit der Active Directory-Kennwortauthentifizierung, der integrierten Active Directory-Authentifizierung oder der SQL Server-Authentifizierung können Sie sich bei verschiedenen Servern anmelden.)
- SSMS unterstützt die universelle Active Directory-Authentifizierung für den Objekt-Explorer, den Abfrage-Editor und die Abfragespeichervisualisierung.
- SSMS Version 17.2 bietet Unterstützung von DacFx Wizard zum Exportieren/Extrahieren/Bereitstellen von Daten aus Datenbanken. Sobald ein bestimmter Benutzer über das erste Authentifizierungsdialogfeld mithilfe der universellen Authentifizierung authentifiziert wurde, funktioniert DacFx Wizard genau wie bei allen anderen Authentifizierungsmethoden.
- Die SSMS-Tabellendesigner unterstützt die universelle Authentifizierung nicht.
- Es bestehen keine weiteren Softwareanforderungen für die universelle Active Directory-Authentifizierung – mit einer Ausnahme: Sie müssen eine unterstützte SSMS-Version verwenden.  
- Die ADAL-Version (Active Directory Authentication Library) für die universelle Authentifizierung wurde auf die aktuelle veröffentlichte Version 3.13.9 von „ADAL.DLL“ aktualisiert. Weitere Informationen finden Sie unter [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  


## <a name="next-steps"></a>Nächste Schritte

- Konfigurationsschritte finden Sie unter [Konfigurieren der Multi-Factor Authentication in Azure SQL-Datenbank für SQL Server Management Studio](sql-database-ssms-mfa-authentication-configure.md).
- Gewähren Sie anderen Benutzern Zugriff auf Ihre Datenbank: [SQL-Datenbankauthentifizierung und -Autorisierung: Gewähren von Zugriff](sql-database-manage-logins.md)  
- Stellen Sie sicher, dass andere Benutzer über die Firewall eine Verbindung herstellen können: [Konfigurieren einer Firewallregel auf Serverebene für Azure SQL-Datenbank mithilfe des Azure-Portals](sql-database-configure-firewall-settings.md)  
- [Konfigurieren und Verwalten der Azure Active Directory-Authentifizierung mit SQL-Datenbank oder SQL Data Warehouse](sql-database-aad-authentication-configure.md)  
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](https://docs.microsoft.com/sql/tools/sqlpackage)  
- [Importieren einer BACPAC-Datei in eine neue Azure SQL-Datenbank](../sql-database/sql-database-import.md)  
- [Exportieren einer Azure SQL-Datenbank in eine BACPAC-Datei](../sql-database/sql-database-export.md)  
- C#-Schnittstelle [IUniversalAuthProvider-Schnittstelle](https://msdn.microsoft.com/library/microsoft.sqlserver.dac.iuniversalauthprovider.aspx)  
- Bei Verwendung der Authentifizierung **Active Directory: universell mit MFA-Unterstützung** ist ab [SSMS 17.3](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) die ADAL-Ablaufverfolgung verfügbar. Die ADAL-Ablaufverfolgung ist standardmäßig deaktiviert. Sie können sie aktivieren, indem Sie unter **Azure-Dienste** > **Azure-Cloud** > **Ablaufverfolgungsebene für ADAL-Ausgabefenster** im Menü **Optionen** die **Tools** verwenden und im Menü **Ansicht** die Option **Ausgabe** aktivieren. Die Ablaufverfolgungen im Ausgabefenster sind verfügbar, wenn Sie die Option **Azure Active Directory** aktivieren.  
