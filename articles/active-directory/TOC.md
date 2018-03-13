# [Azure Active Directory-Dokumentation](index.md)

# Übersicht
## [Was ist Azure Active Directory?](active-directory-whatis.md)
## [Informationen zur Azure-Identitätsverwaltung](identity-fundamentals.md)
## [Grundlegendes zu Azure-Identitätslösungen](understand-azure-identity-solutions.md)
## [Auswählen einer Hybrididentitätslösung](choose-hybrid-identity-solution.md)
## [Zuordnen von Azure-Abonnements](active-directory-how-subscriptions-associated-directory.md)
## [Häufig gestellte Fragen](active-directory-faq.md)
## [Neuerungen](whats-new.md)


# Erste Schritte
## [Erste Schritte mit Azure AD](get-started-azure-ad.md)
## [Registrieren für Azure AD Premium](active-directory-get-started-premium.md)
## [Hinzufügen eines benutzerdefinierten Domänennamens](add-custom-domain.md)
## [Konfigurieren des Unternehmensbrandings](customize-branding.md)
## [Hinzufügen von Benutzern zu Azure AD](add-users-azure-active-directory.md)
## [Zuweisen von Lizenzen zu Benutzern](license-users-groups.md)
## [Konfigurieren der Self-Service-Kennwortzurücksetzung](active-directory-passwords-getting-started.md)


# Anleitung
## Planen und Entwerfen
### [Informationen zur Azure AD-Architektur](active-directory-architecture.md)
### [Zuordnen von Benutzeransprüchen in Azure Active Directory](active-directory-claims-mapping.md)
### [Bereitstellen einer Hybrididentitätslösung](active-directory-hybrid-identity-design-considerations-overview.md)
#### Bestimmen der Anforderungen
##### [Identität](active-directory-hybrid-identity-design-considerations-business-needs.md)
##### [Verzeichnissynchronisierung](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
##### [Mehrstufige Authentifizierung](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)
##### [Strategie zum Identitätslebenszyklus](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)
#### [Plan für Datensicherheit](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)
##### [Datenschutz](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)
##### [Content Management](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)
##### [Zugriffssteuerung](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)
##### [Reaktion auf Vorfälle](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)
#### Planen des Identitätslebenszyklus
##### [Aufgaben](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)
##### [Strategie für die Einführung](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)
#### [Nächste Schritte](active-directory-hybrid-identity-design-considerations-nextsteps.md)
#### [Vergleich von Tools](active-directory-hybrid-identity-design-considerations-tools-comparison.md)

## Verwalten von Benutzern
### [Hinzufügen neuer Benutzer zu Azure AD](add-users-azure-active-directory.md)
### [Verwalten von Benutzerprofilen](active-directory-users-profile-azure-portal.md)
### [Freigeben von Konten](active-directory-sharing-accounts.md)
### [Zuweisen von Benutzern zu Administratorrollen](active-directory-users-assign-role-azure-portal.md)
### [Wiederherstellen eines gelöschten Benutzers](active-directory-users-restore.md)
### [Hinzufügen von Gastbenutzern aus einem anderen Verzeichnis (B2B)](active-directory-b2b-what-is-azure-ad-b2b.md)
#### [Administratoren, die B2B-Benutzer hinzufügen](active-directory-b2b-admin-add-users.md)
#### [Information-Worker, die B2B-Benutzer hinzufügen](active-directory-b2b-iw-add-users.md)
#### [API und Anpassung](active-directory-b2b-api.md)
#### [Code- und Azure PowerShell-Beispiele](active-directory-b2b-code-samples.md)
#### [Beispiel für Self-Service-Registrierungsportal](active-directory-b2b-self-service-portal.md)
#### [Einladungs-E-Mail](active-directory-b2b-invitation-email.md)
#### [Einlösen der Einladung](active-directory-b2b-redemption-experience.md)
#### [Hinzufügen von B2B-Benutzern ohne Einladung](active-directory-b2b-add-user-without-invite.md)
#### [Bedingter Zugriff für B2B](active-directory-b2b-mfa-instructions.md)
#### [B2B-Freigaberichtlinien](active-directory-b2b-delegate-invitations.md)
#### [Hinzufügen eines B2B-Benutzers zu einer Rolle](active-directory-b2b-add-guest-to-role.md)
#### [Dynamische Gruppen und B2B-Benutzer](active-directory-b2b-dynamic-groups.md)
#### [Überwachung und Berichte](active-directory-b2b-auditing-and-reporting.md)
#### [B2B für Hybridorganisationen](active-directory-b2b-hybrid-organizations.md)
#### [B2B und externe Office 365-Freigaben](active-directory-b2b-o365-external-user.md)
#### [B2B-Lizenzierung](active-directory-b2b-licensing.md)
#### [Aktuelle Einschränkungen](active-directory-b2b-current-limitations.md)
#### [HÄUFIG GESTELLTE FRAGEN](active-directory-b2b-faq.md)
#### [Problembehandlung für B2B](active-directory-b2b-troubleshooting.md)
#### [Grundlegendes zu B2B-Benutzern](active-directory-b2b-user-properties.md)
#### [B2B-Benutzertoken](active-directory-b2b-user-token.md)
#### [B2B für integrierte Azure AD-Apps](active-directory-b2b-configure-saas-apps.md)
#### [Zuordnen von B2B-Benutzeransprüchen](active-directory-b2b-claims-mapping.md)
#### [Gegenüberstellung von B2B-Zusammenarbeit und B2C](active-directory-b2b-compare-b2c.md)
#### [Anfordern von Unterstützung für B2B](active-directory-b2b-support.md)

## [Verwalten von Gruppen und Mitgliedern](active-directory-manage-groups.md)
### Verwalten von Gruppen
#### [Azure-Portal](active-directory-groups-create-azure-portal.md)
#### [Azure PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
### [Verwalten von Gruppenmitgliedern](active-directory-groups-members-azure-portal.md)
### [Verwalten von Gruppenbesitzern](active-directory-accessmanagement-managing-group-owners.md)
### [Verwalten der Gruppenmitgliedschaft](active-directory-groups-membership-azure-portal.md)
### [Zuweisen von Lizenzen mithilfe von Gruppen](active-directory-licensing-whatis-azure-portal.md)
#### [Zuweisen von Lizenzen zu einer Gruppe](active-directory-licensing-group-assignment-azure-portal.md)
#### [Bestimmen und Beheben von Lizenzproblemen in einer Gruppe](active-directory-licensing-group-problem-resolution-azure-portal.md)
#### [Migrieren einzelner lizenzierter Benutzer zur gruppenbasierten Lizenzierung](active-directory-licensing-group-migration-azure-portal.md)
#### [Migrieren von Benutzern zwischen Produktlizenzen](active-directory-licensing-group-product-migration.md)
#### [Zusätzliche Szenarien für die Gruppenbasierte Lizenzierung](active-directory-licensing-group-advanced.md)
#### [Azure PowerShell-Beispiele für die gruppenbasierte Lizenzierung](active-directory-licensing-ps-examples.md)
#### [Referenz zu Produkten und Serviceplänen in Azure AD](active-directory-licensing-product-and-service-plan-reference.md)
### [Einrichten des Office 365-Gruppenablaufs](active-directory-groups-lifecycle-azure-portal.md)
### [Erzwingen einer Benennungsrichtlinie für Gruppen](groups-naming-policy.md)
### [Anzeigen aller Gruppen](active-directory-groups-view-azure-portal.md)
### [Hinzufügen des Gruppenzugriffs auf SaaS-Apps](active-directory-accessmanagement-group-saasapps.md)
### [Wiederherstellen einer gelöschten Office 365-Gruppe](active-directory-groups-restore-azure-portal.md)
### Verwalten von Gruppeneinstellungen
#### [Azure-Portal](active-directory-groups-settings-azure-portal.md)
#### [Cmdlets](active-directory-accessmanagement-groups-settings-cmdlets.md)
### Erstellen erweiterter Regeln
#### [Azure-Portal](active-directory-groups-dynamic-membership-azure-portal.md)
### [Einrichten von Self-Service-Gruppen](active-directory-accessmanagement-self-service-group-management.md)
### [Problembehandlung](active-directory-accessmanagement-troubleshooting.md)

## [Verwalten von Berichten](active-directory-reporting-azure-portal.md)
### [Anmeldungsaktivität](active-directory-reporting-activity-sign-ins.md)
### [Überwachungsaktivität](active-directory-reporting-activity-audit-logs.md)
### [Gefährdete Benutzer](active-directory-reporting-security-user-at-risk.md)
### [Riskante Anmeldungen](active-directory-reporting-security-risky-sign-ins.md)
### [Risikoereignisse](active-directory-reporting-risk-events.md)
### [HÄUFIG GESTELLTE FRAGEN](active-directory-reporting-faq.md)
### Aufgaben
#### [Konfigurieren benannter Orte](active-directory-named-locations.md)
#### [Suchen nach Aktivitätsberichten](active-directory-reporting-migration.md)
#### [Verwenden des Azure Active Directory-Power BI-Inhaltspakets](active-directory-reporting-power-bi-content-pack-how-to.md)
#### [Bereinigen von Benutzern mit Risikomarkierung](active-directory-report-security-user-at-risk-remediation.md)
### Verweis
#### [Vermerkdauer](active-directory-reporting-retention.md)
#### [Wartezeiten](active-directory-reporting-latencies-azure-portal.md)
#### [Notifications](active-directory-reporting-notifications.md)
#### [Fehlercodes für Anmeldeaktivitäten](active-directory-reporting-activity-sign-ins-errors.md)
#### [Multi-Factor Authentication](active-directory-reporting-activity-sign-ins-mfa.md)
### Problembehandlung
#### [Fehlende Überwachungsdaten](active-directory-reporting-troubleshoot-missing-audit-data.md)
#### [Fehlende Daten in den Downloads](active-directory-reporting-troubleshoot-missing-data-download.md)
#### [Azure Active Directory-Aktivitätsprotokolle: Fehler in Inhaltspaketen](active-directory-reporting-troubleshoot-content-pack.md)
### [Programmgesteuerter Zugriff](active-directory-reporting-api-getting-started-azure-portal.md)
#### [Überwachungsreferenz](active-directory-reporting-api-audit-reference.md)
#### [Anmeldereferenz](active-directory-reporting-api-sign-in-activity-reference.md)
#### [Voraussetzungen](active-directory-reporting-api-prerequisites-azure-portal.md)
#### [Überwachungsbeispiele](active-directory-reporting-api-audit-samples.md)
#### [Anmeldebeispiele](active-directory-reporting-api-sign-in-activity-samples.md)
#### [Verwenden von Zertifikaten](active-directory-reporting-api-with-certificates.md)

## Verwalten von Kennwörtern
### [Übersicht über Kennwörter](active-directory-passwords-overview.md)
### Benutzerdokumente
#### [Zurücksetzen oder Ändern des Kennworts](active-directory-passwords-update-your-own-password.md)
#### [Bewährte Methoden für Kennwörter](active-directory-secure-passwords.md)
#### [Registrieren für die Self-Service-Kennwortzurücksetzung](active-directory-passwords-reset-register.md)
### [SSPR – So funktioniert‘s](active-directory-passwords-how-it-works.md)
### [SSPR-Bereitstellungshandbuch](active-directory-passwords-best-practices.md)
### [SSPR and Windows 10 (SSPR und Windows 10)](active-directory-passwords-login.md)
### [SSPR-Richtlinien ](active-directory-passwords-policy.md)
### [SSPR-Anpassung](active-directory-passwords-customize.md)
### [SSPR-Datenanforderungen](active-directory-passwords-data.md)
### [SSPR-Berichterstellung](active-directory-passwords-reporting.md)
### IT-Administratoren: Zurücksetzen von Kennwörtern
#### [Azure-Portal](active-directory-users-reset-password-azure-portal.md)
### [Lizenzieren von SSPR](active-directory-passwords-licensing.md)
### [Kennwortrückschreiben](active-directory-passwords-writeback.md)
### [Problembehandlung](active-directory-passwords-troubleshoot.md)
### [HÄUFIG GESTELLTE FRAGEN](active-directory-passwords-faq.md)


## Verwalten von Geräten
### [Einführung](device-management-introduction.md)
### [Verwenden des Azure-Portals](device-management-azure-portal.md)
### [Planen der Azure AD-Einbindung](active-directory-azureadjoin-deployment-aadjoindirect.md)
### [Häufig gestellte Fragen](device-management-faq.md)
### Aufgaben
#### [Einrichten von bei Azure AD registrierten Windows 10-Geräten](device-management-azuread-registered-devices-windows10-setup.md)
#### [Einrichten von in Azure AD eingebundenen Geräten](device-management-azuread-joined-devices-setup.md)
#### [Einrichten von in Azure AD eingebundenen Hybridgeräten](device-management-hybrid-azuread-joined-devices-setup.md)
#### [Lokale Bereitstellung](active-directory-device-registration-on-premises-setup.md)
#### [Azure AD-Einbindung während des Eindrucks beim ersten Ausführen von Windows 10](device-management-azuread-joined-devices-frx.md)
### Problembehandlung
#### [In Azure AD eingebundene Windows 10- und Windows Server 2016-Hybridgeräte](device-management-troubleshoot-hybrid-join-windows-current.md)
#### [In Azure AD eingebundene ältere Windows-Hybridgeräte](device-management-troubleshoot-hybrid-join-windows-legacy.md)

## Verwalten von Apps
### [Übersicht](active-directory-enable-sso-scenario.md)
### [Erste Schritte](active-directory-integrating-applications-getting-started.md)
### [Tutorials für die Integration von SaaS-Apps](active-directory-saas-tutorial-list.md)
### [Cloud-App-Ermittlung](cloudappdiscovery-get-started.md)
#### [Erstellen von Momentaufnahmenberichten](cloudappdiscovery-set-up-snapshots.md)
#### [Konfigurieren des automatischen Uploads von Protokollen für kontinuierliche Berichte](https://docs.microsoft.com/cloud-app-security/discovery-docker)
#### [Use a custom log parser](https://docs.microsoft.com/cloud-app-security/custom-log-parser) (Verwenden eines benutzerdefinierten Protokollparsers)
#### Agent-basierte Ermittlung
##### [Was ist Cloud App Discovery?](active-directory-cloudappdiscovery-whatis.md)
##### [Aktualisieren von Registrierungseinstellungen](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md)
##### [Grundlegendes zu Sicherheit und Datenschutz](active-directory-cloudappdiscovery-security-and-privacy-considerations.md)

### [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von SaaS-Apps](active-directory-saas-app-provisioning.md)
#### [Tutorials für die App-Integration](active-directory-saas-tutorial-list.md)
#### [Automatisieren der Bereitstellung in SCIM-fähigen Apps](active-directory-scim-provisioning.md)
#### [Anpassen von Attributzuordnungen](active-directory-saas-customizing-attribute-mappings.md)
#### [Schreiben von Ausdrücken für Attributzuordnungen in Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md)
#### [Verwenden von Bereichsfiltern](active-directory-saas-scoping-filters.md)
#### [Berichterstellung zur automatischen Benutzerbereitstellung](active-directory-saas-provisioning-reporting.md)
#### [Problembehandlung bei der Benutzerbereitstellung](active-directory-application-provisioning-content-map.md)



### [Remotezugriff auf Apps mit dem Anwendungsproxy](active-directory-application-proxy-get-started.md)
#### Erste Schritte
##### [Aktivieren der App-Proxy](active-directory-application-proxy-enable.md)
##### [Veröffentlichen von Apps](application-proxy-publish-azure-portal.md)
##### [Benutzerdefinierte Domänen](active-directory-application-proxy-custom-domains.md)
#### [Einmaliges Anmelden](application-proxy-sso-overview.md)
##### [SSO mit KCD](active-directory-application-proxy-sso-using-kcd.md)
##### [SSO mit Headern](application-proxy-ping-access.md)
##### [SSO mit Kennworttresoren](application-proxy-sso-azure-portal.md)
#### Konzepte
##### [Connectors](application-proxy-understand-connectors.md)
##### [Sicherheit](application-proxy-security-considerations.md)
##### [Netzwerke](application-proxy-network-topology-considerations.md)
##### [Upgrade von TMG oder UAG](application-proxy-transition-from-uag-tmg.md)

#### Erweiterte Konfigurationen
##### [Veröffentlichen in getrennten Netzwerken](active-directory-application-proxy-connectors-azure-portal.md)
##### [Proxyserver](application-proxy-working-with-proxy-servers.md)
##### [Ansprüche unterstützende Apps](active-directory-application-proxy-claims-aware-apps.md)
##### [Native Client-Apps](active-directory-application-proxy-native-client.md)
##### [Installation im Hintergrund](active-directory-application-proxy-silent-installation.md)
##### [Benutzerdefinierte Startseite](application-proxy-office365-app-launcher.md)
##### [Übersetzen von Inline-Links](application-proxy-link-translation.md)
##### [Platzhalteranwendungen](active-directory-application-proxy-wildcard.md)

#### Veröffentlichen von exemplarische Vorgehensweisen
##### [Remotedesktop](application-proxy-publish-remote-desktop.md)
##### [SharePoint](application-proxy-enable-remote-access-sharepoint.md)
##### [Microsoft Teams](application-proxy-teams.md)
#### [PowerShell](https://docs.microsoft.com/powershell/module/azuread)
#### [Problembehandlung](active-directory-application-proxy-troubleshoot.md)


### Verwalten von Unternehmens-Apps
#### [Zuweisen von Benutzern](active-directory-coreapps-assign-user-azure-portal.md)
#### [Anpassen des Brandings](active-directory-coreapps-change-app-logo-user-azure-portal.md)
#### [Deaktivieren von Benutzeranmeldungen](active-directory-coreapps-disable-app-azure-portal.md)
#### [Entfernen von Benutzern](active-directory-coreapps-remove-assignment-azure-portal.md)
#### [Anzeigen aller meiner Apps](active-directory-coreapps-view-azure-portal.md)
#### [Verwalten der Bereitstellung von Benutzerkonten](active-directory-enterprise-apps-manage-provisioning.md)
#### [Verwalten des einmaligen Anmeldens für Unternehmens-Apps](active-directory-enterprise-apps-manage-sso.md)
#### [Erweiterte Zertifikatsignatur für SAML-Apps](active-directory-enterprise-apps-advance-certificate-options.md)
#### [Ausblenden einer Anwendung auf der Benutzeroberfläche](active-directory-coreapps-hide-third-party-app.md)
### [Configure Sign-In Auto-Acceleration using HRD Policy (Konfigurieren der automatischen Anmeldebeschleunigung per HRD Policy)](active-directory-auto-acceleration-using-hrd.md)
### [Migrieren von AD FS-Apps zu Azure AD](migrate-adfs-apps-to-azure.md)
### [Verwalten des Zugriffs auf Apps](active-directory-managing-access-to-apps.md)
#### [SSO-Zugriff](active-directory-appssoaccess-whatis.md)
#### [Zertifikate für SSO](active-directory-sso-certs.md)
#### [Mandanteneinschränkungen](active-directory-tenant-restrictions.md)
#### [Verwenden von SCIM zum Bereitstellen von Benutzern](active-directory-scim-provisioning.md)

### [Problembehandlung](active-directory-application-troubleshoot-content-map.md)
#### [Anwendungsentwicklung](active-directory-application-dev-troubleshoot-content-map.md)
##### [Konfiguration und Registrierung](active-directory-application-dev-config-content-map.md)
##### [Entwicklung](active-directory-application-dev-development-content-map.md)
#### [Anwendungsverwaltung](active-directory-application-management-troubleshoot-content-map.md)
##### [Konfiguration](active-directory-application-config-content-map.md)
##### [Anmeldung](active-directory-application-sign-in-content-map.md)
##### [Bereitstellung](active-directory-application-provisioning-content-map.md)
###### [Überprüfen, ob ein Benutzer bereitgestellt wurde](application-provisioning-when-will-provisioning-finish-specific-user.md)
###### [Bereitstellung, die sehr lange dauert](application-provisioning-when-will-provisioning-finish.md)
###### [Konfigurieren der Benutzerbereitstellung](application-provisioning-config-how-to.md)
###### [Problem beim Konfigurieren der Bereitstellung](application-provisioning-config-problem.md)
###### [Problem beim Speichern der Administratoranmeldeinformationen](application-provisioning-config-problem-storage-limit.md)
###### [Keine Benutzer werden bereitgestellt.](application-provisioning-config-problem-no-users-provisioned.md)
###### [Falsche Benutzer werden bereitgestellt.](application-provisioning-config-problem-wrong-users-provisioned.md)
##### [Verwalten des Zugriffs](active-directory-application-access-content-map.md)
##### [Zugriffsbereich](active-directory-application-access-panel-content-map.md)
##### [Anwendungsproxy](active-directory-application-proxy-content-map.md)
##### [Bedingter Zugriff](active-directory-application-conditional-access-content-map.md)
### [Entwickeln von Apps](active-directory-applications-guiding-developers-for-lob-applications.md)
### [Dokumentbibliothek](active-directory-apps-index.md)

## Verwalten Ihres Verzeichnisses
### [Azure AD Connect](./connect/active-directory-aadconnect.md)
### Benutzerdefinierte Domänennamen
#### [Schnellstart](add-custom-domain.md)
#### [Hinzufügen benutzerdefinierter Domänennamen](active-directory-domains-manage-azure-portal.md)
### [Verwalten Ihres Verzeichnisses](active-directory-administer.md)
### [Mehrere Verzeichnisse](active-directory-licensing-directory-independence.md)
### [Self-Service-Registrierung](active-directory-self-service-signup.md)
### [Übernehmen eines nicht verwalteten Verzeichnisses](domains-admin-takeover.md)
### [Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
#### [Aktivieren](active-directory-windows-enterprise-state-roaming-enable.md)
#### [Gruppenrichtlinieneinstellungen](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
#### [Windows 10-Einstellungen](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
#### [Häufig gestellte Fragen](active-directory-windows-enterprise-state-roaming-faqs.md)
#### [Problembehandlung](active-directory-windows-enterprise-state-roaming-troubleshooting.md)


### [Integrieren lokaler Identitäten mit Azure AD Connect](./connect/active-directory-aadconnect.md)

## [Verwalten des Zugriffs auf Azure](toc.yml)

## Delegieren des Zugriffs auf Ressourcen
### [Administratorrollen](active-directory-assign-admin-roles-azure-portal.md)
#### [Zuweisen der Administratorrolle zu einem Benutzer](active-directory-users-assign-role-azure-portal.md)
#### [Vergleichen der Berechtigungen für Mitglieder und Gastbenutzer](users-default-permissions.md)
### [Sichern des privilegierten Zugriffs](admin-roles-best-practices.md) 
### [Erstellen von Administratorkonten mit Notfallzugriff](active-directory-admin-manage-emergency-access-accounts.md)
### [Verwaltungseinheiten](active-directory-administrative-units-management.md)
### [Konfigurieren der Tokengültigkeitsdauer](active-directory-configurable-token-lifetimes.md)

## Zugriffsüberprüfungen
### [Übersicht über Zugriffsüberprüfungen](active-directory-azure-ad-controls-access-reviews-overview.md)
### [Abschließen einer Zugriffsüberprüfung](active-directory-azure-ad-controls-complete-access-review.md)
### [Erstellen einer Zugriffsüberprüfung](active-directory-azure-ad-controls-create-access-review.md)
### [Durchführen einer Zugriffsüberprüfung](active-directory-azure-ad-controls-perform-access-review.md)
### [Überprüfen des Zugriffs](active-directory-azure-ad-controls-how-to-review-your-access.md)
### [Gastzugriff mit Zugriffsüberprüfungen](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
### [Verwaltung des Benutzerzugriffs mit Überprüfungen](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
### [Verwalten von Programmen und Steuerelementen](active-directory-azure-ad-controls-manage-programs-controls.md)


## Schützen Ihrer Identitäten
### [Bedingter Zugriff](active-directory-conditional-access-azure-portal.md)
#### [Bedingungen](active-directory-conditional-access-conditions.md)
#### [Ortsbedingung](active-directory-conditional-access-locations.md)
#### [Kontrollen](active-directory-conditional-access-controls.md)
#### [Erste Schritte](active-directory-conditional-access-azure-portal-get-started.md)
#### [bewährten Methoden](active-directory-conditional-access-best-practices.md)
#### [Grundlegendes zu Geräterichtlinien für Office 365-Dienste](active-directory-conditional-access-device-policies.md)
#### [Migrieren von klassischen Richtlinien](active-directory-conditional-access-migration.md)
#### [Was-wäre-wenn-Tool](active-directory-conditional-access-whatif.md)
#### Aufgaben
##### [Migrieren der klassischen MFA-Richtlinie](active-directory-conditional-access-migration-mfa.md)
##### [Einrichten des gerätebasierten bedingten Zugriffs](active-directory-conditional-access-policy-connected-applications.md)
##### [Einrichten des App-basierten bedingten Zugriffs](active-directory-conditional-access-mam.md)
##### [Angeben von Nutzungsbedingungen für Benutzer und Apps](active-directory-tou.md)
##### [Einrichten der VPN-Konnektivität](active-directory-conditional-access-vpn-connectivity-windows10.md)
##### [Einrichten von SharePoint und Exchange Online](active-directory-conditional-access-no-modern-authentication.md)
##### [Problembehandlung](active-directory-conditional-access-device-remediation.md)
#### [Technische Referenz](active-directory-conditional-access-technical-reference.md)
#### [Häufig gestellte Fragen](active-directory-conditional-faqs.md)

### Windows Hello
#### [Authentifizierung ohne Kennwort](active-directory-azureadjoin-passport.md)
#### [Aktivieren von Windows Hello for Business](active-directory-azureadjoin-passport-deployment.md)
### Zertifikatbasierte Authentifizierung
#### [Android](active-directory-certificate-based-authentication-android.md)
#### [iOS](active-directory-certificate-based-authentication-ios.md)
#### [Erste Schritte](active-directory-certificate-based-authentication-get-started.md)

### [Azure AD Identity Protection](active-directory-identityprotection.md)
#### [Aktivieren](active-directory-identityprotection-enable.md)
#### [Erkennen von Sicherheitsrisiken](active-directory-identityprotection-vulnerabilities.md)
#### [Risikoereignisse](active-directory-identity-protection-risk-events.md)
#### [Benachrichtigungen](active-directory-identityprotection-notifications.md)
#### [Anmeldevorgang](active-directory-identityprotection-flows.md)
#### [Simulieren von Risikoereignissen](active-directory-identityprotection-playbook.md)
#### [Entsperren von Benutzern](active-directory-identityprotection-unblock-howto.md)
#### [Häufig gestellte Fragen](active-directory-identity-protection-faqs.md)
#### [Glossar](active-directory-identityprotection-glossary.md)
#### [Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)
### [Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

## Integrieren anderer Dienste in Azure AD
### [Integrieren von LinkedIn in Azure AD](linkedin-integration.md)

## [Bereitstellen von AD DS auf virtuellen Azure-Computern](virtual-networks-windows-server-active-directory-virtual-machines.md)
### [Windows Server Active Directory auf Azure-VMs](active-directory-deploying-ws-ad-guidelines.md)
### [Replikatdomänencontroller in einem virtuellen Azure-Netzwerk](active-directory-install-replica-active-directory-domain-controller.md)
### [Neue Gesamtstruktur in einem virtuellen Azure-Netzwerk](active-directory-new-forest-virtual-machine.md)



## [Bereitstellen von AD FS in Azure](active-directory-aadconnect-azure-adfs.md)
### [Hochverfügbarkeit](active-directory-adfs-in-azure-with-azure-traffic-manager.md)
### [Ändern des Signaturhashalgorithmus](active-directory-federation-sha256-guidance.md)

## [Problembehandlung](active-directory-troubleshooting-support-howto.md)
### [Problembehandlung bei fehlendem oder nicht verfügbarem Active Directory-Element](active-directory-troubleshooting.md)

## Bereitstellen eines Proof of Concept (PoC) für Azure AD
### [PoC-Playbook: Einführen](active-directory-playbook-intro.md)
### [PoC-Playbook: Bestandteile](active-directory-playbook-ingredients.md)
### [PoC-Playbook: Implementierung](active-directory-playbook-implementation.md)
### [PoC-Playbook: Bausteine](active-directory-playbook-building-blocks.md)


# Verweis
## [Codebeispiele](https://azure.microsoft.com/en-us/resources/samples/?service=active-directory)
## [Azure PowerShell-Cmdlets](/powershell/azure/overview)
## [Java-API-Referenz](/java/api)
## [.NET API](/active-directory/adal/microsoft.identitymodel.clients.activedirectory)
## [Dienst- und andere Einschränkungen](active-directory-service-limits-restrictions.md)

# Verwandte Themen
## [Multi-Factor Authentication](/azure/multi-factor-authentication/)
## [Azure AD Connect](./connect/active-directory-aadconnect.md)
## [Azure AD Connect Health](./connect-health/active-directory-aadconnect-health.md)
## [Azure AD für Entwickler](./develop/active-directory-how-to-integrate.md)
## [Azure AD Privileged Identity Management](./privileged-identity-management/active-directory-securing-privileged-access.md)

# angeben
## [Azure-Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory)
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/?category=security-identity)
## [MSDN-Forum](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD)
## [Preise](https://azure.microsoft.com/pricing/details/active-directory/)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=active-directory)
## [Stapelüberlauf](http://stackoverflow.com/questions/tagged/azure-active-directory)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=active-directory)
