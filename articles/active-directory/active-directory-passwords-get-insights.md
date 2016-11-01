<properties
	pageTitle="Operative Einblicke: Kennwortverwaltung in Azure AD | Microsoft Azure"
	description="Dieser Artikel beschreibt, wie Sie Berichte verwenden, um einen Einblick in die Vorgänge zur Kennwortverwaltung in Ihrem Unternehmen zu erhalten."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>

# Operative Einblicke durch Berichte zur Kennwortverwaltung

> [AZURE.IMPORTANT] **Sind Sie hier, weil Sie Probleme bei der Anmeldung haben?** Wenn ja, helfen Ihnen die Informationen zum [Ändern und Zurücksetzen Ihres eigenen Kennworts](active-directory-passwords-update-your-own-password.md) weiter.

In diesem Abschnitt wird beschrieben, wie Sie Berichte zur Kennwortverwaltung in Azure Active Directory verwenden können, um die Verwendung der Kennwortzurücksetzung und -änderung durch Benutzer in Ihrer Organisation zu verfolgen.

- [**Übersicht über Berichte zur Kennwortverwaltung**](#overview-of-password-management-reports)
- [**Anzeigen von Berichten für die Kennwortverwaltung**](#how-to-view-password-management-reports)
- [**Anzeigen der Aktivität zur Registrierung für die Kennwortzurücksetzung in Ihrer Organisation**](#view-password-reset-registration-activity)
- [**Anzeigen der Aktivität zur Kennwortzurücksetzung in Ihrer Organisation**](#view-password-reset-activity)

## Übersicht über Berichte zur Kennwortverwaltung
Sobald Sie die Kennwortzurücksetzung bereitgestellt haben, besteht einer der nächsten Schritte üblicherweise darin, herauszufinden, wie diese in Ihrer Organisation verwendet wird. Beispielsweise möchten Sie einen Einblick gewinnen, wie Benutzer sich für die Kennwortzurücksetzung registrieren oder wie viele Kennwortzurücksetzungen in den letzten paar Tagen stattgefunden haben. Im Folgenden sind einige der häufigsten Fragen aufgeführt, die Sie anhand der Berichte zur Kennwortverwaltung beantworten können, die derzeit im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zur Verfügung stehen:

- Wie viele Personen haben sich für die Kennwortzurücksetzung registriert?
- Wer hat sich für das Zurücksetzen von Kennwörtern registriert?
- Welche Daten registrieren die Benutzer?
- Wie viele Personen haben ihre Kennwörter in den letzten 7 Tagen zurückgesetzt?
- Welche Methoden werden von Benutzern und Administratoren am häufigsten zum Zurücksetzen von Kennwörtern eingesetzt?
- Welche Probleme treten häufig für Benutzer oder Administratoren bei dem Versuch auf, das Kennwort zurückzusetzen?
- Welche Administratoren setzen häufig ihre eigenen Kennwörter zurück?
- Gibt es verdächtige Aktivitäten beim Zurücksetzen des Kennworts?


## Anzeigen von Berichten für die Kennwortverwaltung
Zum Finden der Berichte für die Kennwortverwaltung führen Sie die folgenden Schritte aus:

1.	Klicken Sie im **Azure-Verwaltungsportal** auf die [Active Directory-Erweiterung](https://manage.windowsazure.com).
2.	Wählen Sie Ihr Verzeichnis aus der Liste aus, die im Portal angezeigt wird.
3.	Klicken Sie auf die Registerkarte **Berichte**.
4.	Sehen Sie im Abschnitt **Aktivitätsprotokolle** nach.
5.	Wählen Sie entweder den Bericht **Aktivität "Zurücksetzen des Kennworts"** oder den Bericht **Aktivität "Registrierung für Zurücksetzen des Kennworts"** aus.

    ![][001]

## Zugreifen auf Berichte zur Kennwortverwaltung über eine API
Ab August 2015 unterstützen die Berichte und Ereignisse von Azure AD das Abrufen aller Informationen, die in den Berichten „Kennwortzurücksetzung“ und „Registrierung für Zurücksetzen des Kennworts“ enthalten sind.

Zum Zugreifen auf diese Daten müssen Sie eine kleine App oder ein Skript schreiben, um sie von unseren Servern abzurufen. [Hier erfahren Sie, wie Sie die ersten Schritte mit der Azure AD Reporting-API ausführen](active-directory-reporting-api-getting-started.md).

Nachdem Sie über ein funktionierendes Skript verfügen, sollten Sie sich als Nächstes mit den Kennwortzurücksetzungs- und Registrierungsereignissen befassen, die Sie zum Erfüllen Ihrer Szenarien abrufen können.

- [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent): Listet die Spalten auf, die für Ereignisse zum Zurücksetzen des Kennworts verfügbar sind.
- [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent): Listet die Spalten auf, die für Ereignisse zum Registrieren der Kennwortzurücksetzung verfügbar sind.

## Anzeigen der Aktivität "Registrierung für Zurücksetzen des Kennworts"

Der Bericht "Aktivität "Registrierung für Zurücksetzen des Kennworts"" zeigt alle Registrierungen für die Kennwortzurücksetzung, die in Ihrer Organisation erfolgt sind. Eine Registrierung für die Kennwortzurücksetzung wird in diesem Bericht für jeden Benutzer angezeigt, der erfolgreich Authentifizierungsinformationen beim Registrierungsportal für die Kennwortzurücksetzung registriert hat ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)).

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Zum Herunterladen**: Ja, über eine CSV-Datei

    ![][002]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

- **Benutzer** – Der Benutzer, der versucht hat, sich für die Kennwortzurücksetzung zu registrieren.
- **Rolle** – Die Rolle des Benutzers im Verzeichnis.
- **Datum und Uhrzeit** – Datum und Uhrzeit des Versuchs.
- **Registrierte Daten** – Die Authentifizierungsdaten, die vom Benutzer während der Registrierung für die Kennwortzurücksetzung bereitgestellt wurden.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

Column|Zulässige Werte und ihre Bedeutung
---|---
Registrierte Daten| **Alternative E-Mail-Adresse** – Benutzer hat für die Authentifizierung eine alternative E-Mail-Adresse oder eine E-Mail-Adresse zur Authentifizierung verwendet.<p><p>**Bürotelefon** – Benutzer hat seine Bürotelefonnummer zur Authentifizierung verwendet.<p>**Mobiltelefon** – Benutzer hat die Nummer seines Mobiltelefons oder seines Authentifizierungstelefons zum Authentifizieren verwendet.<p>**Sicherheitsfragen** – Benutzer hat Sicherheitsfragen zur Authentifizierung verwendet.<p>**Eine beliebige Kombination der oben genannten Daten (z.B. alternative E-Mail-Adresse und Mobiltelefon)** – tritt auf, wenn eine Richtlinie für die zweistufige Überprüfung angegeben ist, und zeigt, welche beiden Methoden der Benutzer zur Authentifizierung seiner Anforderung zum Zurücksetzen des Kennworts verwendet hat.

## Anzeigen der Aktivität "Zurücksetzen des Kennworts"

Dieser Bericht zeigt alle Versuche der Kennwortzurücksetzung an, die in Ihrer Organisation erfolgt sind.

- **Max. Zeitraum**: 1 Monat
- **Maximale Anzahl von Zeilen**: unbegrenzt
- **Zum Herunterladen**: Ja, über eine CSV-Datei

    ![][003]

### Beschreibung der Berichtsspalten
In der folgende Liste werden alle Berichtsspalten im Detail beschrieben:

1. **Benutzer** – Der Benutzer, der versucht hat, ein Kennwort zurückzusetzen (basierend auf dem Feld "Benutzer-ID", das bereitgestellt wird, wenn der Benutzer ein Kennwort zurückzusetzen versucht).
2. **Rolle** – Die Rolle des Benutzers im Verzeichnis.
3. **Datum und Uhrzeit** – Datum und Uhrzeit des Versuchs.
4. **Verwendete Methode(n)** – Die Authentifizierungsmethoden, die der Benutzer für diesen Zurücksetzungsvorgang verwendet hat.
5. **Ergebnis** – Das Endergebnis des Vorgangs zum Zurücksetzen des Kennworts.
6. **Details** – Die Einzelheiten dazu, warum die Kennwortzurücksetzung zu dem entsprechenden Wert geführt hat. Enthält auch alle Maßnahmen, die Sie ergreifen können, um einen unerwarteten Fehler zu beheben.

### Beschreibung der Berichtswerte
Die folgende Tabelle beschreibt die verschiedenen Werte, die für die einzelnen Spalten zulässig sind:

Column|Zulässige Werte und ihre Bedeutung
---|---
Verwendete Methoden|**Alternative E-Mail-Adresse** – Benutzer hat für die Authentifizierung eine alternative E-Mail-Adresse oder eine E-Mail-Adresse zur Authentifizierung verwendet.<p>**Bürotelefon** – Benutzer hat seine Bürotelefonnummer zur Authentifizierung verwendet.<p>**Mobiltelefon** – Benutzer hat die Nummer seines Mobiltelefons oder seines Authentifizierungstelefons zum Authentifizieren verwendet.<p>**Sicherheitsfragen** – Benutzer hat Sicherheitsfragen zur Authentifizierung verwendet.<p>**Eine beliebige Kombination der oben genannten Daten (z.B. alternative E-Mail-Adresse und Mobiltelefon)** – tritt auf, wenn eine Richtlinie für die zweistufige Überprüfung angegeben ist, und zeigt, welche beiden Methoden der Benutzer zur Authentifizierung seiner Anforderung zum Zurücksetzen des Kennworts verwendet hat.
Ergebnis|**Vorzeitig beendet** – der Benutzer hat die Kennwortzurücksetzung gestartet, den Vorgang jedoch mittendrin beendet und nicht abgeschlossen.<p>**Blockiert** – das Konto des Benutzers wurde an der Kennwortzurücksetzung gehindert, weil die Seite zur Kennwortzurücksetzung oder eine einzelne Überprüfungsmethode zur Kennwortzurücksetzung in einem Zeitraum von 24 Stunden zu häufig verwendet wurde.<p>**Abgebrochen** – der Benutzer hat die Kennwortzurücksetzung gestartet, aber dann auf die Schaltfläche „Abbrechen“ geklickt, um die Sitzung mittendrin abzubrechen.<p>**Administrator kontaktiert** – beim Benutzer ist während der Sitzung ein Problem aufgetreten, das er nicht lösen konnte. Daher hat der Benutzer auf den Link „Wenden Sie sich an Ihren Administrator“ geklickt, statt die Kennwortzurücksetzung abzuschließen.<p>**Fehler** – der Benutzer konnte ein Kennwort nicht zurücksetzen, wahrscheinlich weil der Benutzer nicht für die Verwendung dieses Features konfiguriert wurde (z.B. keine Lizenz, fehlende Informationen für die Authentifizierung, Kennwort lokal verwaltet ohne Aktivierung der Rückschreibungsfunktion).<p>**Erfolgreich** – die Kennwortzurücksetzung war erfolgreich.
Details|Beachten Sie die folgende Tabelle.

### Zulässige Werte für die Spalte "Details"
Nachfolgend finden Sie die Liste der Ergebnistypen, die Sie im Bericht zur Aktivität "Zurücksetzen des Kennworts" erwarten können:

Details | Ergebnistyp
----|----
Benutzer hat nach Abschluss der Überprüfung per E-Mail vorzeitig beendet. | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet. | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet. | Vorzeitig beendet
Benutzer hat nach Abschluss der Überprüfung per Sicherheitsfragen vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach Eingabe der Benutzer-ID vorzeitig beendet.| Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per E-Mail vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-SMS vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Mobiltelefon-Sprachanruf vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Bürotelefon-Sprachanruf vorzeitig beendet.|Vorzeitig beendet
Benutzer hat nach dem Start der Überprüfung per Sicherheitsfragen vorzeitig beendet.| Vorzeitig beendet
Benutzer hat vor Auswahl eines neuen Kennworts vorzeitig beendet.| Vorzeitig beendet
Benutzer hat während Auswahl eines neuen Kennworts vorzeitig beendet.| Vorzeitig beendet
Benutzer hat zu viele ungültige SMS-Überprüfungscodes eingegeben und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat die Überprüfung per Mobiltelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat die Überprüfung per Bürotelefon-Sprachanruf zu oft versucht und ist für 24 Stunden blockiert. |Blockiert
Benutzer hat zu häufig versucht, die Sicherheitsfragen zu beantworten, und ist für 24 Stunden blockiert.| Blockiert
Benutzer hat zu oft versucht, eine Telefonnummer zu überprüfen, und ist für 24 Stunden blockiert.|Blockiert
Benutzer hat vor Übergabe der erforderlichen Authentifizierungsmethoden abgebrochen.|Abgebrochen
Benutzer hat vor Übermittlung eines neuen Kennworts abgebrochen.|Abgebrochen
Benutzer hat nach Versuch der Überprüfung per E-Mail den Administrator kontaktiert. |Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-SMS den Administrator kontaktiert.|Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Mobiltelefon-Sprachanruf den Administrator kontaktiert.|Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Bürotelefon-Sprachanruf den Administrator kontaktiert. |Administrator kontaktiert
Benutzer hat nach Versuch der Überprüfung per Sicherheitsfrage den Administrator kontaktiert.|Administrator kontaktiert
Die Kennwortzurücksetzung ist für diesen Benutzer nicht aktiviert. Aktivieren Sie die Kennwortzurücksetzung auf der Konfigurationsregisterkarte, um dieses Problem zu beheben.| Fehler
Der Benutzer hat keine Lizenz. Sie können dem Benutzer eine Lizenz hinzufügen, um dieses Problems zu beheben.|Fehler
Benutzer hat versucht, die Zurücksetzung von einem Gerät ohne aktivierte Cookies durchzuführen.| Fehler
Im Benutzerkonto sind nicht genügend Authentifizierungsmethoden definiert. Fügen Sie Authentifizierungsinformationen hinzu, um dieses Problem zu beheben.|Fehler
Das Kennwort des Benutzers wird lokal verwaltet. Sie können die Kennwortrückschreibung aktivieren, um dieses Problem zu beheben.|Fehler
Wir konnten den Dienst zum Zurücksetzen Ihres lokalen Kennworts nicht erreichen. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers.|Fehler
Beim Zurücksetzen des lokalen Kennworts des Benutzers ist ein Problem aufgetreten. Überprüfen Sie das Ereignisprotokoll des Synchronisierungscomputers. | Fehler
Der Benutzer ist kein Mitglied der Benutzergruppe für Kennwortzurücksetzung. Fügen Sie diesen Benutzer dieser Gruppe hinzu, um dieses Problem zu beheben.|Fehler
Die Kennwortzurücksetzung wurde für diesen Mandanten vollständig deaktiviert. [Hier](http://aka.ms/ssprtroubleshoot) finden Sie Informationen zur Lösung des Problems. | Fehler
Benutzer hat das Kennwort erfolgreich zurückgesetzt.|Succeeded

## Links zu Informationen zur Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD:

* **Sind Sie hier, weil Sie Probleme bei der Anmeldung haben?** Wenn ja, helfen Ihnen die Informationen zum [Ändern und Zurücksetzen Ihres eigenen Kennworts](active-directory-passwords-update-your-own-password.md) weiter.
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) – Erfahren Sie mehr über die sechs verschiedenen Komponenten des Diensts und deren Funktionen.
* [**Erste Schritte**](active-directory-passwords-getting-started.md) – Erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben.
* [**Anpassen**](active-directory-passwords-customize.md) – Erfahren Sie, wie Sie das Aussehen und Verhalten des Diensts an die Anforderungen Ihrer Organisation anpassen.
* [**Best Practices**](active-directory-passwords-best-practices.md) – Erfahren Sie, wie Sie Kennwörter in Ihrer Organisation schnell bereitstellen und effektiv verwalten.
* [**Häufig gestellte Fragen**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen.
* [**Problembehandlung**](active-directory-passwords-troubleshoot.md) – Erfahren Sie, wie Sie Probleme mit dem Dienst schnell beheben.
* [**Weitere Informationen**](active-directory-passwords-learn-more.md) – Erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts.



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"

<!---HONumber=AcomDC_0713_2016-->