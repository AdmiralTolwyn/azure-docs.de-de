---
title: Aktivieren des Azure AD-Anwendungsproxys im klassischen Portal | Microsoft-Dokumentation
description: "Aktivieren Sie den Anwendungsproxy über das klassische Azure-Portal, und installieren Sie die Connectors für den Reverseproxy."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/02/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: ea97fdc8d146ed524a932018b572ceda0982738b
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/03/2017
---
# <a name="enable-application-proxy-in-the-classic-portal-and-download-connectors"></a>Aktivieren des Anwendungsproxys im klassischen Azure-Portal und Herunterladen von Connectors
In diesem Artikel wird anhand der erforderlichen Schritte beschrieben, wie Sie den Microsoft Azure AD-Anwendungsproxy für Ihr Cloudverzeichnis in Azure AD aktivieren.

Weitere Informationen zu den Vorteilen des Anwendungsproxys finden Sie unter [Bereitstellen von sicherem Remotezugriff auf lokale Anwendungen](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Voraussetzungen für den Anwendungsproxy
Bevor Sie die Anwendungsproxydienste aktivieren und verwenden können, benötigen Sie Folgendes:

* Ein [Basic- oder Premium-Abonnement](active-directory-editions.md) für Microsoft Azure AD und ein Azure AD-Verzeichnis, für das Sie als globaler Administrator fungieren.
* Ein Server, auf dem Windows Server 2012 R2 bzw. Windows Server 2016 oder höher installiert ist und auf dem Sie den Anwendungsproxy-Connector installieren können. Der Server sendet Anforderungen an die Anwendungsproxydienste in der Cloud und benötigt eine HTTP- oder HTTPS-Verbindung mit den Anwendungen, die Sie veröffentlichen.
  * Für das einmalige Anmelden an Ihren veröffentlichten Anwendungen sollte dieser Computer in dieselbe AD-Domäne wie die veröffentlichten Anwendungen eingebunden sein. Weitere Informationen finden Sie unter [Einmaliges Anmelden mit Anwendungsproxy](active-directory-application-proxy-sso-using-kcd.md).
* Wenn in Ihrer Organisation Proxyserver zum Herstellen einer Verbindung mit dem Internet verwendet werden, helfen Ihnen die Details zur Konfiguration in [Working with existing on-premises proxy servers](application-proxy-working-with-proxy-servers.md) (Verwenden von vorhandenen lokalen Proxyservern) weiter.

## <a name="open-your-ports"></a>Öffnen der Ports

Zum Vorbereiten Ihrer Umgebung für den Azure AD-Anwendungsproxy, müssen Sie zuerst die Kommunikation mit Azure-Rechenzentren ermöglichen. Wenn der Pfad durch eine Firewall geschützt ist, sollten Sie sich vergewissern, dass diese so konfiguriert ist, dass der Connector HTTPS-Anforderungen (TCP) an den Anwendungsproxy richten kann.

1. Öffnen Sie die folgenden Ports für den **ausgehenden** Datenverkehr:

   | Portnummer | Wie diese verwendet wird |
   | --- | --- |
   | 80 | Herunterladen der Zertifikatssperrlisten (CRL) bei der Überprüfung des SSL-Zertifikats |
   | 443 | Gesamte ausgehende Kommunikation mit dem Webanwendungsproxy-Dienst |

   Wenn Ihre Firewall Datenverkehr gemäß Ursprungsbenutzern erzwingt, öffnen Sie diese Ports für den Datenverkehr aus Windows-Diensten, die als Netzwerkdienst ausgeführt werden.

   > [!IMPORTANT]
   > Die Tabelle gibt die Portanforderungen für die Connector-Versionen 1.5.132.0 und höher wieder. Haben Sie eine ältere Connector-Version, müssen Sie auch die folgenden Ports aktivieren: 5671, 8080, 9090, 9091, 9350, 9352 und 10100 – 10120.
   >
   >Weitere Informationen zum Aktualisieren Ihres Connectors auf die neueste Version, finden Sie unter [Understand Azure AD Application Proxy connectors (Verstehen von Azure AD-Anwendungsproxy-Connectors)](application-proxy-understand-connectors.md#automatic-updates).

2. Wenn Ihre Firewall oder ihr Proxy DNS-Whitelisting zulässt, können Sie Verbindungen zu msappproxy.net und servicebus.windows.net mittels Whitelist beschränken. Ist dies nicht der Fall, müssen Sie den Zugriff auf die [IP-Adressbereiche für das Azure-Rechenzentrum](https://www.microsoft.com/download/details.aspx?id=41653) aktivieren, die wöchentlich aktualisiert werden.

3. Verwenden Sie den [Azure AD Application Proxy Connector Ports Test Tool (Testtool der Anwendungsproxy-Connectortools von Azure AD)](https://aadap-portcheck.connectorporttest.msappproxy.net/), um sicherzustellen, dass Ihr Connector den Anwendungsproxydienst erreichen kann. Stellen Sie zumindest sicher, dass die Region USA (Mitte) und die Ihnen am nächsten gelegene Region alle über grüne Häkchen verfügen. Darüber hinaus bedeuten mehr grüne Häkchen größere Resilienz.

## <a name="enable-application-proxy-in-azure-ad"></a>Aktivieren des Anwendungsproxys in Azure AD
1. Melden Sie sich als Administrator am [klassischen Azure-Portal](https://manage.windowsazure.com/)an.
2. Gehen Sie zu Active Directory, und wählen Sie das Verzeichnis aus, in dem Sie den Anwendungsproxy aktivieren möchten.

    ![Active Directory – Symbol](./media/active-directory-application-proxy-enable/ad_icon.png)
3. Wählen Sie auf der Verzeichnisseite die Option **Konfigurieren** aus, und navigieren Sie nach unten zu **Anwendungsproxy**.
4. Legen Sie die Option **Anwendungsproxydienste für dieses Verzeichnis aktivieren** auf **Aktiviert** fest.

    ![Aktivieren des Anwendungsproxys](./media/active-directory-application-proxy-enable/app_proxy_enable.png)
5. Wählen Sie **Jetzt herunterladen** aus. Der **Download des Azure AD-Anwendungsproxyconnectors** wird gestartet. Lesen und akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Herunterladen**, um die Windows Installer-Datei (.exe) für den Connector zu speichern.

## <a name="install-and-register-the-connector"></a>Installieren und Registrieren des Connectors
1. Führen Sie die Datei **AADApplicationProxyConnectorInstaller.exe** auf dem Server aus, den Sie gemäß den Vorgaben vorbereitet haben.
2. Befolgen Sie die Anweisungen des Assistenten für die Installation.
3. Während der Installation werden Sie aufgefordert, den Connector beim Anwendungsproxy Ihres Azure AD-Mandanten zu registrieren.

   * Geben Sie Ihre globalen Azure AD-Administratoranmeldeinformationen ein. Ihr globaler Administratorenmandant kann von Ihren Microsoft Azure-Anmeldeinformationen abweichen.
   * Achten Sie darauf, dass sich der Administrator, der den Connector registriert, in dem Verzeichnis befindet, in dem Sie auch den Anwendungsproxydienst aktiviert haben. Wenn die Mandantendomäne also beispielsweise „contoso.com“ lautet, muss sich der Administrator als admin@contoso.com oder mit einem anderen Aliasnamen in dieser Domäne anmelden.
   * Falls auf dem Server die Option **Verstärkte Sicherheitskonfiguration für IE** auf **Ein** festgelegt ist, wird der Registrierungsbildschirm unter Umständen blockiert. Befolgen Sie die Anweisungen in der Fehlermeldung, um den Zugriff zuzulassen. Stellen Sie sicher, dass die erweiterte Sicherheit von Internet Explorer deaktiviert ist.
   * Falls die Connectorregistrierung nicht erfolgreich war, helfen Ihnen die Informationen unter [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)weiter.  
4. Nach Abschluss der Installation werden zwei neue Dienste auf dem Server hinzugefügt:

   * **Microsoft AAD-Anwendungsproxyconnector** ermöglicht die Konnektivität.

     * Die **Aktualisierung des Microsoft AAD-Anwendungsproxyconnectors** ist ein automatischer Aktualisierungsdienst. Er sucht regelmäßig nach neuen Versionen des Connectors und aktualisiert ihn bei Bedarf.

     ![Anwendungsproxy-Connectordienste – Screenshot](./media/active-directory-application-proxy-enable/app_proxy_services.png)
5. Klicken Sie im Installationsfenster auf **Fertig stellen** .

Informationen zu Connectors finden Sie unter [Understand Azure AD Application Proxy connectors](application-proxy-understand-connectors.md) (Grundlegendes zu Azure AD-Anwendungsproxyconnectors).

Falls Sie hohe Verfügbarkeit benötigen, sollten Sie mindestens zwei Connectors bereitstellen. Wiederholen Sie zum Bereitstellen weiterer Connectors die Schritte 2 und 3. Jeder Connector muss separat registriert werden.

Wenn Sie den Connector deinstallieren möchten, müssen Sie sowohl den Connector- als auch den Updatedienst deinstallieren. Starten Sie den Computer neu, um den Dienst vollständig zu entfernen.

## <a name="next-steps"></a>Nächste Schritte
Nun können Sie [Anwendungen mit dem Anwendungsproxy veröffentlichen](active-directory-application-proxy-publish.md).

Wenn Sie über Anwendungen verfügen, die sich in separaten Netzwerken oder an unterschiedlichen Standorten befinden, können Sie Connectorgruppen verwenden, um die verschiedenen Connectors in logischen Einheiten anzuordnen. Weitere Informationen zur Verwendung von Anwendungsproxy-Connectors finden Sie unter [Veröffentlichen von Anwendungen in getrennten Netzwerken und an getrennten Speicherorten mit Connectorgruppen](active-directory-application-proxy-connectors.md).
