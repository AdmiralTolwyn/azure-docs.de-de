---
title: Mandantenübergreifende Verwaltungsmöglichkeiten
description: Die delegierte Azure-Ressourcenverwaltung ermöglicht eine mandantenübergreifende Verwaltungserfahrung.
ms.date: 11/7/2019
ms.topic: conceptual
ms.openlocfilehash: df6866e93e19d79b34bbbe430c7466e5f6686325
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453605"
---
# <a name="cross-tenant-management-experiences"></a>Mandantenübergreifende Verwaltungsmöglichkeiten

Als Dienstanbieter können Sie mit der [delegierten Azure-Ressourcenverwaltung](../concepts/azure-delegated-resource-management.md) Azure-Ressourcen für mehrere Kunden in Ihrem eigenen Mandanten im [Azure-Portal](https://portal.azure.com) verwalten. Die meisten Aufgaben und Dienste können auf delegierten Azure-Ressourcen über verwaltete Mandanten ausgeführt werden. In diesem Artikel werden einige der erweiterten Szenarien beschrieben, in denen die delegierte Azure-Ressourcenverwaltung effektiv sein kann.

> [!NOTE]
> Die delegierte Azure-Ressourcenverwaltung kann auch [in einem Unternehmen verwendet werden, das über mehrere eigene Mandanten verfügt](enterprise.md), um die mandantenübergreifende Verwaltung zu vereinfachen.

## <a name="understanding-customer-tenants"></a>Grundlegendes zu Kundenmandanten

Ein Azure Active Directory-Mandant (Azure AD) ist eine Darstellung einer Organisation. Es handelt sich um eine dedizierte Instanz von Azure AD, die Organisationen bereitgestellt wird, wenn diese sich für Azure, Microsoft 365 oder andere Dienste registrieren und damit eine Geschäftsbeziehung mit Microsoft eingehen. Jeder Azure AD-Mandant ist eindeutig und von anderen Azure AD-Mandanten getrennt und verfügt über eine eigene Mandanten-ID (eine GUID). Weitere Informationen finden Sie unter [Was ist Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md).

Um Azure-Ressourcen für einen Kunden zu verwalten, müssten sich Dienstanbieter in der Regel mit einem Konto beim Azure-Portal anmelden, das dem Mandanten dieses Kunden zugeordnet ist, wofür ein Administrator im Mandanten des Kunden benötigt wird, um Benutzerkonten für den Dienstanbieter zu erstellen und zu verwalten.

Mit der delegierten Azure-Ressourcenverwaltung gibt der Onboardingprozess Benutzer im Mandanten des Dienstanbieters an, die auf Abonnements, Ressourcengruppen und Ressourcen im Mandanten des Kunden zugreifen und diese verwalten können sollen. Diese Benutzer können sich dann beim Azure-Portal mit ihren eigenen Anmeldeinformationen anmelden. Innerhalb des Azure-Portals können Sie Ressourcen verwalten, die zu allen Kunden gehören, auf die sie Zugriff haben. Hierzu können Sie die Seite [Meine Kunden](../how-to/view-manage-customers.md) im Azure-Portal besuchen oder direkt im Kontext des Abonnements dieses Kunden arbeiten, entweder im Azure-Portal oder mittels APIs.

Die delegierte Azure-Ressourcenverwaltung ermöglicht größere Flexibilität bei der Verwaltung von Ressourcen für mehrere Kunden, ohne sich bei verschiedenen Konten in unterschiedlichen Mandanten anmelden zu müssen. So kann ein Dienstanbieter beispielsweise drei Kunden mit unterschiedlichen Zuständigkeiten und Zugriffsebenen haben, wie hier gezeigt:

![Drei Kundenmandanten, die die Zuständigkeiten des Dienstanbieters anzeigen](../media/azure-delegated-resource-management-customer-tenants.jpg)

Mithilfe der delegierten Azure-Ressourcenverwaltung können sich autorisierte Benutzer beim Mandanten des Dienstanbieters anmelden, um auf diese Ressourcen zuzugreifen, wie hier gezeigt:

![Über einen Dienstanbietermandanten verwaltete Kundenressourcen](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>Unterstützung für APIs und Verwaltungstools

Sie können Verwaltungsaufgaben für delegierte Ressourcen direkt im Portal oder mithilfe von APIs und Verwaltungstools (z. B. Azure-Befehlszeilenschnittstelle und Azure PowerShell) durchführen. Alle vorhandenen APIs können für die Arbeit mit delegierten Ressourcen verwendet werden, solange die Funktionalität für mandantenübergreifende Verwaltung unterstützt wird und der Benutzer über die entsprechenden Berechtigungen verfügt.

Wir stellen auch APIs bereit, um die Aufgaben der delegierten Azure-Ressourcenverwaltung durchzuführen. Weitere Informationen finden Sie im Abschnitt **Referenz**.

## <a name="enhanced-services-and-scenarios"></a>Verbesserte Dienste und Szenarien

Die meisten Aufgaben und Dienste können auf delegierten Ressourcen über verwaltete Mandanten ausgeführt werden. Im Folgenden finden Sie einige wichtige Szenarien, in denen die mandantenübergreifende Verwaltung effektiv sein kann.

[Azure Arc für Server (Vorschauversion):](../../azure-arc/servers/overview.md)

- [Verbinden von Windows Server- oder Linux-Computern außerhalb von Azure](../../azure-arc/servers/quickstart-onboard-portal.md) mit delegierten Abonnements oder Ressourcengruppen in Azure
- Verwalten von verbundenen Computern mithilfe von Azure-Konstrukten, z. B. Azure Policy und Tagging

[Azure Automation](../../automation/index.yml):

- Verwenden von Automation-Konten für den Zugriff auf und die Arbeit mit delegierten Kundenressourcen

[Azure Backup](../../backup/index.yml):

- Sichern und Wiederherstellen von Kundendaten in Kundenmandanten

[Azure Kubernetes Service (AKS)](../../aks/index.yml):

- Verwalten gehosteter Kubernetes-Umgebungen und Bereitstellen und Verwalten von Containeranwendungen innerhalb von Kundenmandanten

[Azure Monitor](../../azure-monitor/index.yml):

- Anzeigen von Warnungen für delegierte Abonnements mit der Möglichkeit, Warnungen in allen Abonnements anzuzeigen
- Anzeigen von Aktivitätsprotokolldetails für delegierte Abonnements
- Log Analytics: Abfragen von Daten aus Remote-Kundenarbeitsbereichen in mehreren Mandanten
- Erstellen von Warnungen in Kundenmandanten, die eine Automatisierung auslösen, wie z. B. Azure Automation-Runbooks oder Azure Functions im Dienstanbietermandanten über Webhooks

[Azure Policy](../../governance/policy/index.yml):

- Compliancemomentaufnahmen zeigen Details von zugewiesenen Richtlinien innerhalb delegierter Abonnements
- Erstellen und Bearbeiten von Richtliniendefinitionen innerhalb eines delegierten Abonnements
- Zuweisen der von Kunden definierten Richtliniendefinitionen innerhalb des delegierten Abonnements
- Kunden werden Richtlinien, die vom Dienstanbieter erstellt wurden, zusammen mit allen Richtlinien angezeigt, die sie selbst erstellt haben.
- [Beheben von „deployIfNotExists“ oder Ändern von Zuweisungen innerhalb des Kundenmandanten](../how-to/deploy-policy-remediation.md)

[Azure Resource Graph](../../governance/resource-graph/index.yml):

- Enthält jetzt die Mandanten-ID in zurückgegebenen Abfrageergebnissen, sodass Sie ermitteln können, ob ein Abonnement zum Mandanten des Kunden oder dem des Dienstanbieters gehört.

[Azure Security Center](../../security-center/index.yml):

- Mandantenübergreifende Sichtbarkeit
  - Überwachen der Compliance mit Sicherheitsrichtlinien und Sicherstellen der Sicherheitsabdeckung für alle Ressourcen der Mandanten
  - Kontinuierliche Überwachung der Einhaltung gesetzlicher Bestimmungen für mehrere Kunden in einer einzigen Ansicht
  - Überwachen, Selektieren und Priorisieren von umsetzbaren Sicherheitsempfehlungen mit Sicherheitsbewertungsberechnung
- Mandantenübergreifende Sicherheitsstatusverwaltung
  - Verwalten von Sicherheitsrichtlinien
  - Ergreifen von Maßnahmen für Ressourcen, die nicht mit umsetzbaren Sicherheitsempfehlungen konform sind
  - Erfassen und Speichern von sicherheitsbezogenen Daten
- Mandantenübergreifend Bedrohungserkennung und entsprechender Schutz davor
  - Erkennen von Bedrohungen über Ressourcen von Mandanten hinweg
  - Anwenden von Advanced Threat Protection-Kontrollen, wie z. B. JIT-VM-Zugriff (Just-in-Time)
  - Verstärkung des Schutzes der Konfiguration von Netzwerksicherheitsgruppen mit adaptiver Netzwerkhärtung
  - Sicherstellung mittels adaptiver Anwendungssteuerung, dass auf Servern nur die Anwendungen und Prozesse ausgeführt werden, die ausgeführt werden sollten
  - Überwachen von Änderungen an wichtigen Dateien und Registrierungseinträgen mittels Überwachung der Dateiintegrität (FIM)

[Azure Sentinel:](../../sentinel/multiple-tenants-service-providers.md)

- Verwalten von Azure Sentinel-Ressourcen in Kundenmandanten

[Azure Service Health](../../service-health/index.yml):

- Überwachen der Integrität von Kundenressourcen mittels Azure Resource Health
- Verfolgen der Integrität der Azure-Dienste, die von ihren Kunden verwendet werden

[Azure Site Recovery](../../site-recovery/index.yml):

- Verwalten von Notfallwiederherstellungsoptionen für virtuelle Azure-Computer in Kundenmandanten (beachten Sie, dass Sie keine „RunAs“-Konten zum Kopieren von VM-Erweiterungen verwenden können)

[Azure Virtual Machines](../../virtual-machines/index.yml):

- Verwenden von VM-Erweiterungen, um nach der Bereitstellung Konfigurations- und Automatisierungsaufgaben auf virtuellen Azure-Computern in Kundenmandanten auszuführen
- Verwenden der Startdiagnose zur Problembehandlung von Azure-VMs in Kundenmandanten
- Zugreifen auf VMs mit der seriellen Konsole in Kundenmandanten
- Beachten Sie, dass Sie Azure Active Directory nicht für die Remoteanmeldung bei einem virtuellen Computer verwenden können, und dass Sie keine VM mit einem Key Vault für Kennwörter, geheime Schlüssel oder Kryptografieschlüssel für die Datenträgerverschlüsselung integrieren können.

[Azure Virtual Network](../../virtual-network/index.yml):

- Bereitstellen und Verwalten virtueller Netzwerke und virtueller Netzwerkschnittstellenkarten (vNICs) innerhalb von Kundenmandanten

Supportanfragen:

- Supportanfragen für delegierte Ressourcen können Sie auf dem Blatt **Hilfe und Support** im Azure-Portal öffnen (indem Sie den für den delegierten Bereich verfügbaren Supportplan auswählen).

## <a name="current-limitations"></a>Aktuelle Einschränkungen
Beachten Sie bei allen Szenarios die folgenden aktuellen Einschränkungen:

- Von Azure Resource Manager verarbeitete Anforderungen können mithilfe der delegierten Azure-Ressourcenverwaltung durchgeführt werden. Die Vorgangs-URIs für diese Anforderungen beginnen mit `https://management.azure.com`. Anforderungen, die von einer Instanz eines Ressourcentyps verarbeitet werden (z. B. Zugriff auf Key Vault-Geheimnisse oder Zugriff auf Speicherdaten) verarbeitet werden, werden nicht von der delegierten Azure-Ressourcenverwaltung unterstützt. Die Vorgangs-URIs für diese Anforderungen beginnen in der Regel mit einer Adresse, die für Ihre Instanz eindeutig ist, z. B. `https://myaccount.blob.core.windows.net` oder `https://mykeyvault.vault.azure.net/`. Letzteres sind in der Regel auch eher Datenvorgänge als Verwaltungsvorgänge. 
- Rollenzuweisungen müssen [integrierte Rollen](../../role-based-access-control/built-in-roles.md) für die rollenbasierte Zugriffssteuerung (RBAC) verwenden. Alle integrierten Rollen werden derzeit mit der delegierten Azure-Ressourcenverwaltung unterstützt, ausgenommen „Besitzer“ und alle integrierten Rollen mit der Berechtigung [DataActions](../../role-based-access-control/role-definitions.md#dataactions). Die Rolle „Benutzerzugriffsadministrator“ wird nur für die eingeschränkte Verwendung beim [Zuweisen von Rollen zu verwalteten Identitäten](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant) unterstützt.  Benutzerdefinierte Rollen und [klassische Abonnementadministratorrollen](../../role-based-access-control/classic-administrators.md) werden nicht unterstützt.
- Derzeit ist es nicht möglich, ein Abonnement (oder eine Ressourcengruppe innerhalb eines Abonnements) für die delegierte Azure-Ressourcenverwaltung zu integrieren, wenn das Abonnement Azure Databricks verwendet. Ähnlich können Sie, wenn ein Abonnement für das Onboarding mit dem **Microsoft.ManagedServices**-Ressourcenanbieter registriert wurde, zu diesem Zeitpunkt auch keinen Databricks-Arbeitsbereich für dieses Abonnement erstellen.
- Sie können zwar Abonnements und Ressourcengruppen mit Ressourcensperren in die delegierte Azure-Ressourcenverwaltung integrieren, diese Sperren verhindern jedoch nicht die Ausführung von Aktionen durch Benutzer im Verwaltungsmandanten. [Ablehnungszuweisungen](../../role-based-access-control/deny-assignments.md), die systemseitig verwaltete Ressourcen schützen – beispielsweise solche, die von verwalteten Azure-Anwendungen oder von Azure Blueprints erstellt wurden (systemseitig zugewiesene Ablehnungszuweisungen) –, verhindern, dass Benutzer im Verwaltungsmandanten Aktionen für diese Ressourcen ausführen. Benutzer im Kundenmandanten können gegenwärtig allerdings keine eigenen Ablehnungszuweisungen (benutzerseitig zugewiesene Ablehnungszuweisungen) erstellen.

## <a name="next-steps"></a>Nächste Schritte

- Onboarding Ihrer Kunden in die delegierte Azure-Ressourcenverwaltung, entweder unter [Verwendung von Azure Resource Manager-Vorlagen](../how-to/onboard-customer.md) oder mittels [Veröffentlichung eines privaten oder öffentlichen Angebots für verwaltete Dienste im Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Anzeigen und Verwalten von Kunden](../how-to/view-manage-customers.md), indem sie im Azure-Portal zu **Meine Kunden** navigieren.
