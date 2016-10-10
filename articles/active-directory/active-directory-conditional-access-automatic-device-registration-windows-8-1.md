<properties
	pageTitle="Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte | Microsoft Azure"
	description=" Schritte zum Konfigurieren einer Gruppenrichtlinie für in eine Windows 8.1-Domäne eingebundene Geräte für die automatische Registrierung bei Azure AD. "
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2016"
	ms.author="Markvi"/>

# Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 8.1-Geräte

Sie können eine Active Directory-Gruppenrichtlinie verwenden, um Ihre in eine Domäne eingebundenen Windows 8.1-Geräte für die automatische Registrierung bei Azure AD zu konfigurieren. Um die Gruppenrichtlinie zu konfigurieren, benötigen Sie mindestens einen in einer Domäne eingebundenen Windows Server 2012 R2- oder Windows 8.1-Computer mit installierter Gruppenrichtlinienverwaltung. Nach der Installation der Gruppenrichtlinie für Ihre Domäne wird jeder Domänenbenutzer, der sich bei dem Computer anmeldet, automatisch und im Hintergrund mit einem Geräteobjekt bei Azure AD registriert. Für jeden registrierten Benutzer des physischen Geräts gibt es ein Geräteobjekt in Azure AD. Stellen Sie sicher, dass Sie die Voraussetzungen in "Automatische Geräteregistrierung mit Azure Active Directory für in Domänen eingebundene Windows-Geräte" gelesen haben und erfüllen.

>[AZURE.NOTE]
 Aktuelle Anweisungen zum Einrichten der automatischen Geräteregistrierung finden Sie unter [Einrichten der automatischen Registrierung von in die Domäne eingebundenen Windows-Geräten bei Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## Konfigurieren der Gruppenrichtlinie für Ihre in eine Domäne eingebundenen Windows 8.1-Geräte

1. Öffnen Sie den Server-Manager, und navigieren Sie zu **Tools** > **Gruppenrichtlinienverwaltung**.
2. Navigieren Sie von der Gruppenrichtlinienverwaltung aus zu dem Domänenknoten, der der Domäne entspricht, in der Sie **Automatische Arbeitsbereichverknüpfung** aktivieren möchten.
3. Klicken Sie mit der rechten Maustaste auf Ihr neues **Gruppenrichtlinienobjekt**, und wählen Sie dann **Neu** aus. Geben Sie Ihrem Gruppenrichtlinienobjekt einen Namen, z. B. **Automatische Arbeitsbereichverknüpfung**. Klicken Sie auf **OK**.
4. Klicken Sie mit der rechten Maustaste auf Ihr neues Gruppenrichtlinienobjekt, und wählen Sie dann **Bearbeiten** aus.
5. Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Arbeitsbereichverknüpfung**.
6. Klicken Sie mit der rechten Maustaste auf "Clientcomputer automatisch in Arbeitsbereich einbinden", und wählen Sie dann **Bearbeiten**.
7. Aktivieren Sie das Optionsfeld "Aktiviert", und klicken Sie dann auf "Übernehmen". Klicken Sie auf **OK**.
8. Sie können das Gruppenrichtlinienobjekt jetzt mit einem Speicherort Ihrer Wahl verknüpfen. Um diese Richtlinie für alle in eine Domäne eingebundenen Windows 8.1-Geräte in Ihrer Organisation zu aktivieren, verknüpfen Sie die Gruppenrichtlinie mit der Domäne.

## Aufheben der Registrierung Ihrer in eine Domäne eingebundenen Windows 8.1-Geräte

Sie können die Registrierung Ihrer in eine Domäne eingebundenen Windows 8.1-Geräte wie folgt aufheben: Ändern Sie die im vorherigen Abschnitt erstellten Gruppenrichtlinieneinstellungen für die Arbeitsbereichverknüpfung. Legen Sie die Richtlinie "Clientcomputer automatisch in Arbeitsbereich einbinden" auf "Deaktiviert" fest. Dadurch wird verhindert, dass neue Geräte automatisch in den Arbeitsbereich eingebunden werden.

Heben Sie die Registrierung der vorhandenen in eine Domäne eingebundenen Windows 8.1-Computer mithilfe einer der folgenden zwei Optionen auf:

* Option 1: **Aufhebung der Registrierung eines in eine Domäne eingebundenen Windows 8.1-Geräts mithilfe von PC-Einstellungen**
  1. Navigieren Sie auf dem Windows 8.1-Gerät zu **PC-Einstellungen** > **Netzwerk** > **Arbeitsbereich**.
  2. Wählen Sie **Verlassen** aus. Dieser Vorgang muss für jeden Domänenbenutzer wiederholt werden, der sich bei dem Computer angemeldet hat und automatisch in den Arbeitsbereich eingebunden wurde.

* Option 2: Aufhebung der Registrierung eines in eine Domäne eingebundenen Windows 8.1-Geräts mithilfe eines Skripts
  	1. Öffnen Sie eine Eingabeaufforderung auf dem Windows 8.1-Computer, und führen Sie den folgenden Befehl aus: ` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Dieser Befehl muss im Kontext jedes Domänenbenutzers ausgeführt werden, der sich bei dem Computer angemeldet hat.

##Ereignisanzeige & Fehler für in eine Domäne eingebundene Windows 8.1-Geräte

Das Windows-Ereignisprotokoll auf einem Windows 8.1-Computer zeigt Fehlermeldungen im Zusammenhang mit der Geräteregistrierung an. Sie finden dort sowohl Meldungen zu erfolgreichen als auch fehlgeschlagenen Ereignissen.

Das Ereignisprotokoll finden Sie in der Ereignisanzeige unter **Anwendungs- und Dienstprotokolle** > **Microsoft** > **Windows > Arbeitsbereichverknüpfung**.

##Zusätzliche Details

Die Gruppenrichtlinie aktiviert einen geplanten Task auf dem System, der im Kontext des Benutzers ausgeführt und bei Anmeldung eines Benutzers ausgelöst wird. Der Task registriert den Benutzer und das Gerät im Hintergrund bei Azure AD, nachdem die Anmeldung abgeschlossen ist. Den geplanten Task finden Sie auf Windows 8.1.-Geräten in der Aufgabenplanungsbibliothek unter **Microsoft** > **Windows** > **Arbeitsbereichverknüpfung**. Der Task wird für alle Active Directory-Benutzer ausgeführt, die sich bei dem Computer anmelden, und registriert sie.

## Weitere Themen
- [Azure Active Directory-Geräteregistrierung – Übersicht](active-directory-conditional-access-device-registration-overview.md)
- [Automatische Geräteregistrierung bei Azure Active Directory für in Domänen eingebundene Windows 10-Geräte](active-directory-conditional-access-automatic-device-registration.md)
- [Konfigurieren der automatischen Geräteregistrierung für in eine Domäne eingebundene Windows 7-Geräte](active-directory-conditional-access-automatic-device-registration-windows7.md)

<!---HONumber=AcomDC_0928_2016-->