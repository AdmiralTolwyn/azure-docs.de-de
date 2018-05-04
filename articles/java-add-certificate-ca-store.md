---
title: Hinzufügen eines Zertifikats zum Java-Zertifizierungsstellenspeicher
description: Erfahren Sie, wie Sie ein Zertifikat der Zertifizierungsstelle (CA) zum Java-Zertifizierungsstellen-Zertifikatspeicher (cacerts) für den Twilio-Dienst oder Azure Service Bus hinzufügen.
services: ''
documentationcenter: java
author: rmcmurray
manager: mbaldwin
ms.assetid: d3699b0a-835c-43fb-844d-9c25344e5cda
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/11/2018
ms.author: robmcm
ms.openlocfilehash: 1c432aa9da9637675262313d935edd38bd7b698b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/18/2018
---
# <a name="adding-a-certificate-to-the-java-ca-certificates-store"></a>Hinzufügen eines Zertifikats zum Java CA-Zertifikatspeicher
In den folgenden Schritten wird gezeigt, wie ein Zertifikat der Zertifizierungsstelle (Certificate Authority, CA) zum Java CA-Zertifikatspeicher bzw. cacerts-Speicher hinzugefügt wird. Das verwendete Beispiel steht für das Zertifizierungsstellenzertifikat, das für den Twilio-Dienst erforderlich ist. Weiter unten in diesem Thema wird beschrieben, wie das Zertifizierungsstellenzertifikat für Azure Service Bus installiert wird. 

Sie können „keytool“ zum Hinzufügen des Zertifizierungsstellenzertifikats verwenden, bevor Sie das JDK komprimieren und zum Ordner **approot** des Azure-Projekts hinzufügen. Optional können Sie auch eine Azure-Startaufgabe ausführen, bei der zum Hinzufügen des Zertifikats „keytool“ zum Einsatz kommt. (In diesem Beispiel wird davon ausgegangen, dass Sie vor dem Zippen Ihres JDK ein Zertifizierungsstellenzertifikat hinzufügen.) Darüber hinaus wird in diesem Beispiel ein bestimmtes Zertifizierungsstellenzertifikat verwendet. Die Schritte zum Abrufen eines anderen Zertifizierungsstellenzertifikats und Importieren dieses Zertifikats in den cacerts-Speicher sind allerdings nahezu identisch.

## <a name="to-add-a-certificate-to-the-cacerts-store"></a>So fügen Sie dem cacerts-Speicher ein Zertifikat hinzu
1. Führen Sie an einer Administratoreingabeaufforderung, die für den Ordner **jdk\jre\lib\security** des JDK festgelegt ist, Folgendes aus, um die bereits installierten Zertifikate anzuzeigen:
   
    `keytool -list -keystore cacerts`
   
    Sie werden aufgefordert, das Kennwort für den Speicher einzugeben. Das Standardkennwort lautet **changeit**. (Wenn Sie das Kennwort ändern möchten, finden Sie weitere Informationen in der Dokumentation zu „keytool“ unter <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.) Bei diesem Beispiel wird davon ausgegangen, dass das Zertifikat mit dem MD5-Fingerabdruck „67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4“ nicht aufgeführt ist und dass Sie dieses Zertifikat importieren möchten. Dieses bestimmte Zertifikat ist für den Twilio API-Dienst erforderlich.
2. Rufen Sie das Zertifikat in der Liste der Zertifikate ab, die unter [GeoTrust-Stammzertifikate](http://www.geotrust.com/resources/root-certificates/)aufgeführt sind. Klicken Sie mit der rechten Maustaste auf den Link für das Zertifikat mit der Seriennummer 35:DE:F4:CF, und speichern Sie das Zertifikat im Ordner **jdk\jre\lib\security**. In diesem Beispiel wurde das Zertifikat in einer Datei mit dem Namen **Equifax\_Secure\_Certificate\_Authority.cer** gespeichert.
3. Importieren Sie das Zertifikat mithilfe des folgenden Befehls:
   
    `keytool -keystore cacerts -importcert -alias equifaxsecureca -file Equifax_Secure_Certificate_Authority.cer`
   
    Wenn Sie aufgefordert werden, die Vertrauenswürdigkeit eines Zertifikats mit dem MD5-Fingerabdruck „67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4“ zu bestätigen, geben Sie **y**ein.
4. Führen Sie den folgenden Befehl aus, um sicherzustellen, dass das Zertifizierungsstellenzertifikat erfolgreich importiert wurde:
   
    `keytool -list -keystore cacerts`
5. Komprimieren Sie das JDK, und fügen Sie es dem Ordner **approot** des Azure-Projekts hinzu.

Weitere Informationen zu „keytool“ finden Sie unter <http://docs.oracle.com/javase/7/docs/technotes/tools/windows/keytool.html>.

## <a name="azure-root-certificates"></a>Azure-Stammzertifikate
Anwendungen, bei denen Azure-Dienste (z. B. Azure Service Bus) zum Einsatz kommen, müssen das Baltimore CyberTrust-Stammzertifikat als vertrauenswürdig einstufen. (Am 15. April 2013 begann Azure mit der Migration von "GTE CyberTrust Global Root" zu "Baltimore CyberTrust Root". Bis diese Migration letztendlich abgeschlossen war, vergingen mehrere Monate.)

Das Baltimore-Zertifikat ist ggf. im cacerts-Speicher vorhanden. Führen Sie also zunächst den Befehl **keytool -list** aus, um herauszufinden, ob dieses Zertifikat bereits installiert wurde.

Wenn Sie das Zertifikat "Baltimore CyberTrust Root" hinzufügen müssen, sei darauf hingewiesen, dass es die Seriennummer "02:00:00:b9" und den SHA1-Fingerabdruck "d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74" verwendet. Es kann von <https://cacert.omniroot.com/bc2025.crt>heruntergeladen, in einer lokalen Datei mit der Erweiterung **.cer** gespeichert und dann mithilfe von **keytool** wie in den oben beschriebenen Schritten importiert werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu den von Azure verwendeten Stammzertifikaten finden Sie unter [Azure Root Certificate Migration](http://blogs.msdn.com/b/windowsazure/archive/2013/03/15/windows-azure-root-certificate-migration.aspx)(in englischer Sprache).

Weitere Informationen zu Java finden Sie im Artikel [Azure für Java-Entwickler](/java/azure).

