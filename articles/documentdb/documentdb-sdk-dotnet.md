<properties 
	pageTitle="DocumentDB .NET SDK | Microsoft Azure" 
	description="Wichtige Informationen zum .NET SDK einschließlich Veröffentlichungstermine, Deaktivierungstermine und Änderungen an den einzelnen Versionen des DocumentDB .NET SDK." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="rnagpal"/>

# DocumentDB SDK

> [AZURE.SELECTOR]
- [.NET SDK](documentdb-sdk-dotnet.md)
- [Node.js SDK](documentdb-sdk-node.md)
- [Java SDK](documentdb-sdk-java.md)
- [Python SDK](documentdb-sdk-python.md)

##DocumentDB .NET SDK

<table>
<tr><td>**Download**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)</td></tr>
<tr><td>**Dokumentation**</td><td>[.NET SDK-Referenzdokumentation] (https://msdn.microsoft.com/library/azure/dn948556.aspx)</td></tr>
<tr><td>**Beispiele**</td><td>[.NET Code-Beispiele] (https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)</td></tr>
<tr><td>**Erste Schritte**</td><td>[Erste Schritte mit dem DocumentDB .NET SDK] (documentdb-get-started.md)</td></tr>
<tr><td>**Aktuelles unterstütztes Framework**</td><td>[Microsoft .NET Framework 4.5] (https://www.microsoft.com/download/details.aspx?id=30653)</td></tr>
</table></br>

## Versionsinformationen

### <a name="1.6.3"/>[1\.6.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.3)
  - Ein Fehler in der Nuget-Paketerstellung von .NET SDK wurde behoben (dient zum Verpacken von .NET SDK als Teil einer Azure Cloud Service-Lösung).
  
### <a name="1.6.2"/>[1\.6.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.6.2)
  - Implementierte [partitionierte Sammlungen](documentdb-partition-data.md) und [benutzerdefinierte Leistungsstufen](documentdb-performance-levels.md) 

### <a name="1.5.3"/>[1\.5.3](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.3)
  - **[Behoben]** Die Abfrage des DocumentDB-Endpunkts löst Folgendes aus: 'System.Net.Http.HttpRequestException: Fehler beim Kopieren von Inhalt in einen Datenstrom.

### <a name="1.5.2"/>[1\.5.2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.2)
  - Erweiterte LINQ-Unterstützung, einschließlich neuer Operatoren für Paging, bedingte Ausdrücke und Bereichsvergleiche.
    - Take-Operator, um SELECT TOP-Verhalten in LINQ zu ermöglichen
    - CompareTo-Operator, um Vergleiche von Zeichenfolgenbereichen zu ermöglichen
    - Bedingte (?) und zusammengefügte (??) Operatoren
  - **[Behoben]** ArgumentOutOfRangeException beim Kombinieren der Modellprojektion mit „Where-In“ in LINQ-Abfragen. [Nr. 81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="1.5.1"/>[1\.5.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.1)
 - **[Korrigiert]** Wenn SELECT nicht der letzte Ausdruck ist, nahm der LINQ-Anbieter keine Projektion an und erzeugte SELECT * falsch. [Nr. 58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="1.5.0"/>[1\.5.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.5.0)
 - Upsert implementiert, UpsertXXXAsync-Methoden hinzugefügt
 - Leistungsverbesserungen für alle Anforderungen
 - LINQ-Anbieterunterstützung für bedingte, „Coalesce“- und „CompareTo“-Methoden für Zeichenfolgen
 - **[Korrigiert]** LINQ-Anbieter--> „Contains“-Methode in „List“ implementiert, um für „IEnumerable“ und „Array“ dieselbe SQL zu generieren.
 - **[Korrigiert]** „BackoffRetryUtility“ verwendet die gleiche „HttpRequestMessage“ erneut, anstatt bei einer Wiederholung eine neue zu erstellen.
 - **[Veraltet]** „UriFactory.CreateCollection“ --> „UriFactory.CreateDocumentCollection“ sollte stattdessen verwendet werden.
 
### <a name="1.4.1"/>[1\.4.1](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.1)
 - **[Korrigiert]** Lokalisierungsprobleme bei Verwenden nicht englischer Kulturinformationen wie beispielsweise nl-NL usw. 
 
### <a name="1.4.0"/>[1\.4.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.4.0)
  - ID-basiertes Routing
    - Neues „UriFactory“-Hilfsprogramm zum Unterstützen des Erstellens ID-basierter Ressourcenlinks.
    - Neue Überladungen für „DocumentClient“ zum Aufnehmen des URI.
  - „IsValid()“ und „IsValidDetailed()“ in LINQ für Geodaten hinzugefügt.
  - LINQ-Anbieterunterstützung verbessert.
    - **Math:** Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
    - **String:** Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
    - **Array:** Concat, Contains, Count
    - **IN**-Operator

### <a name="1.3.0"/>[1\.3.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.3.0)
  - Unterstützung für das Ändern der Indizierungsrichtlinie hinzugefügt.
    - Neue „ReplaceDocumentCollectionAsync“-Methode in „DocumentClient“
    - Neue „IndexTransformationProgress“-Eigenschaft in „ResourceResponse“<T> zum Nachverfolgen des prozentualen Fortschritts von Indexrichtlinienänderungen
    - „DocumentCollection.IndexingPolicy“ jetzt änderbar.
  - Unterstützung für die räumliche Indizierung und Abfrage hinzugefügt.
    - Neuer „Microsoft.Azure.Documents.Spatial“-Namespace für die Serialisierung/Deserialisierung räumlicher Typen wie Punkt und Polygon.
    - Neue „SpatialIndex“-Klasse für die Indizierung von in DocumentDB gespeicherten GeoJSON-Daten.
  - **[Korrigiert]** Falsche SQL-Abfrage aus LINQ-Ausdruck generiert [Nr. 38](https://github.com/Azure/azure-documentdb-net/issues/38)

### <a name="1.2.0"/>[1\.2.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.2.0)
- Abhängigkeit von Newtonsoft.Json v5.0.7 
- Änderungen zur Unterstützung von „Order By“
  - LINQ-Anbieterunterstützung für „OrderBy()“ oder „OrderByDescending()“
  - Änderung an „IndexingPolicy“ zur Unterstützung von „Order By“ 
  
		**NB: Possible breaking change** 
  
    	If you have existing code that provisions collections with a custom indexing policy, then your existing code will need to be updated to support the new IndexingPolicy class. If you have no custom indexing policy, then this change does not affect you.

### <a name="1.1.0"/>[1\.1.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.1.0)
- Unterstützung für das Partitionieren von Daten mithilfe der neuen Klassen „HashPartitionResolver“ und „RangePartitionResolver“ und von „IPartitionResolver“
- „DataContract“-Serialisierung
- GUID-Unterstützung in LINQ-Anbieter
- UDF-Unterstützung in LINQ

### <a name="1.0.0"/>[1\.0.0](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/1.0.0)
- Allgemeine Verfügbarkeit (GA) des SDK

> [AZURE.NOTE]
In der GA-Version wurde im Vergleich zur Preview-Version der Name eines NuGet-Pakets geändert. **Microsoft.Azure.Documents.Client** wurde in **Microsoft.Azure.DocumentDB** geändert. <br/>


### <a name="0.9.x-preview"/>[0\.9.x-preview](https://www.nuget.org/packages/Microsoft.Azure.Documents.Client)
- SDK-Vorschauversionen [Veraltet]

## Veröffentlichungs- und Deaktivierungstermine
Wenn Microsoft ein SDK deaktiviert, werden Sie mindestens **12 Monate** vorher benachrichtigt, um einen reibungslosen Übergang zu einer neueren/unterstützten Version zu gewährleisten.

Neue Features, Funktionen und Optimierungen werden nur dem aktuellen SDK hinzugefügt. Daher wird empfohlen, immer so früh wie möglich auf die neueste SDK-Version zu aktualisieren.

Anforderungen von DocumentDB mithilfe eines deaktivierten SDK werden vom Dienst abgelehnt.

> [AZURE.WARNING]
Alle Versionen des Azure DocumentDB-SDK für .NET vor Version **1.0.0** werden am **29. Februar 2016** deaktiviert.
 
<br/>
 
| Version | Herausgabedatum | Deaktivierungstermine 
| ---	  | ---	         | ---
| [1\.6.3](#1.6.3) | 8. April 2016 |--- | [1\.6.2](#1.6.2) | 29. März 2016 |--- | [1\.5.3](#1.5.3) | 19. Februar 2016 |--- | [1\.5.2](#1.5.2) | 14. Dezember 2015 |--- | [1\.5.1](#1.5.1) | 23. November 2015 |--- | [1\.5.0](#1.5.0) | 5. Oktober 2015 |--- | [1\.4.1](#1.4.1) | 25. August 2015 |--- | [1\.4.0](#1.4.0) | 13. August 2015 |--- | [1\.3.0](#1.3.0) | 5. August 2015 |--- | [1\.2.0](#1.2.0) | 6. Juli 2015 |--- | [1\.1.0](#1.1.0) | 30. April 2015 |--- | [1\.0.0](#1.0.0) | 8. April 2015 |--- | [0\.9.3-Vorabversion](#0.9.x-preview) | 12. März 2015 | 29. Februar 2016 | [0\.9.2-Vorabversion](#0.9.x-preview) | Januar 2015 | 29. Februar 2016 | [.9.1-Vorabversion](#0.9.x-preview) | 13. Oktober 2014 | 29. Februar 2016 | [0\.9.0-Vorabversion](#0.9.x-preview) | 21. August 2014 | 29. Februar 2016

## Häufig gestellte Fragen
[AZURE.INCLUDE [documentdb-sdk-faq](../../includes/documentdb-sdk-faq.md)]

## Weitere Informationen

Weitere Informationen zu DocumentDB finden Sie auf der Seite zum Dienst [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/).

<!---HONumber=AcomDC_0413_2016-->