---
title: "Azure Storage-Explorer – Leitfaden zur Problembehandlung | Microsoft-Dokumentation"
description: "Übersicht über die beiden Debugfeatures von Azure"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: 2f62de428d1915b1e070350a2837f24c3486f8c7
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/21/2018
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage-Explorer – Leitfaden zur Problembehandlung

Bei der Vorschauversion von Microsoft Azure Storage-Explorer handelt es sich um eine eigenständige App, über die Sie unter Windows, macOS und Linux komfortabel mit Azure Storage-Daten arbeiten können. Die App kann mit Storage-Konten verbunden werden, die in Azure, nationalen Clouds und Azure Stack gehostet werden.

In diesem Leitfaden sind Lösungen für häufig aufgetretene Probleme im Storage-Explorer zusammengefasst.

## <a name="sign-in-issues"></a>Probleme bei der Anmeldung

Es werden nur AAD-Konten (Azure Active Directory) unterstützt. Wenn Sie ein AD FS-Konto verwenden, wird davon ausgegangen, dass die Anmeldung beim Storage-Explorer nicht funktioniert. Bevor Sie fortfahren, starten Sie die Anwendung neu, und prüfen Sie, ob die Probleme so behoben werden können.

### <a name="error-self-signed-certificate-in-certificate-chain"></a>Fehler: selbstsigniertes Zertifikat in der Zertifikatkette

Für diesen Fehler gibt es verschiedene Ursachen. Die beiden gängigsten Ursachen sind die folgenden:

1. Die App ist über einen „transparenten Proxy“ verbunden, d.h., ein Server (z.B. Ihr Firmenserver) fängt HTTPS-Datenverkehr ab, entschlüsselt ihn und verschlüsselt ihn dann mit einem selbstsignierten Zertifikat.

2. Sie führen eine Anwendung aus, z.B. eine Antivirensoftware, die in die empfangenen HTTPS-Nachrichten ein selbstsigniertes SSL-Zertifikat einfügt.

Wenn im Storage-Explorer eines dieser Probleme auftritt, kann nicht mehr festgestellt werden, ob die empfangene HTTPS-Nachricht manipuliert wurde. Wenn Sie über eine Kopie des selbstsignierten Zertifikats verfügen, können Sie festlegen, dass es im Storage-Explorer als vertrauenswürdig eingestuft wird. Wenn Sie nicht sicher sind, woher das Zertifikat eingefügt wurde, können Sie dies mit den folgenden Schritten feststellen:

1. Installieren Sie OpenSSL.

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (eine Light-Version sollte ausreichend sein)

    - Mac und Linux: sollte im Betriebssystem enthalten sein

2. Führen Sie OpenSSL aus.

    - Windows: Öffnen Sie das Installationsverzeichnis, klicken Sie auf **/bin/**, und doppelklicken Sie dann auf **openssl.exe**.
    - Mac und Linux: Führen Sie **openssl** über ein Terminal aus.

3. Führen Sie s_client -showcerts -connect microsoft.com:443 aus.

4. Suchen Sie nach selbstsignierten Zertifikaten. Wenn Sie nicht sicher sind, welche Zertifikate selbstsigniert sind, suchen Sie nach Stellen, an denen der Antragssteller („s:“) und der Zertifikataussteller („i:“) identisch sind.

5. Wenn Sie selbstsignierte Zertifikate gefunden haben, kopieren Sie für jedes Zertifikat den gesamten Inhalt von einschließlich **-----BEGIN CERTIFICATE-----** bis **-----END CERTIFICATE-----**, und fügen Sie ihn in eine neue CER-Datei ein.

6. Öffnen Sie den Storage-Explorer, klicken Sie auf **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**, suchen Sie über die Dateiauswahl die erstellten CER-Dateien, wählen Sie sie aus, und öffnen Sie sie.

Wenn Sie mit den oben beschriebenen Schritten keine selbstsignierten Zertifikate finden, wenden Sie sich über das Feedbacktool an uns, um weitere Unterstützung zu erhalten.

### <a name="unable-to-retrieve-subscriptions"></a>Abonnements können nicht abgerufen werden

Wenn Sie nach erfolgreicher Anmeldung Ihre Abonnements nicht abrufen können, führen Sie zum Beheben dieses Problems die folgenden Schritte aus:

- Melden Sie sich beim Azure-Portal an, um zu überprüfen, ob Ihr Konto Zugriff auf die Abonnements hat.

- Stellen Sie sicher, dass die Anmeldung mit der richtigen Umgebung (Azure, Azure China, Azure Deutschland, Azure US-Regierung oder Benutzerdefinierte Umgebung/Azure Stack) erfolgt ist.

- Wenn Sie sich hinter einem Proxy befinden, stellen Sie sicher, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.

- Entfernen Sie das Konto, und fügen Sie es wieder hinzu.

- Löschen Sie die folgenden Dateien aus Ihrem Stammverzeichnis (d.h. „C:\Users\ContosoUser“), und fügen Sie dann das Konto erneut hinzu:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Überprüfen Sie bei der Anmeldung in der Entwicklertools-Konsole (durch Drücken von F12), ob Fehlermeldungen angezeigt werden:

![Entwicklertools](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>Authentifizierungsseite wird nicht angezeigt

Wenn die Authentifizierungsseite nicht angezeigt wird, führen Sie zum Beheben dieses Problems die folgenden Schritte aus:

- Je nach Verbindungsgeschwindigkeit kann es eine Weile dauern, bis die Anmeldeseite geladen wird. Warten Sie mindestens eine Minute, bevor Sie das Dialogfeld für die Authentifizierung schließen.

- Wenn Sie sich hinter einem Proxy befinden, stellen Sie sicher, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.

- Zeigen Sie durch Drücken der Taste F12 die Entwicklerkonsole an. Prüfen Sie in den Antworten in der Entwicklerkonsole, ob diese Aufschluss darüber geben, warum die Authentifizierung nicht funktioniert.

### <a name="cannot-remove-account"></a>Konto kann nicht entfernt werden

Wenn Sie ein Konto nicht entfernen können oder der Link für die erneute Authentifizierung keine Wirkung zeigt, führen Sie zum Beheben dieses Problems die folgenden Schritte aus:

- Löschen Sie die folgenden Dateien aus Ihrem Stammverzeichnis, und fügen Sie dann das Konto wieder hinzu:

    - .adalcache

    - .devaccounts

    - .extaccounts

- Wenn Sie Storage-Ressourcen mit SAS-Anfügung entfernen möchten, löschen Sie die folgenden Dateien:

    - Ordner „%AppData%/StorageExplorer“ für Windows

    - „/Users/<Ihr_Name>/Library/Application Support/StorageExplorer“ für Mac

    - „~/.config/StorageExplorer“ für Linux

> [!NOTE]
>  Nach dem Löschen der oben aufgeführten Dateien müssen Sie sich an Ihren Konten erneut anmelden.

## <a name="proxy-issues"></a>Proxyprobleme

Vergewissern Sie sich zunächst, dass Sie die folgenden Informationen richtig eingegeben haben:

- Proxy-URL und Portnummer

- Benutzername und Kennwort, sofern für den Proxy erforderlich

### <a name="common-solutions"></a>Gängige Lösungen

Wenn weiterhin Probleme auftreten, gehen Sie zur Behebung folgendermaßen vor:

- Wenn Sie ohne Verwendung des Proxys eine Verbindung mit dem Internet herstellen können, überprüfen Sie, ob der Storage-Explorer ohne aktivierte Proxyeinstellungen ausgeführt wird. Wenn dies der Fall ist, liegt möglicherweise ein Problem mit den Proxyeinstellungen vor. Identifizieren Sie die Probleme zusammen mit dem Administrator für den Proxyserver.

- Überprüfen Sie, ob andere Anwendungen, die den Proxyserver verwenden, wie erwartet ausgeführt werden.

- Stellen Sie sicher, dass Sie über den Webbrowser eine Verbindung mit dem Microsoft Azure-Portal herstellen können.

- Vergewissern Sie sich, dass Antworten von den Dienstendpunkten empfangen werden können. Geben Sie eine der Endpunkt-URLs im Browser ein. Wenn Sie eine Verbindung herstellen können, sollten Sie eine XML-Antwort wie „InvalidQueryParameterValue“ oder eine ähnliche erhalten.

- Wenn eine andere Person auch den Storage-Explorer mit Ihrem Proxyserver verwendet, überprüfen Sie, ob diese Person eine Verbindung herstellen kann. Wenn dies der Fall ist, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden.

### <a name="tools-for-diagnosing-issues"></a>Tools zum Diagnostizieren von Problemen

Wenn Sie über Netzwerktools verfügen, z.B. Fiddler für Windows, können Sie die Probleme unter Umständen wie folgt diagnostizieren:

- Wenn Sie über den Proxy arbeiten müssen, müssen Sie das Netzwerktool möglicherweise so konfigurieren, dass eine Verbindung über den Proxy hergestellt wird.

- Überprüfen Sie die vom Netzwerktool verwendete Portnummer.

- Geben Sie die URL des lokalen Hosts und die Portnummer des Netzwerktools als Proxyeinstellungen im Storage-Explorer ein. Bei korrekter Eingabe beginnt das Netzwerktool mit der Protokollierung der vom Storage-Explorer an Verwaltungs- und Dienstendpunkte gesendeten Netzwerkanforderungen. Wenn Sie in einem Browser beispielsweise „https://cawablobgrs.blob.core.windows.net/“ für den Blobendpunkt eingeben, erhalten Sie eine Antwort ähnlich der folgenden, in der angegeben wird, dass die Ressource vorhanden ist, Sie jedoch keinen Zugriff darauf haben.

![Codebeispiel](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>Kontaktaufnahme mit dem Administrator für den Proxyserver

Wenn Ihre Proxyeinstellungen richtig sind, müssen Sie sich möglicherweise an den Administrator für den Proxyserver wenden und

- sicherstellen, dass der Proxy nicht den Datenverkehr zu Azure Verwaltungs- oder Ressourcenendpunkten blockiert.

- das von Ihrem Proxyserver verwendete Authentifizierungsprotokoll überprüfen. NTLM-Proxys werden derzeit vom Storage-Explorer nicht unterstützt.

## <a name="unable-to-retrieve-children-error-message"></a>Fehlermeldung: Untergeordnete Elemente können nicht abgerufen werden

Wenn Sie die Verbindung mit Azure über einen Proxy hergestellt haben, überprüfen Sie, ob die Proxyeinstellungen richtig sind. Wenn Ihnen vom Besitzer des Abonnements oder Kontos Zugriff auf eine Ressource erteilt wurde, vergewissern Sie sich, dass Sie über die Berechtigung zum Lesen oder Auflisten für diese Ressource verfügen.

### <a name="issues-with-sas-url"></a>Probleme mit der SAS-URL
Wenn Sie eine Verbindung mit einem Dienst über eine SAS-URL herstellen und dieser Fehler auftritt:

- Stellen Sie sicher, dass die URL die erforderlichen Berechtigungen zum Lesen oder Auflisten von Ressourcen umfasst.

- Vergewissern Sie sich, dass die URL nicht abgelaufen ist.

- Wenn die SAS-URL auf einer Zugriffsrichtlinie basiert, stellen Sie sicher, dass die Zugriffsrichtlinie nicht aufgehoben wurde.

Wenn Sie versehentlich eine ungültige SAS-URL angefügt haben und diese nicht trennen können, führen Sie diese Schritte aus:
1.  Drücken Sie im Storage-Explorer F12, um das Fenster mit den Entwicklertools zu öffnen.
2.  Klicken Sie auf der Registerkarte „Anwendung“ und dann in der Struktur auf der linken Seite auf „Lokaler Speicher“ > „file://“.
3.  Suchen Sie den Schlüssel, der dem Diensttyp des problematischen SAS-URI zugeordnet ist. Wenn z.B. der fehlerhafte SAS-URI für einen Blobcontainer gilt, suchen Sie nach dem Schlüssel mit dem Namen `StorageExplorer_AddStorageServiceSAS_v1_blob`.
4.  Der Wert des Schlüssels sollte ein JSON-Array sein. Suchen Sie das Objekt, das dem fehlerhaften URI zugeordnet ist, und entfernen Sie es.
5.  Drücken Sie STRG+R, um den Storage-Explorer neu zu laden.

## <a name="linux-dependencies"></a>Linux-Abhängigkeiten

Für andere Linux-Distributionen als Ubuntu 16.04 müssen Sie einige Abhängigkeiten ggf. manuell installieren. Die folgenden Pakete sind im Allgemeinen erforderlich:
* libgconf-2-4
* libsecret
* Aktuelle GCC

Abhängig von Ihrer Distribution müssen Sie möglicherweise weitere Pakete installieren. Die [Anmerkungen zu dieser Version](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) des Storage-Explorers enthalten spezielle Schritte für einige Distributionen.

## <a name="next-steps"></a>Nächste Schritte

Wenn sich das Problem durch keine dieser Lösungen beheben lässt, übermitteln Sie das Problem über das Feedbacktool unter Angabe Ihrer E-Mail-Adresse und möglichst vieler Details, sodass wir Sie zur Behebung des Problems kontaktieren können.

Klicken Sie dazu auf das Menü **Hilfe** und dann auf **Feedback senden**.

![Feedback](./media/storage-explorer-troubleshooting/4022503_en_1.png)
