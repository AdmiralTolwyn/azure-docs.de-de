---
title: "Schützen Ihrer IoT-Bereitstellung | Microsoft Docs"
description: "Dieser Artikel enthält Informationen zum Schützen Ihrer IoT-Bereitstellung."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 95c23341-16b0-4954-b3f2-d2e82ab7b367
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2018
ms.author: dobett
ms.openlocfilehash: 3c2c5c045ad4786ed656fbaf99373644de244570
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/18/2018
---
[!INCLUDE [iot-secure-your-deployment](../../includes/iot-secure-your-deployment.md)]

## <a name="iot-suite-cipher-suites"></a>IoT Suite-Verschlüsselungssammlungen

Azure IoT Suite unterstützt die folgenden Verschlüsselungssammlungen in der angegebenen Reihenfolge.

| Verschlüsselungssammlung | Länge |
| --- | --- |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA384 (0xc028) ECDH secp384r1 (entspricht RSA-Verschlüsselung mit 7680 Bit) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0xc027) ECDH secp256r1 (entspricht RSA-Verschlüsselung mit 3072 Bit) FS |128 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_256\_CBC\_SHA (0xc014) ECDH secp384r1 (entspricht RSA-Verschlüsselung mit 7680 Bit) FS |256 |
| TLS\_ECDHE\_RSA\_WITH\_AES\_128\_CBC\_SHA (0xc013) ECDH secp256r1 (entspricht RSA-Verschlüsselung mit 3072 Bit) FS |128 |
| TLS\_RSA\_WITH\_AES\_256\_GCM\_SHA384 (0x9d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256 (0x9c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA256 (0x3d) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA256 (0x3c) |128 |
| TLS\_RSA\_WITH\_AES\_256\_CBC\_SHA (0x35) |256 |
| TLS\_RSA\_WITH\_AES\_128\_CBC\_SHA (0x2f) |128 |
| TLS\_RSA\_WITH\_3DES\_EDE\_CBC\_SHA (0xa) |112 |

## <a name="see-also"></a>Weitere Informationen
Sie können auch einige andere Features und Funktionen der vorkonfigurierten IoT Suite-Lösungen ausprobieren:

* [Übersicht über die vorkonfigurierte Lösung für vorhersagbaren Wartungsbedarf][lnk-predictive-overview]
* [Häufig gestellte Fragen zu IoT Suite][lnk-faq]

Sie können unter [Verwalten des Zugriffs auf IoT Hub][lnk-devguide-security] im IoT Hub-Entwicklerhandbuch mehr über die Sicherheit von IoT Hub erfahren.

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-devguide-security]: ../iot-hub/iot-hub-devguide-security.md
