<properties
    pageTitle="Hinzufügen eines Benutzers zu Ihrer Azure RemoteApp-Sammlung | Microsoft Azure"
    description="Erfahren Sie, wie Sie Benutzer zu Ihrer Azure RemoteApp-Sammlung hinzufügen."
    services="remoteapp"
	documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/18/2016"
    ms.author="elizapo" />

# Hinzufügen eines Benutzers zu Ihrer Azure RemoteApp-Sammlung

Damit Ihre Benutzer die Apps in Azure RemoteApp anzeigen und verwenden können, müssen Sie ihnen Zugriff auf Ihre Sammlung gewähren. Dies ist der einfache Teil: Geben Sie auf der Registerkarte **Benutzerzugriff** die Kontoinformationen für den Benutzer ein, und klicken Sie auf das Häkchen.

Welche Kontoinformationen benötigen Sie? Das hängt vom dem Typ der erstellten Sammlung (Cloud oder Hybrid) und der möglichen Verwendung von Office 365 ProPlus in dieser Sammlung ab.

## Unterstützte Benutzeridentitäten

Die unterschiedlichen Sammlungstypen (Cloud oder Hybrid) unterstützen für den Zugriff auf Anwendungen die Verwendung unterschiedlicher Benutzeridentitäten.

Bei einer Hybridsammlung von RemoteApp müssen Sie eine lokale Active Directory-Domäneninfrastruktur und einen Azure Active Directory-Mandanten mit Verzeichnisintegration (und optional einmaliges Anmelden) einrichten. Darüber hinaus müssen Sie einige Active Directory-Objekte im lokalen Verzeichnis erstellen.

Für eine RemoteApp-Cloudsammlung kann beliebigen Benutzern mit Azure Active Directory-Supportidentitäten der Benutzerzugriff auf RemoteApp einschließlich der Microsoft-Konten erteilt werden. Siehe hierzu die nachstehende Tabelle.

Office 365-Benutzer sind Azure Active Directory-Benutzer. Wenn diese über mit dem Verzeichnis synchronisierte Azure Active Directory-Hybridkonten verfügen, kann ihnen der Benutzerzugriff in einer Hybridbereitstellung von RemoteApp erteilt werden.

Diese Tabelle dient als Kurzübersicht zu den für Ihre Sammlung unterstützten Identitäten und den Active Directory-Anforderungen.

|Benutzerkonten |Cloud |Hybrid|
|--------------|--------|------|
|Microsoft Account| 	Ja|	Nein|
|Azure Active Directory (Azure AD)| | |
|Nur Azure AD-Cloud |Ja |Nein |
|ADsync mit Kennwortsynchronisierung |Ja |Ja |
|ADsync ohne Kennwortsynchronisierung|	Ja |Nein |
|ADsync mit AD FS |Ja |Ja |
|[Von Azure unterstützte Drittidentitätsanbieter](https://msdn.microsoft.com/library/azure/jj679342.aspx) (z. B. Ping) |Ja |Ja|
|Multi-Factor Authentication |Ja |Ja |

Beachten Sie die [weiteren Informationen](remoteapp-ad.md) zum Konfigurieren von Active Directory für RemoteApp.


> [AZURE.NOTE] Azure Active Directory-Benutzer müssen dem Ihrem Abonnement zugeordneten Mandanten angehören. (Sie können Ihr Abonnement im Portal auf der Registerkarte **Einstellungen** anzeigen und bearbeiten. Weitere Informationen finden Sie unter [Ändern des von RemoteApp verwendeten Azure Active Directory-Mandanten](remoteapp-changetenant.md).)

## Benutzerkontoinformationen für Office 365 ProPlus
Bei Verwendung des Vorlagenimages von Office 365 ProPlus in Ihrer Sammlung *oder* bei Erstellung eines benutzerdefinierten Images, das Office 365 verwendet, dürfen Sie nur Azure Active Directory-Benutzer hinzufügen, die Office 365-Abonnements für die Standarddomäne Ihres Abonnements haben. Weitere Informationen finden Sie unter [Verwenden von Office 365 mit Azure RemoteApp](remoteapp-o365.md).

<!---HONumber=AcomDC_0629_2016-->