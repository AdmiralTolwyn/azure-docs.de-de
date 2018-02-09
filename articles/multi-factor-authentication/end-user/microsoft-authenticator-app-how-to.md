---
title: "Microsoft Authenticator-App für Mobiltelefone | Microsoft Docs"
description: "Erfahren Sie, wie Sie Azure Authenticator auf die neueste Version aktualisieren können."
services: multi-factor-authentication
documentationcenter: 
author: barlanmsft
manager: mtillman
ms.assetid: 3065a1ee-f253-41f0-a68d-2bd84af5ffba
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: barlan
ms.reviewer: librown
ms.custom: H1Hack27Feb2017, end-user
ms.openlocfilehash: 1532054a9463d710685d3f865d2e26ee7ff5014f
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2018
---
# <a name="get-started-with-the-microsoft-authenticator-app"></a>Erste Schritte mit der Microsoft Authenticator-App
Die Microsoft Authenticator-App bietet eine zusätzliche Sicherheitsstufe in Ihrem Geschäfts-, Schul- oder Unikonto (z.B. bsimon@contoso.com) oder Ihrem Microsoft-Konto (z.B. bsimon@outlook.com).

Die App funktioniert auf zwei Arten:

* **Benachrichtigung**. Die App kann dazu beitragen, unberechtigten Zugriff auf Konten zu verhindern und betrügerische Transaktionen zu beenden, indem eine Benachrichtigung an Ihr Smartphone oder Tablet gesendet wird. Zeigen Sie einfach die Benachrichtigung an, und wählen Sie **Überprüfen**, wenn Sie den Zugriff zulassen möchten. Wählen Sie andernfalls **Verweigern**.
* **Überprüfungscode** Die App kann als Softwaretoken zum Generieren eines OATH-Überprüfungscodes verwendet werden. Nachdem Sie Benutzernamen und Kennwort eingegeben haben, geben Sie auf dem Anmeldebildschirm den von der App bereitgestellten Code ein. Der Überprüfungscode kann als zweite Authentifizierungsmethode eingegeben werden.

Die Microsoft Authenticator-App ersetzt die Azure Authenticator-App. Die Azure Authenticator-App kann weiterhin verwendet werden. Sollten Sie sich jedoch entscheiden, zur neuen Microsoft Authenticator-App zu wechseln, finden Sie in diesem Artikel hilfreiche Informationen.  

## <a name="opt-in-for-two-step-verification"></a>Abonnieren der zweistufigen Überprüfung

Die Microsoft-Authenticator-App kann nicht ohne zusätzliche Konfiguration verwendet werden. Konfigurieren Sie Ihre Konten so, dass Sie nach der Anmeldung mit Ihrem Benutzernamen und Kennwort zu einer zweiten Überprüfungsmethode aufgefordert werden.

Für ein Geschäfts-, Schul- oder Unikonto können Sie diese Funktion in der Regel nicht selbst auswählen. Stattdessen meldet sich ein Sicherheitsadministrator in Ihrem Auftrag an und setzt Sie darüber in Kenntnis, dass Sie Überprüfungsmethoden für Ihr Konto registrieren müssen. Wenn dieses Szenario auf Sie zutrifft, erhalten Sie weitere Informationen unter [Was ist Azure Multi-Factor Authentication?](multi-factor-authentication-end-user.md).

Bei einem persönlichen Konto müssen Sie die zweistufige Überprüfung selbst einrichten. Wenn Sie ein Microsoft-Konto besitzen, finden Sie weitere Informationen unter [Zweistufige Überprüfung](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

Sie können den Microsoft-Authenticator auch mit Nicht-Microsoft-Konten verwenden. Die Funktion heißt dann möglicherweise nicht „zweistufige Überprüfung“, aber Sie sollten sie unter „Sicherheit“ oder „Anmeldeeinstellungen“ finden.

## <a name="install-the-app"></a>Installieren der App
Die Microsoft Authenticator-App ist für [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) und [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071) verfügbar.

## <a name="add-accounts-to-the-app"></a>Hinzufügen von Konten zur App
Verwenden Sie für jedes Konto, das Sie zur Microsoft Authenticator-App hinzufügen möchten, eines der folgenden Verfahren:

### <a name="add-a-personal-microsoft-account-to-the-app"></a>Hinzufügen eines persönliches Microsoft-Kontos zur App

Für ein persönliches Microsoft-Konto (das Sie zur Anmeldung bei Outlook.com, Xbox, Skype usw. verwenden) müssen Sie sich lediglich in der Microsoft Authenticator-App bei Ihrem Konto anmelden.

### <a name="add-a-work-or-school-account-to-the-app-using-the-qr-code-scanner"></a>Hinzufügen eines Geschäfts-, Schul- oder Unikontos zur App über den QR-Code-Scanner
1. Rufen Sie die Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung auf.  Informationen dazu, wie Sie auf diese Seite gelangen, finden Sie unter [Ändern der Sicherheitseinstellungen](multi-factor-authentication-end-user-manage-settings.md#where-to-find-the-settings-page).
2. Aktivieren Sie das Kontrollkästchen neben **Authenticator-App**, und wählen Sie dann **Konfigurieren**.

    ![Schaltfläche „Konfigurieren“ auf der Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung](./media/authenticator-app-how-to/azureauthe.png)

    Daraufhin wird ein Bildschirm mit einem QR-Code angezeigt.

    ![Bildschirm, der den QR-Code enthält](./media/authenticator-app-how-to/barcode2.png)
3. Öffnen Sie die Microsoft Authenticator-App. Wählen Sie auf dem Bildschirm **Konten** die Option **+**, und geben Sie dann an, dass Sie ein Arbeits- oder Schulkonto hinzufügen möchten.
4. Verwenden Sie die Kamera, um den QR-Code zu scannen, und wählen Sie **Fertig** , um den Bildschirm mit dem QR-Code zu schließen.

    Wenn die Kamera nicht ordnungsgemäß funktioniert, können Sie den [QR-Code und die URL manuell eingeben](#add-an-account-to-the-app-manually).

5. Wenn die App Ihren Kontonamen mit einem sechsstelligen Code darunter anzeigt, ist der Vorgang abgeschlossen.

    ![Bildschirm „Konten“](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-manually"></a>Manuelles Hinzufügen eines Kontos zur App
1. Rufen Sie die Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung auf.  Informationen dazu, wie Sie auf diese Seite gelangen, finden Sie unter [Ändern der Sicherheitseinstellungen](multi-factor-authentication-end-user-manage-settings.md).
2. Wählen Sie **Konfigurieren**aus.

    ![Schaltfläche „Konfigurieren“ auf der Seite mit Ihren Einstellungen für die Sicherheitsüberprüfung](./media/authenticator-app-how-to/azureauthe.png)

    Daraufhin wird ein Bildschirm mit einem QR-Code angezeigt.  Notieren Sie den Code und die URL.

    ![Bildschirm, der den QR-Code und die URL enthält](./media/authenticator-app-how-to/barcode2.png)
3. Öffnen Sie die Microsoft Authenticator-App. Wählen Sie auf dem Bildschirm **Konten** die Option **+**, und geben Sie dann an, dass Sie ein Arbeits- oder Schulkonto hinzufügen möchten.

4. Wählen Sie im Scanner **Code manuell eingeben**.

    ![Bildschirm zum Scannen eines QR-Codes](./media/multi-factor-authentication-end-user-first-time/scan2.png)
5. Geben Sie den Code und die URL in die entsprechenden Felder in der App ein, und wählen Sie dann **Fertig stellen**.

    ![Bildschirm für die Eingabe von Code und URL](./media/authenticator-app-how-to/manual.png)

6. Wenn die App Ihren Kontonamen mit einem sechsstelligen Code darunter anzeigt, ist der Vorgang abgeschlossen.

    ![Bildschirm „Konten“](./media/authenticator-app-how-to/accounts.png)

### <a name="add-an-account-to-the-app-using-your-devices-fingerprint-or-facial-recognition-capabilities"></a>Hinzufügen eines Kontos zur App mithilfe des Fingerabdrucks Ihres Geräts oder Gesichtserkennungsfunktionen
Möglicherweise ist in Ihrem Unternehmen eine PIN erforderlich, um die Überprüfungsherausforderung abzuschließen. Die Microsoft Authenticator-App kann den Fingerabdruck Ihres Geräts oder Gesichtserkennungsfunktionen anstelle einer PIN verwenden. Um dies zu Ihrer ersten Überprüfung in der App einzurichten, sehen Sie eine Option, um Touch ID (für iOS) oder die Fingerabdruckidentifikation stattdessen zu verwenden. 

Um Touch ID für Microsoft Authenticator einzurichten, müssen Sie eine normale Überprüfungsherausforderung mit einer PIN ausführen. Microsoft Authenticator führt eine automatische Einrichtung für Geräte durch, die Touch ID unterstützen. 

![Überprüfung der Touch ID-Einrichtung](./media/authenticator-app-how-to/touchid1.png)

Danach müssen Sie zum Überprüfen Ihrer Anmeldung nur noch die erhaltene Pushbenachrichtigung wählen und Ihren Fingerabdruck scannen lassen, anstatt die PIN einzugeben.

![Pushbenachrichtigung](./media/authenticator-app-how-to/touchid2.png)

## <a name="use-the-app-when-you-sign-in"></a>Verwenden der App bei der Anmeldung

Sobald Sie Ihr Konto der App hinzugefügt haben, werden Sie möglicherweise aufgefordert, eine Testüberprüfung auszuführen, um sicherzustellen, dass alles richtig konfiguriert wurde. Danach sind Sie fertig! Vor der nächsten Anmeldung müssen Sie keine weiteren Schritte durchführen.

Wenn Sie die Verwendung von Überprüfungscodes in der App festgelegt haben, werden diese auf der Startseite angezeigt. Sie ändern sich alle 30 Sekunden, sodass Sie stets über einen neuen Code verfügen, wenn Sie einen benötigen. Sie müssen sie jedoch erst beachten, wenn Sie sich anmelden und zur Eingabe eines Überprüfungscodes aufgefordert werden.  
