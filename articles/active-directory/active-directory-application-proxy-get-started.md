<properties
	pageTitle="Bereitstellen von sicherem Remotezugriff auf lokale Apps"
	description="Erläutert, wie Sie mit dem Azure AD-Anwendungsproxy sicheren Remotezugriff auf Ihre lokalen Anwendungen bereitstellen können."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen

> [AZURE.NOTE] Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Sie möchten für Remotebenutzer den Zugriff mit beliebigen Arten von Geräten bereitstellen – verwaltete und nicht verwaltete Geräte, Tablets, Smartphones und Laptops. Die Bereitstellung des sicheren Zugriffs für eine Vielzahl von Ressourcen kann jedoch komplex sein. In den letzten Jahren war es beliebt, sicheren Remotezugriff über Reverseproxys bereitzustellen, aber diese mussten sich hinter Firewalls befinden. Die Firewalls waren zum einen schwierig zu sichern, andererseits war es schwer, ihre hohe Verfügbarkeit zu garantieren.

## Sicherer Remotezugriff in der Cloud
Mit dem Anwendungsproxy in Azure Active Directory (AD) erreichen Sie beim Remotezugriff über moderne Cloudumgebungen ein technisch höheres Niveau. Der Anwendungsproxy ist ein Feature von Azure AD, das als Dienst bereitgestellt wird, d. h. es ist einfach bereitzustellen und zu verwenden. Es ist in Azure AD integriert, der gleichen Identitätsplattform, die von Office 365 verwendet wird.

## Was ist der Azure Active Directory-Anwendungsproxy?
Der Anwendungsproxy ermöglicht das einmalige Anmelden (SSO, Single Sign-On) und sicheren Remotezugriff für lokal gehostete Webanwendungen, wie z. B. SharePoint-Websites und Outlook Web Access. Auf Ihre lokalen Webanwendungen kann jetzt genauso zugegriffen werden wie auf Ihre SaaS-Apps in Azure Active Directory, ohne dass ein VPN oder die Änderung der Netzwerkinfrastruktur nötig wäre. Mit dem Anwendungsproxy können Sie Anwendungen veröffentlichen, und Mitarbeiter können sich dann von zu Hause oder auf ihren eigenen Geräten bei Ihren Apps anmelden und sich über diesen cloudbasierten Proxy authentifizieren.

## Wie funktioniert Application Insights?

Der Anwendungsproxy führt drei grundlegende Schritte aus. 1. Connectors werden im lokalen Netzwerk bereitgestellt. 2. Der Connector stellt eine Verbindung mit dem Clouddienst her. 3. Der Connector und der Clouddienst routen den Benutzerdatenverkehr an die Anwendungen.

 ![AzureAD-App-Proxy-Diagramm](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

1. Der Benutzer greift auf die Anwendung über den Anwendungsproxy zu und wird zur Authentifizierung an die Azure AD-Anmeldeseite geleitet.
2. Nach der erfolgreichen Anmeldung wird ein Token generiert und an den Benutzer gesendet.
3. Der Benutzer sendet das Token an den Anwendungsproxy, der den Benutzerprinzipalnamen (UPN) und den Sicherheitsprinzipalnamen (SPN) aus dem Token abruft und die Anforderung dann an den Connector leitet.
4. Der Connector fordert im Namen des Benutzers ein Kerberos-Ticket an, das für die interne Authentifizierung (Windows) verwendet werden kann. Dies wird als eingeschränkte Kerberos-Delegierung bezeichnet.
5. Ein Kerberos-Ticket wird von Active Directory abgerufen.
6. Das Ticket wird an den Anwendungsserver gesendet und überprüft.
7. Die Antwort wird über den Anwendungsproxy an den Benutzer gesendet.

### Aktivieren des Zugriffs
Um den Anwendungsproxy nutzen zu können, müssen Sie einen als Connector bezeichneten schlanken Windows Server-Dienst in Ihrem Netzwerk installieren. Für den Connector müssen Sie keine eingehenden Ports öffnen und keine Komponenten in der DMZ platzieren. Wenn der Datenverkehr für Ihre Apps ansteigt, können Sie weitere Connectors hinzufügen, und der Dienst übernimmt den Lastenausgleich. Connectors sind zustandslos und rufen alle Informationen nach Bedarf aus der Cloud ab.

Beim Remotezugriff eines Benutzers auf Anwendungen mit einem beliebigen Gerät wird er von Azure Active Directory authentifiziert und erhält Zugriff auf die Anwendung.

### Einmaliges Anmelden
Der Azure AD-Anwendungsproxy ermöglicht einmaliges Anmelden für Anwendungen, die die integrierte Windows-Authentifizierung (IWA) verwenden oder Ansprüche unterstützen. Wenn Ihre Anwendung die integrierte Windows-Authentifizierung verwendet, nimmt der Anwendungsproxy mithilfe der eingeschränkten Kerberos-Delegierung die Identität des Benutzers an, um das einmalige Anmelden (SSO) zu ermöglichen. Wenn Sie über eine Anwendung verfügen, die Ansprüche unterstützt und die Azure Active Directory vertraut, wird das einmalige Anmelden möglich, weil der Benutzer bereits über Azure Active Directory authentifiziert wurde.

## Erste Schritte
Stellen Sie sicher, dass Sie über ein Azure AD Basic- oder Premium-Abonnement und ein Azure AD-Verzeichnis verfügen, für die Sie als globaler Administrator angemeldet sind. Sie benötigen auch Azure AD Basic- oder Premium-Lizenzen für den Verzeichnisadministrator und Benutzer, die auf die Apps zugreifen. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

### Erste Schritte zur Aktivierung des Remotezugriffs auf lokale Anwendungen
Das Einrichten des Anwendungsproxys erfolgt in zwei Schritten:

1. [Aktivieren des Anwendungsproxys und Konfigurieren des Connectors](active-directory-application-proxy-enable.md)  
2. [Veröffentlichen von Anwendungen](active-directory-application-proxy-publish.md) – Verwenden Sie den schnell und einfach einzusetzenden Assistenten, um Ihre lokalen Apps zu veröffentlichen und den Remotezugriff darauf zu ermöglichen.

## Wie geht es weiter?
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)


### Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen
- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Als Organisation für Azure registrieren](sign-up-organization.md)
- [Azure-Identität](fundamentals-identity.md)

<!---HONumber=AcomDC_0518_2016-->