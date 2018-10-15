# [Dokumentation für Data Lake Storage Gen1](index.md)
# [Dokumentation für den Wechsel zu Data Lake Storage Gen2 (Vorschauversion)](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction)

# Übersicht
## [Übersicht über Data Lake Storage Gen1](data-lake-store-overview.md)
## [Vergleichen mit Azure Storage](data-lake-store-comparison-with-blob-storage.md)
## [Verarbeiten von Big Data](data-lake-store-data-scenarios.md)
## [Arbeiten mit Open Source-Anwendungen](data-lake-store-compatible-oss-other-applications.md)
## [bewährten Methoden](data-lake-store-best-practices.md)

# Erste Schritte
## [Verwenden des Azure-Portals](data-lake-store-get-started-portal.md)
## [Verwenden von Azure PowerShell](data-lake-store-get-started-powershell.md)
## [Verwenden der Azure-Befehlszeilenschnittstelle](data-lake-store-get-started-cli-2.0.md)

# Anleitung
## Laden und Verschieben von Daten
### [Verwenden von Azure Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Verwenden von Storage Explorer](data-lake-store-in-storage-explorer.md)
### [Verwenden von AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
### [Verwenden von DistCp](data-lake-store-copy-data-wasb-distcp.md)
### [Verwenden von Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
### [Hochladen von Daten aus Offlinequellen](data-lake-store-offline-bulk-data-upload.md)
### [Regionsübergreifendes Migrieren von Data Lake Storage Gen1](data-lake-store-migration-cross-region.md)

## Schützen von Daten
### [Sicherheitsübersicht](data-lake-store-security-overview.md)
### [Zugriffssteuerung](data-lake-store-access-control.md)
### [Sichern von gespeicherten Daten](data-lake-store-secure-data.md)
### [Verschlüsselung](data-lake-store-encryption.md)
### [Integration des virtuellen Netzwerks (Vorschau)](data-lake-store-network-security.md)

## Authentifizieren mit Data Lake Storage Gen1
### [Authentifizierungsoptionen](data-lakes-store-authentication-using-azure-active-directory.md)
### [Authentifizierung von Endbenutzern](data-lake-store-end-user-authenticate-using-active-directory.md)
#### [Verwenden von Java](data-lake-store-end-user-authenticate-java-sdk.md)
#### [Verwenden des .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
#### [Verwenden der REST-API](data-lake-store-end-user-authenticate-rest-api.md)
#### [Verwenden von Python](data-lake-store-end-user-authenticate-python.md)
### [Dienst-zu-Dienst-Authentifizierung](data-lake-store-service-to-service-authenticate-using-active-directory.md)
#### [Verwenden von Java](data-lake-store-service-to-service-authenticate-java.md)
#### [Verwenden des .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
#### [Verwenden der REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
#### [Verwenden von Python](data-lake-store-service-to-service-authenticate-python.md)

## Verwenden von Data Lake Storage Gen1
### Kontoverwaltungsvorgänge
#### [Verwenden des .NET SDK](data-lake-store-get-started-net-sdk.md)
#### [Verwenden der REST-API](data-lake-store-get-started-rest-api.md)
#### [Verwenden von Python](data-lake-store-get-started-python.md)
### Dateisystemvorgänge
#### [Verwenden des .NET SDK](data-lake-store-data-operations-net-sdk.md)
#### [Verwenden des Java SDK](data-lake-store-get-started-java-sdk.md)
#### [Verwenden der REST-API](data-lake-store-data-operations-rest-api.md)
#### [Verwenden von Python](data-lake-store-data-operations-python.md)

## Leistung
### [Übersicht](data-lake-store-performance-tuning-guidance.md)
### [Verwenden von Azure PowerShell](data-lake-store-performance-tuning-powershell.md)
### [Verwenden von Spark in HDInsight](data-lake-store-performance-tuning-spark.md)
### [Verwenden von Hive in HDInsight](data-lake-store-performance-tuning-hive.md)
### [Verwendung von MapReduce in HDInsight](data-lake-store-performance-tuning-mapreduce.md)
### [Verwenden von Storm in HDInsight](data-lake-store-performance-tuning-storm.md)

## Integration in Azure-Dienste
### Mit HDInsight
#### [Verwenden des Azure-Portals](data-lake-store-hdinsight-hadoop-use-portal.md)
#### [Verwenden von Azure PowerShell (Standardspeicher)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
#### [Verwenden von Azure PowerShell (zusätzlicher Speicher)](data-lake-store-hdinsight-hadoop-use-powershell.md)
#### [Verwenden von Azure-Vorlagen](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
### [Zugreifen von VMs in Azure VNET](data-lake-store-connectivity-from-vnets.md)
### [Verwenden mit Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
### [Verwenden mit Azure Event Hubs](data-lake-store-archive-eventhub-capture.md)
### [Verwenden mit Data Factory](../data-factory/load-azure-data-lake-store.md)
### [Verwenden mit Stream Analytics](data-lake-store-stream-analytics.md)
### [Verwenden mit Power BI](data-lake-store-power-bi.md)
### [Verwenden mit Data Catalog](data-lake-store-with-data-catalog.md)
### [Verwendung mit PolyBase in SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md)
### [Verwendung mit SQL Server Integration Services](https://docs.microsoft.com/sql/integration-services/connection-manager/azure-data-lake-store-connection-manager)
### [Weitere Azure-Integrationsoptionen](data-lake-store-integrate-with-other-services.md)

## Verwalten
### [Zugreifen auf Diagnoseprotokolle](data-lake-store-diagnostic-logs.md)
### [Planen für Hochverfügbarkeit](data-lake-store-disaster-recovery-guidance.md)

# Verweis
## [Codebeispiele](https://azure.microsoft.com/resources/samples/?service=data-lake-store)
## [Azure PowerShell](/powershell/module/azurerm.datalakestore)
## [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)
## [Java](/java/api/com.microsoft.azure.datalake.store)
## [Node.js](https://www.npmjs.com/package/azure-arm-datalake-store)
## [Python (Kontoverwaltung)](https://docs.microsoft.com/python/api/azure.mgmt.datalake.store?view=azure-python)
## [Python (Dateisystemverwaltung)](http://azure-datalake-store.readthedocs.io/en/latest)
## [REST](/rest/api/datalakestore)
## [Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/dls)

# Ressourcen
## [Azure-Roadmap](https://azure.microsoft.com/roadmap/)
## [Data Lake Store-Blog](https://blogs.msdn.microsoft.com/azuredatalake/)
## [Feedback über UserVoice](https://feedback.azure.com/forums/327234-data-lake)
## [MSDN-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDataLake)
## [Preise](https://azure.microsoft.com/pricing/details/data-lake-store/)
## [Preisrechner](https://azure.microsoft.com/pricing/calculator/)
## [Dienstupdates](https://azure.microsoft.com/updates/?product=data-lake-store)
## [Stack Overflow-Forum](http://stackoverflow.com/questions/tagged/azure-data-lake)
## [Videos](https://azure.microsoft.com/documentation/videos/index/?services=data-lake-store)
