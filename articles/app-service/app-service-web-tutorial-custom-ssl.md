---
title: Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie ein benutzerdefiniertes SSL-Zertifikat an Ihre Web-App, ein Back-End Ihrer mobilen App oder Ihre API-App in Azure App Service binden.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 11/30/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: f69bc731b2858c338d7f7b4d347e7107a0f4eeed
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2017
---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>Binden eines vorhandenen benutzerdefinierten SSL-Zertifikats an Azure-Web-Apps

Azure-Web-Apps bietet einen hochgradig skalierbaren Webhosting-Dienst mit Self-Patching. In diesem Tutorial erfahren Sie, wie Sie ein benutzerdefiniertes SSL-Zertifikat binden, das Sie von einer vertrauenswürdigen Zertifizierungsstelle für [Azure-Web-Apps](app-service-web-overview.md) erworben haben. Wenn Sie fertig sind, können Sie am HTTPS-Endpunkt Ihrer benutzerdefinierten DNS-Domäne auf Ihre Web-App zugreifen.

![Web-App mit benutzerdefiniertem SSL-Zertifikat](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Binden Ihres benutzerdefinierten SSL-Zertifikats an App Service
> * Erzwingen von HTTPS für Ihre App
> * Automatisieren der SSL-Zertifikatbindung mit Skripts

> [!NOTE]
> Wenn Sie ein benutzerdefiniertes SSL-Zertifikat benötigen, können Sie dieses direkt im Azure-Portal abrufen und an Ihre Web-App binden. Absolvieren Sie das [Tutorial zu App Service-Zertifikaten](web-sites-purchase-ssl-web-site.md).

## <a name="prerequisites"></a>Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- [Erstellen einer App Service-App](/azure/app-service/)
- [Zuordnen eines benutzerdefinierten DNS-Namens zu Ihrer Web-App](app-service-web-tutorial-custom-domain.md)
- Erwerben eines SSL-Zertifikats von einer vertrauenswürdigen Zertifizierungsstelle
- Den privaten Schlüssel, den Sie zum Signieren der SSL-Zertifikatanforderung verwendet haben.

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>Anforderungen an Ihr SSL-Zertifikat

Das Zertifikat muss sämtliche der folgenden Anforderungen erfüllen, damit Ihr Zertifikat in App Service verwendet werden kann:

* Von einer vertrauenswürdigen Zertifizierungsstelle signiert
* Als kennwortgeschützte PFX-Datei exportiert
* Enthält einen privaten Schlüssel mit mindestens 2048 Bit
* Enthält alle Zwischenzertifikate in der Zertifikatkette

> [!NOTE]
> **ECC-Zertifikate (Elliptic Curve Cryptography, Kryptografie für elliptische Kurven)** können mit App Service funktionieren, werden in diesem Artikel aber nicht behandelt. Erarbeiten Sie mit Ihrer Zertifizierungsstelle die einzelnen Schritte zum Erstellen von ECC-Zertifikaten.

## <a name="prepare-your-web-app"></a>Vorbereiten Ihrer Web-App

Um ein benutzerdefiniertes SSL-Zertifikat an Ihre Web-App zu binden, muss Ihr [App Service-Plan](https://azure.microsoft.com/pricing/details/app-service/) den Tarif **Basic**, **Standard** oder **Premium** aufweisen. Stellen Sie in diesem Schritt sicher, dass sich Ihre Web-App im richtigen Tarif befindet.

### <a name="log-in-to-azure"></a>Anmelden an Azure

Öffnen Sie das [Azure-Portal](https://portal.azure.com).

### <a name="navigate-to-your-web-app"></a>Navigieren zu Ihrer Web-App

Klicken Sie im linken Menü auf **App Services** und anschließend auf den Namen Ihrer Web-App.

![Auswählen der Web-App](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Sie befinden sich auf der Verwaltungsseite Ihrer Web-App.  

### <a name="check-the-pricing-tier"></a>Überprüfen des Tarifs

Scrollen Sie im linken Navigationsbereich auf der Seite Ihrer Web-App zum Abschnitt **Einstellungen**, und wählen Sie **Zentral hochskalieren (App Service-Plan)** aus.

![Menü „Zentral hochskalieren“](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Stellen Sie sicher, dass sich Ihre Web-App nicht im Tarif **Free** oder **Shared** befindet. Der aktuelle Tarif Ihrer Web-App wird durch einen dunkelblauen Rahmen hervorgehoben.

![Überprüfen des Tarifs](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

Benutzerdefiniertes SSL wird in dem Tarif **Free** oder **Shared** nicht unterstützt. Wenn Sie Ihren App Service-Plan zentral hochskalieren müssen, führen Sie die Schritte im nächsten Abschnitt aus. Schließen Sie andernfalls die Seite **Tarif auswählen** und fahren Sie mit [SSL-Zertifikat hochladen und binden](#upload) fort.

### <a name="scale-up-your-app-service-plan"></a>Zentrales Hochskalieren Ihres App Service-Plans

Wählen Sie den Tarif **Basic**, **Standard** oder **Premium** aus.

Klicken Sie auf **Auswählen**.

![Auswählen eines Tarifs](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

Wenn die unten stehende Benachrichtigung angezeigt wird, ist der Skalierungsvorgang abgeschlossen.

![Benachrichtigung zum Hochskalieren](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>Binden Ihres SSL-Zertifikats

Sie können das SSL-Zertifikat in Ihrer Web-App hochladen.

### <a name="merge-intermediate-certificates"></a>Zusammenführen von Zwischenzertifikaten

Wenn Ihre Zertifizierungsstelle Ihnen mehrere Zertifikate in der Zertifikatskette bereitstellt, müssen Sie die Zertifikate nacheinander zusammenführen. 

Öffnen Sie hierzu alle Zertifikate, die Sie erhalten haben, in einem Text-Editor. 

Erstellen Sie eine Datei für das zusammengeführte Zertifikat mit dem Namen _mergedcertificate.crt_. Kopieren Sie den Inhalt der einzelnen Zertifikate in einem Text-Editor in diese Datei. Die Reihenfolge Ihrer Zertifikate sollte der Reihenfolge in der Zertifikatkette folgen, beginnend mit Ihrem Zertifikat und endend mit dem Stammzertifikat. Dies sieht in etwa wie im folgenden Beispiel aus:

```
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

### <a name="export-certificate-to-pfx"></a>Exportieren des Zertifikats als PFX-Datei

Exportieren Sie Ihr benutzerdefiniertes SSL-Zertifikat mit dem privaten Schlüssel, mit dem Ihre Zertifikatanforderung generiert wurde.

Wenn Sie die Zertifikatanforderung mittels OpenSSL generiert haben, haben Sie eine Datei des privaten Schlüssels erstellt. Führen Sie folgenden Befehl aus, um Ihr Zertifikat nach PFX zu exportieren. Ersetzen Sie die Platzhalter _&lt;private-key-file>_ und _&lt;merged-certificate-file>_ mit den Pfaden zu Ihrem privaten Schlüssel und Ihrer zusammengeführten Zertifikatdatei.

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>  
```

Definieren Sie ein Kennwort für den Export, wenn Sie dazu aufgefordert werden. Dieses Kennwort verwenden Sie, wenn Sie Ihr SSL-Zertifikat zu einem späteren Zeitpunkt in App Service hochladen.

Wenn Sie IIS oder _Certreq.exe_ zum Generieren Ihrer Zertifikatanforderung verwendet haben, installieren Sie das Zertifikat auf dem lokalen Computer, und klicken Sie anschließend auf [Zertifikat nach PFX exportieren](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx).

### <a name="upload-your-ssl-certificate"></a>Hochladen Ihres SSL-Zertifikats

Klicken Sie im linken Navigationsbereich Ihrer Web-App auf **SSL-Zertifikate**, um Ihr SSL-Zertifikat hochzuladen.

Klicken Sie auf **Zertifikat hochladen**. 

Wählen Sie unter **PFX-Zertifikatdatei** Ihre PFX-Datei aus. Geben Sie unter **Zertifikatkennwort** das Kennwort ein, das Sie beim Exportieren der PFX-Datei erstellt haben.

Klicken Sie auf **Hochladen**.

![Hochladen des Zertifikats](./media/app-service-web-tutorial-custom-ssl/upload-certificate-private1.png)

Wenn der Upload Ihres Zertifikats in App Service abgeschlossen ist, wird es auf der Seite **SSL-Zertifikate** angezeigt.

![Zertifikat hochgeladen](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>Binden Ihres SSL-Zertifikats

Klicken Sie im Abschnitt **SSL-Bindungen** auf **Bindung hinzufügen**.

Wählen Sie auf der Seite **SSL-Bindung hinzufügen** aus den Dropdownlisten den Domänennamen, der geschützt werden soll, sowie das zu verwendende Zertifikat aus.

> [!NOTE]
> Wenn Sie Ihr Zertifikat hochgeladen haben, der/die Domänenname(n) im Dropdownmenü **Hostname** jedoch nicht angezeigt wird/werden, versuchen Sie, die Browserseite zu aktualisieren.
>
>

Wählen Sie unter **SSL-Typ** aus, ob SSL auf der **[Servernamensanzeige (Server Name Indication, SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** oder der IP basieren soll.

- **SNI-basiertes SSL**: Möglicherweise können mehrere SNI-basierte SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse zugehörige Domänen durch mehrere SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen zur Browserunterstützung finden Sie unter [Servernamensanzeige](http://wikipedia.org/wiki/Server_Name_Indication)).
- **IP-basiertes SSL** : Möglicherweise kann nur eine IP-basierte SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein SSL-Zertifikat geschützt werden. Um mehrere Domänen zu schützen, müssen Sie sie alle mit demselben SSL-Zertifikat schützen. Dies ist die herkömmliche Option für SSL-Bindungen.

Klicken Sie auf **Bindung hinzufügen**.

![Binden eines SSL-Zertifikats](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

Wenn der Upload Ihres Zertifikats in App Service abgeschlossen ist, wird es in den Abschnitten **SSL-Bindungen** angezeigt.

![An die Web-App gebundenes Zertifikat](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>Neuzuordnen eines A-Eintrags für IP-SSL

Wenn Sie kein IP-basiertes SSL in Ihrer Web-App verwenden, fahren Sie mit [Testen von HTTPS für benutzerdefinierte Domänen](#test) fort.

Standardmäßig verwendet Ihre Web-App eine freigegebene öffentliche IP-Adresse. Wenn Sie ein Zertifikat mit IP-basiertem SSL binden, erstellt App Service eine neue, dedizierte IP-Adresse für Ihre Web-App.

Wenn Sie Ihrer Web-App einen A-Eintrag zugeordnet haben, aktualisieren Sie Ihre Domänenregistrierung mit dieser neuen, dedizierten IP-Adresse.

Die Seite **Benutzerdefinierte Domäne** Ihrer Web-App wird mit der neuen, dedizierten IP-Adresse aktualisiert. [Kopieren Sie diese IP-Adresse](app-service-web-tutorial-custom-domain.md#info) und [ordnen Sie dieser neuen IP-Adresse dann den A-Eintrag neu zu](app-service-web-tutorial-custom-domain.md#map-an-a-record).

<a name="test"></a>

## <a name="test-https"></a>Testen von HTTPS

Sie müssen jetzt nur noch sicherstellen, dass HTTPS für Ihre benutzerdefinierte Domäne funktioniert. Rufen Sie in verschiedenen Browsern `https://<your.custom.domain>` auf, um zu sehen, ob Ihre Web-App angeboten wird.

![Portalnavigation zur Azure-App](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Wenn Ihre Web-App Zertifikatüberprüfungsfehler meldet, verwenden Sie wahrscheinlich ein selbstsigniertes Zertifikat.
>
> Wenn dies nicht der Fall ist, haben Sie beim Exportieren des Zertifikats als PFX-Datei eventuell Zwischenzertifikate ausgelassen.

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>Erzwingen von HTTPS

Standardmäßig können Benutzer weiterhin mit HTTP auf Ihre Web-App zugreifen. Sie können alle HTTP-Anforderungen an den HTTPS-Port umleiten.

Klicken Sie im linken Navigationsbereich der Web-App-Seite auf **Benutzerdefinierte Domänen**. Wählen Sie anschließend für **Nur HTTPS** die Option **Ein**.

![Erzwingen von HTTPS](./media/app-service-web-tutorial-custom-ssl/enforce-https.png)

Wenn der Vorgang abgeschlossen ist, navigieren Sie zu einer beliebigen HTTP-URL, die auf Ihre App verweist. Beispiel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

Durch die [Azure CLI](/cli/azure/install-azure-cli) oder durch [Azure PowerShell](/powershell/azure/overview) können Sie mithilfe von Skripts SSL-Bindungen für Ihre Web-App automatisieren.

### <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle

Mit folgendem Befehl wird eine exportierte PFX-Datei hochgeladen und der Fingerabdruck abgerufen.

```bash
thumbprint=$(az webapp config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

Der folgende Befehl fügt mithilfe des Fingerabdrucks aus dem vorherigen Befehl eine SNI-basierte SSL-Bindung hinzu.

```bash
az webapp config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

Mit folgendem Befehl wird eine exportierte PFX-Datei hochgeladen und eine SNI-basierte SSL-Bindung hinzugefügt.

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="public-certificates-optional"></a>Öffentliche Zertifikate (optional)
Sie können [öffentliche Zertifikate](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer/) in Ihre Web-App hochladen. Sie können öffentliche Zertifikate auch für Apps in App Service-Umgebungen verwenden. Wenn Sie das Zertifikat im LocalMachine-Zertifikatspeicher speichern müssen, müssen Sie eine Web-App in der App Service-Umgebung verwenden. Weitere Informationen finden Sie unter [App Service Certificates now supports public certificates (.cer)](https://blogs.msdn.microsoft.com/appserviceteam/2017/11/01/app-service-certificates-now-supports-public-certificates-cer) (App Service-Zertifikate unterstützt jetzt öffentliche Zertifikate [CER].).

![Hochladen eines öffentlichen Zertifikats](./media/app-service-web-tutorial-custom-ssl/upload-certificate-public1.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Binden Ihres benutzerdefinierten SSL-Zertifikats an App Service
> * Erzwingen von HTTPS für Ihre App
> * Automatisieren der SSL-Zertifikatbindung mit Skripts

Im nächsten Tutorial erfahren Sie, wie Sie Azure Content Delivery Network verwenden.

> [!div class="nextstepaction"]
> [Hinzufügen eines CDN (Content Delivery Network) zu einer Azure App Service-Instanz](app-service-web-tutorial-content-delivery-network.md)

Weitere Informationen finden Sie unter [Verwenden eines SSL-Zertifikats in Ihrem Anwendungscode in Azure App Service](app-service-web-ssl-cert-load.md).