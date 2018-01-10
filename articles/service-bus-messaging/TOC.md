# Übersicht
## [Was ist Service Bus-Messaging?](service-bus-messaging-overview.md)
## [Service Bus – Grundlagen](service-bus-fundamentals-hybrid-solutions.md)
## [Service Bus-Architektur](service-bus-architecture.md)
## [Häufig gestellte Fragen](service-bus-faq.md)

# Erste Schritte
## [Erstellen eines Namespace](service-bus-create-namespace-portal.md)
## Verwenden von Warteschlangen
### [.NET](service-bus-dotnet-get-started-with-queues.md)
### [Java](service-bus-java-how-to-use-queues.md)
### [Node.js](service-bus-nodejs-how-to-use-queues.md)
### [PHP](service-bus-php-how-to-use-queues.md)
### [Python](service-bus-python-how-to-use-queues.md)
### [Ruby](service-bus-ruby-how-to-use-queues.md)
### [REST](/rest/api/servicebus/queues)
## Verwenden von Themen und Abonnements
### [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
### [Java](service-bus-java-how-to-use-topics-subscriptions.md)
### [Node.js](service-bus-nodejs-how-to-use-topics-subscriptions.md)
### [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
### [Python](service-bus-python-how-to-use-topics-subscriptions.md)
### [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)
## [Erstellen einer Service Bus-Anwendung mit mehreren Ebenen](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md)

# Anleitung
## Planen und Entwerfen
### [Verwaltete Dienstidentität (Vorschauversion)](service-bus-managed-service-identity.md)
### [Rollenbasierte Zugriffssteuerung (Vorschauversion)](service-bus-role-based-access-control.md)
### [Premium-Messaging](service-bus-premium-messaging.md)
### [Vergleich von Azure-Warteschlangen und Service Bus-Warteschlangen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
### [Optimieren der Leistung](service-bus-performance-improvements.md)
### [Georedundante Notfallwiederherstellung und Georeplikation](service-bus-geo-dr.md)
### [Asynchrones Messaging und Hochverfügbarkeit](service-bus-async-messaging.md)
### [Handhabung von Ausfällen und Notfällen](service-bus-outages-disasters.md)

## Entwickeln
### Behandlung von Nachrichten
#### [Service Bus-Warteschlangen, -Themen und -Abonnements](service-bus-queues-topics-subscriptions.md)
#### [Nachrichten, Nutzlasten und Serialisierung](service-bus-messages-payloads.md)
#### [Nachrichtenübertragungen, Sperren und Abrechnung](message-transfers-locks-settlement.md)
#### [Nachrichtensequenzierung und -zeitstempel](message-sequencing.md)
#### [Nachrichtenablauf (Gültigkeitsdauer)](message-expiration.md)
### [Authentifizierung und Autorisierung](service-bus-authentication-and-authorization.md)
#### [Migrieren von ACS zu SAS](service-bus-migrate-acs-sas.md)
#### [Authentifizierung mit SAS (Shared Access Signatures)](service-bus-sas.md)
### [Themenfilter und -aktionen](topic-filters.md)
### [Partitionierte Warteschlangen und Themen](service-bus-partitioning.md)
### [Nachrichtensitzungen](message-sessions.md)
### AMQP
#### [Übersicht über AMQP](service-bus-amqp-overview.md)
#### [.NET](service-bus-amqp-dotnet.md)
#### [Java](service-bus-amqp-java.md)
#### [Java Message Service und AMQP](service-bus-java-how-to-use-jms-api-amqp.md)
#### [Leitfaden zum AMQP-Protokoll](service-bus-amqp-protocol-guide.md)
#### [Anforderung-Antwort-basierte AMQP-Vorgänge](service-bus-amqp-request-response.md)
### Erweiterte Funktionen
#### [Warteschlangen für unzustellbare Nachrichten](service-bus-dead-letter-queues.md)
#### [Vorabrufen von Nachrichten](service-bus-prefetch.md)
#### [Erkennung doppelt vorhandener Nachrichten](duplicate-detection.md)
#### [Nachrichtenzähler](message-counters.md)
#### [Nachrichtenverzögerung](message-deferral.md)
#### [Durchsuchen von Nachrichten](message-browsing.md)
#### [Verketten von Entitäten mit automatischer Weiterleitung](service-bus-auto-forwarding.md)
#### [Transaktionsverarbeitung](service-bus-transactions.md)
#### [Implementierung von gekoppelten Namespaces](service-bus-paired-namespaces.md)
### [End-to-End-Ablaufverfolgung und Diagnose](service-bus-end-to-end-tracing.md)
## Verwalten
### [Überwachen von Service Bus mit Azure-Überwachung](service-bus-metrics-azure-monitor.md)
### [Service Bus-Verwaltungsbibliotheken](service-bus-management-libraries.md)
### [Diagnoseprotokolle](service-bus-diagnostic-logs.md)
### [Anhalten und Reaktivieren von Messagingentitäten](entity-suspend.md)
### [Verwenden von Azure Resource Manager-Vorlagen](service-bus-resource-manager-overview.md)
#### [Erstellen eines Namespace](service-bus-resource-manager-namespace.md)
#### [Erstellen eines Namespace und einer Warteschlange](service-bus-resource-manager-namespace-queue.md)
#### [Erstellen eines Service Bus-Namespace mit Thema und Abonnement mit einer Azure Resource Manager-Vorlage](service-bus-resource-manager-namespace-topic.md)
#### [Erstellen einer Autorisierungsregel für Namespace und Warteschlange](service-bus-resource-manager-namespace-auth-rule.md)
#### [Erstellen eines Namespace mit Thema, Abonnement und Regel](service-bus-resource-manager-namespace-topic-with-rule.md)
#### 
### [Verwenden von Azure PowerShell zur Bereitstellung von Entitäten](service-bus-manage-with-ps.md)

# Verweis
## .NET
### [Microsoft.ServiceBus.Messaging (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging)
### [Microsoft.Azure.ServiceBus (.NET Standard)](/dotnet/api/microsoft.azure.servicebus)
## [Java](/java/api/overview/azure/servicebus)
## [Azure PowerShell](/powershell/module/azurerm.servicebus)
## [REST](/rest/api/servicebus)
## [Ausnahmen](service-bus-messaging-exceptions.md)
## [Quotas](service-bus-quotas.md)
## [SQLFilter-Syntax](service-bus-messaging-sql-filter.md)
## [SQLRuleAction-Syntax](service-bus-messaging-sql-rule-action.md)

# angeben
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/?category=enterprise-integration)
## [Blog](https://blogs.msdn.microsoft.com/servicebus/)
## [MSDN-Foren](https://social.msdn.microsoft.com/forums/home?forum=servbus)
## [Preise](https://azure.microsoft.com/pricing/details/service-bus/)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [Preisübersicht](service-bus-pricing-billing.md)
## [Beispiele](service-bus-samples.md)
## [ServiceBus360](https://www.servicebus360.com/)
## [Service Bus-Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=service-bus)
## [Stapelüberlauf](http://stackoverflow.com/questions/tagged/azureservicebus)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=service-bus)


