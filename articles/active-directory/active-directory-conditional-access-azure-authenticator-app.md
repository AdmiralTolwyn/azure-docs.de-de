---
title: "Azure Authenticator für Android | Microsoft-Dokumentation"
description: "Die Microsoft Azure Authenticator-App kann zur Anmeldung für den Zugriff auf Arbeitsressourcen verwendet werden. Die Azure Authenticator-App teilt Ihnen über eine Warnung auf Ihrem mobilen Gerät mit, dass eine zweistufige Überprüfungsanforderung ansteht."
services: active-directory
documentationcenter: 
author: femila
manager: swadhwa
editor: 
ms.assetid: b7ceca0d-5c9d-45c4-942c-b3a9b6bad36c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: femila
translationtype: Human Translation
ms.sourcegitcommit: 0af5a4e2139a202c7f62f48c7a7e8552457ae76d
ms.openlocfilehash: 5dd6701f25c69f5e870d3add79c312f9aeec6bf4


---
# <a name="azure-authenticator-for-android"></a>Azure Authenticator für Android
Ihr IT-Administrator hat Ihnen eventuell empfohlen, sich über den Microsoft Azure Authenticator für den Zugriff auf Ihre Arbeitsressourcen anzumelden. Diese Anwendung stellt die beiden folgenden Anmeldeoptionen zur Verfügung:

* "Multi-Factor Authentication" ermöglicht Ihnen, Ihre Geschäfts- oder Schulkonten mit zweistufiger Überprüfung zu sichern. Sie melden sich mit Ihnen bekannten Daten an (z. B. Ihrem Kennwort) und schützen das Konto dann noch zusätzlich mit einem Ihnen vorliegenden Sicherheitsschlüssel aus dieser App. Die Azure Authenticator-App teilt Ihnen über eine Warnung auf Ihrem mobilen Gerät mit, dass eine zweistufige Überprüfungsanforderung ansteht. Zeigen Sie die Anforderung einfach in der App an und tippen Sie auf "Überprüfen" oder "Abbrechen". Alternativ werden Sie unter Umständen aufgefordert, die in der App angezeigte Kennung einzugeben.
* "Geschäftskonto" ermöglicht Ihnen, Ihr Android-Telefon oder -Tablet zu einem vertrauenswürdigen Gerät zu machen und einmaliges Anmelden (SSO) für Firmenanwendungen bereitzustellen. Ihr IT-Administrator verlangt von Ihnen unter Umständen, ein Geschäftskonto hinzuzufügen, um auf Unternehmensressourcen zuzugreifen. Mit SSO können Sie sich einmal anmelden und die Anmeldung für alle Anwendungen, die Ihr Unternehmen Ihnen zur Verfügung gestellt hat, automatisch nutzen.

## <a name="installing-the-azure-authenticator-app"></a>Installieren der Azure Authenticator-App
Sie können die Azure Authenticator-App über den Google Play Store installieren.
Die Anweisungen zum Hinzufügen eines Geschäftskontos über Samsung Android-Geräte unterscheiden sich geringfügig von den Anweisungen für Geräte ohne Samsung Android. Beide Arten von Anweisungen sind unten aufgeführt.

## <a name="adding-the-work-account-from-samsung-android-device"></a>Hinzufügen eines Geschäftskontos über das Samsung Android-Gerät
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Hinzufügen eines Geschäftskontos über den Startbildschirm der App
Die folgenden Anweisungen gelten für Telefone mit Samsung GS3 oder höher bzw. für Tablets mit Note2 oder höher.

1. Stimmen Sie auf dem Startbildschirm der App den Microsoft-Software-Lizenzbedingungen zu.
2. Klicken Sie auf dem Bildschirm "Konto aktivieren" auf das Kontextmenü rechts, und wählen Sie **Geschäftskonto**aus.
3. Wählen Sie auf dem Bildschirm „Konto hinzufügen“ die Option** Geschäftskonto**aus.
4. Klicken Sie auf dem Bildschirm "Geräteadministrator aktivieren" auf **Aktivieren**.
5. Aktivieren Sie auf dem Bildschirm "Datenschutzrichtlinien" das Kontrollkästchen, und klicken Sie auf **Bestätigen**.
6. Geben Sie auf dem Bildschirm "Arbeitsbereichverknüpfung" die Benutzer-ID ein, die Ihre Organisation bereitgestellt hat, und klicken Sie auf **Verknüpfen**.
7. Geben Sie zum Anmelden bei der Azure Authenticator-App Ihr Organisationskonto und Ihr Kennwort ein, und klicken Sie auf **Anmelden**.
8. Der nächste Bildschirm, auf dem Informationen zur mehrstufigen Authentifizierung (MFA) anzeigt werden, soll zusätzliche Sicherheit bieten und ist optional. Dieser Bildschirm wird angezeigt, wenn Ihre Firma oder Schule zum Erstellen eines Geschäftskontos eine zweistufige Authentifizierung verlangt. Er enthält Anweisungen zur weiteren Verifizierung Ihres Kontos.
9. Auf dem Bildschirm „Arbeitsbereichverknüpfung“ wird die Meldung**Verknüpfen Ihres Arbeitsbereichs**angezeigt. Die Azure Authenticator-App versucht, das Gerät mit dem Arbeitsplatz zu verbinden.
10. Auf dem nächsten Bildschirm müsste die Meldung angezeigt werden, dass der Arbeitsbereich verknüpft wurde.

> [!NOTE]
> Sie dürfen auf dem Gerät ein einzelnes Geschäftskonto einrichten.
> 
> 

### <a name="adding-the-work-account-from-the-settings-menu"></a>Hinzufügen des Geschäftskontos über das Menü "Einstellungen"
Nach der Installation der Azure Authenticator-App können Sie auch über den Android Account Manager ein Geschäftskonto erstellen.

1. Navigieren Sie im Menü „Einstellungen“ zu **Konten**, und klicken Sie auf **Konto hinzufügen**.
2. Führen Sie die Schritte 3 bis 10 in der Prozedur "Hinzufügen eines Geschäftskontos über den Startbildschirm der App" aus, um ein Geschäftskonto hinzuzufügen.

## <a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Hinzufügen des Geschäftskontos auf einem Gerät ohne Samsung Android
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Hinzufügen eines Geschäftskontos über den Startbildschirm der App
1. Stimmen Sie auf dem Startbildschirm der App den Microsoft-Software-Lizenzbedingungen zu.
2. Klicken Sie auf dem Bildschirm "Konto aktivieren" auf das Kontextmenü rechts, und wählen Sie **Geschäftskonto**aus.
3. Klicken Sie auf dem Bildschirm "Konten" auf **Konto hinzufügen**.
4. Wenn Sie den Bildschirm „Konten“ sehen, klicken Sie auf **Konto hinzufügen**. Wenn ein Geschäftskonto bereits zuvor erstellt wurde, sehen Sie den Bildschirm "Sync", auf dem das vorhandene Geschäftskonto angezeigt wird. Sie können das Geschäftskonto beibehalten, indem Sie einfach auf den Zurück-Pfeil zum Startbildschirm tippen. Alternativ können Sie das zu entfernende Konto auswählen und ein neues Geschäftskonto erstellen. Geben Sie auf dem Bildschirm "Arbeitsbereichverknüpfung" die Benutzer-ID ein, die Ihre Organisation bereitgestellt hat, und klicken Sie auf "Verknüpfen".
5. Geben Sie zum Anmelden bei der Azure Authenticator-App Ihr Organisations-Konto und Ihr Kennwort ein, und klicken Sie auf **Anmelden**.
6. Der nächste Bildschirm, auf dem Informationen zur mehrstufigen Authentifizierung (MFA) anzeigt werden, soll zusätzliche Sicherheit bieten und ist optional. Dieser Bildschirm wird angezeigt, wenn Ihre Firma oder Schule zum Erstellen eines Geschäftskontos eine zweistufige Authentifizierung verlangt. Er enthält Anweisungen zur weiteren Verifizierung Ihres Kontos.
7. Klicken Sie auf dem nächsten Bildschirm auf **OK** . Ändern Sie den Namen des Zertifikats nicht.
   Auf dem Bildschirm "Arbeitsbereichverknüpfung" wird die Meldung "Verknüpfen Ihres Arbeitsbereichs" angezeigt. Die Azure Authenticator-App versucht, das Gerät mit dem Arbeitsplatz zu verbinden.
   Auf dem nächsten Bildschirm müsste die Meldung angezeigt werden, dass der Arbeitsbereich verknüpft wurde.

> [!NOTE]
> Sie dürfen auf dem Gerät ein einzelnes Geschäftskonto einrichten.
> 
> 

Nach der Installation der Azure Authenticator-App können Sie auch über den Android Account Manager ein Geschäftskonto erstellen.

1. Navigieren Sie im Menü **Einstellungen** zu „Konten“, und klicken Sie auf **Konto hinzufügen**.
2. Führen Sie die Schritte 2 bis 7 in der Prozedur "Hinzufügen eines Geschäftskontos über den Startbildschirm der App" aus, um ein Geschäftskonto hinzuzufügen.

### <a name="how-to-find-out-which-version-is-installed"></a>Ermitteln der installierten Version
1. Sie können ermitteln, welche Version der Azure Authenticator-App und der zugehörigen Dienste auf Ihrem Gerät installiert ist.
2. Klicken Sie im Popupmenü auf **Info**.
3. Auf dem Bildschirm "Info" werden die Dienste der App und die auf Ihrem Gerät installierten Versionen angezeigt.

### <a name="sending-log-files-to-report-issues"></a>Senden von Protokolldateien zum Melden von Problemen
1. Befolgen Sie die Anleitung des Microsoft Support, um einen Vorfall mit der Azure Authenticator-App zu melden und eine Vorfallnummer zu erhalten. Senden Sie dann folgendermaßen die Protokolldateien zur zugewiesenen Vorfallnummer:
2. Klicken Sie im Popupmenü auf **Protokollierung**.
3. Wenn Sie einen offenen Vorfall beim Microsoft Support haben, notieren Sie sich die Vorfallnummer (Sie benötigen sie in einem späteren Schritt). Wenn Sie noch keinen Supportfall erstellt haben und Hilfe benötigen, befolgen Sie dem Leitfaden unter [Microsoft Support](https://support.microsoft.com/en-us/contactus), um einen neuen Vorfall zu öffnen.
4. Klicken Sie auf dem Protokollierungsbildschirm auf **Jetzt senden**.
5. Wählen Sie den gewünschten E-Mail-Anbieter aus.
6. Wenn Sie bereits einen offenen Vorfall beim Microsoft Support haben, wenden Sie sich an den Supportmitarbeiter, dem Ihr Problem zugewiesen wurde, um zu erfahren, wie Sie die Protokolldaten senden und dem Vorfall zuordnen lassen können. Der Supportmitarbeiter nennt Ihnen die E-Mail-Adresse und die Betreffzeile. Wenn Sie noch keinen Supportfall haben, befolgen Sie die Anleitung des Microsoft Support, um einen neuen Vorfall zu öffnen.
7. Geben Sie in die Zeilen **To** (An) und **Subject** (Betreff) die Informationen ein, die Sie vom Microsoft Support erhalten haben.
8. Die Azure Authenticator-App fügt die Protokolldatei an die von Ihnen gesendete E-Mail an. Beschreiben Sie das aufgetretene Problem, aktualisieren Sie die Empfängerliste (optional), und senden Sie die E-Mail.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Löschen des Geschäftskontos und Verlassen des Arbeitsbereichs
Sie können das erstellte Geschäftskonto jederzeit wie folgt entfernen:

**So löschen Sie das Geschäftskonto über das Menü "Einstellungen"**

1. Wählen Sie im Account Manager die Option **Geschäftskonto**aus.
2. Wählen Sie auf dem Bildschirm „Geschäftskonto“ unter **Allgemeine Einstellungen** die Option **Kontoeinstellungen – Ihr Arbeitsbereichnetzwerk verlassen** aus.
3. Wählen Sie auf dem Bildschirm **Workplace Join** die Option **Verlassen** aus.
4. Klicken Sie auf **OK** , wenn die Meldung "Möchten Sie den Arbeitsbereich wirklich verlassen?" angezeigt wird.
5. Dadurch wird sichergestellt, dass Sie Ihr Geschäftskonto aus Ihrem Arbeitsbereich gelöscht haben.

> [!NOTE]
> Es wird empfohlen, die Option „Konto entfernen“ nicht zum Löschen eines Geschäftskontos zu verwenden, da diese Option in einigen früheren Versionen von Android möglicherweise nicht funktioniert.
> 
> 

## <a name="uninstalling-the-app"></a>Deinstallieren der App
Vor dem Deinstallieren der App müssen auf einem Samsung Android-Gerät Geräteadministratorrechte wie im Folgenden beschrieben entfernt werden. 

1. Wählen Sie in **Einstellungen** unter **System** den Eintrag **Sicherheit** aus.
2. Klicken Sie in der **Geräteverwaltung** auf **Geräteadministratoren**. Stellen Sie sicher, dass das Kontrollkästchen neben **Azure Authenticator** deaktiviert ist.

## <a name="troubleshooting"></a>Problembehandlung
Wenn die Fehlermeldung **Schlüsselspeicherfehler** angezeigt wird, liegt dies möglicherweise daran, dass Sie den Sperrbildschirm nicht mit einer PIN eingerichtet haben. Um dieses Problem zu umgehen, deinstallieren Sie die Azure Authenticator-App, konfigurieren Sie eine PIN für den Sperrbildschirm, und installieren Sie die App neu.




<!--HONumber=Jan17_HO1-->


