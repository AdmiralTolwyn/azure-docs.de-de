---
title: Verwenden von Azure Search aus einer .NET-Anwendung | Microsoft Docs
description: Verwenden von Azure Search aus einer .NET-Anwendung
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 93653341-c05f-4cfd-be45-bb877f964fcb
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/06/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 21bd4f05eabfd685cb87b819621fe8d826f209b5


---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Verwenden von Azure Search aus einer .NET-Anwendung
In diesem Artikel erfahren Sie, wie Sie Ihr [Azure Search-.NET-SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)schnell betriebsbereit machen. Dank .NET-SDK erhalten Sie in Ihrer Anwendung die vielfältigen Suchfunktionen von Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Inhalt des Azure Search-SDK
Das SDK enthält die Clientbibliothek `Microsoft.Azure.Search`. Mit dem SDK können Sie Ihre Indizes, Datenquellen und Indexer verwalten, Dokumente hochladen und verwalten und Abfragen ausführen, ohne sich mit den Details von HTTP und JSON befassen zu müssen.

Die Clientbibliothek definiert Klassen wie `Index`, `Field` und `Document` sowie Operationen wie `Indexes.Create` und `Documents.Search` für die Klassen `SearchServiceClient` und `SearchIndexClient`. Diese Klassen sind in die folgenden Namespaces aufgeteilt:

* [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
* [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

Die aktuelle Version des Azure Search .NET-SDK ist nun allgemein verfügbar. Wir freuen uns sehr über Ihr Feedback, das wir in der nächsten Version des Programms zu berücksichtigen versuchen. Sie können uns dieses über die [Seite „Feedback“](https://feedback.azure.com/forums/263029-azure-search/) bereitstellen.

Das .NET-SDK unterstützt Version `2015-02-28` der Azure Search-REST-API, die auf [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx)dokumentiert ist. Diese Version unterstützt jetzt die Lucene-Abfragesyntax und Microsoft-Sprachanalyseprogramme. Neue Funktionen, die *nicht* Teil dieser Version sind, wie die Unterstützung für den Suchparameter `moreLikeThis`, befinden sich noch in der [Vorschau](search-api-2015-02-28-preview.md) und stehen im SDK noch nicht zur Verfügung. Statusaktualisierungen zu diesen Funktionen finden Sie im Abschnitt [Versionsverwaltung für den Suchdienst](https://msdn.microsoft.com/library/azure/dn864560.aspx) .

Folgende Funktionen werden in diesem SDK nicht unterstützt:

* [Verwaltungsvorgänge](https://msdn.microsoft.com/library/azure/dn832684.aspx). Die Verwaltungsvorgänge umfassen die Bereitstellung von Azure Search-Diensten sowie die Verwaltung der API-Schlüssel. Diese werden in einem künftigen Azure Search-.NET-Management-SDK unterstützt.

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Aktualisieren auf die neueste Version des SDK
Wenn Sie bereits eine ältere Version des Azure Search-.NET SDK nutzen und auf die neue allgemein verfügbare Version aktualisieren möchten, finden Sie [in diesem Artikel](search-dotnet-sdk-migration.md) eine entsprechende Anleitung.

## <a name="requirements-for-the-sdk"></a>Anforderungen für das SDK
1. Visual Studio 2013 oder Visual Studio 2015.
2. Ihr eigener Azure Search-Dienst. Zur Verwendung des SDK benötigen Sie den Namen Ihres Dienstes und mindestens einen API-Schlüssel. [Create a service in the portal](search-create-service-portal.md) (+++Erstellen eines Dienstes im Portal).
3. Laden Sie das [NuGet-Paket](http://www.nuget.org/packages/Microsoft.Azure.Search) mit dem Azure Search-.NET-SDK in Visual Studio mithilfe von „NuGet-Pakete verwalten“ herunter. Suchen Sie unter NuGet.org nach dem Paketnamen `Microsoft.Azure.Search` .

Das Azure Search-.NET-SDK unterstützt Anwendungen für .NET Framework 4.5 sowie Windows Store-Apps für Windows 8.1 und Windows Phone 8.1. Silverlight wird nicht unterstützt.

## <a name="core-scenarios"></a>Schlüsselszenarien
In Ihrer Suchanwendung müssen Sie verschiedene Aktionen ausführen. In diesem Zusammenhang werden in diesem Lernprogramm die folgenden Schlüsselszenarien behandelt:

* Erstellen eines Index
* Füllen des Index mit Dokumenten
* Suche nach Dokumenten mit Volltextsuche und Filtern

Im nachfolgenden Beispielcode werden all diese Aktionen vorgeführt. Die Codeausschnitte können Sie gerne in Ihrer eigenen Anwendung übernehmen.

### <a name="overview"></a>Übersicht
Die nachfolgend untersuchte Beispielanwendung erstellt einen neuen Index mit dem Namen „hotels“, füllt diesen mit Dokumenten und führt danach einige Suchabfragen aus. Hier das Hauptprogramm mit dem allgemeinen Ablauf:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Dieses gehen wir nun Schritt für Schritt durch. Zunächst muss das Objekt `SearchServiceClient`erstellt werden. Mit diesem Objekt können Indizes verwaltet werden. Zur Erstellung dieses Objekts müssen Sie Ihren Azure Search-Dienstnamen sowie einen Admin-API-Schlüssel angeben.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here.";

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [!NOTE]
> Wenn Sie einen falschen Schlüssel angeben (z. B. einen Abfrageschlüssel, statt eines Admin-Schlüssels), gibt `SearchServiceClient` beim ersten Aufruf einer Operationsmethode (z. B. eines `Indexes.Create`) eine `CloudException` mit der Fehlermeldung „Forbidden“ (Unzulässig) aus. Überprüfen Sie in diesem Fall unsere API-Schlüssel.
> 
> 

Durch die nächsten Zeilen werden Methoden zum Erstellen eines Index mit dem Namen „hotels“ erstellt, wobei dieser, sofern er bereits vorhanden ist, zuerst gelöscht wird. Auf diese Methoden gehen wir später ein.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Danach muss der Index gefüllt werden. Dazu benötigen wir einen `SearchIndexClient`. Dieses Objekt erhalten Sie entweder, indem Sie es erstellen oder indem Sie für `SearchServiceClient` `Indexes.GetClient` aufrufen. Der Einfachheit halber verwenden wir die zweite Methode.

        ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [!NOTE]
> In einer typischen Suchanwendung erfolgen Verwaltung und Füllung des Index getrennt von Suchabfragen. `Indexes.GetClient` eignet sich zum Auffüllen eines Indexes, da Sie keine weiteren `SearchCredentials` bereitstellen müssen. Vielmehr wird der Admin-Schlüssel, den Sie zum Erstellen des Objekts `SearchServiceClient` verwendet haben, auf das neue `SearchIndexClient`-Objekt übertragen. In dem Teil der Anwendung, in dem Abfragen ausgeführt werden, sollte das Objekt `SearchIndexClient` hingegen direkt erstellt werden, damit anstelle des Admin-Schlüssels ein Abfrageschlüssel übergeben werden kann. Dies entspricht dem Prinzip der geringsten Rechte und trägt dazu bei, die Anwendung sicherer zu machen. Weitere Informationen zu Admin- und Abfrageschlüsseln erhalten Sie [hier](https://msdn.microsoft.com/library/azure/dn798935.aspx).
> 
> 

Nachdem das Objekt `SearchIndexClient`erstellt ist, kann der Index gefüllt werden. Dies erfolgt durch eine weitere Methode, die wir später besprechen.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Zum Schluss führen wir einige Suchabfragen aus und zeigen die Ergebnisse an. Dazu verwenden wir wiederum das Objekt `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Wenn Sie diese Anwendung mit einem gültigen Dienstnamen und API-Schlüssel ausführen, sollte die Ausgabe wie folgt aussehen:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []
    Complete.  Press any key to end application...

Der vollständige Quellcode der Anwendung wird am Ende dieses Artikels bereitgestellt.

Nun sehen wir uns die von `Main`aufgerufenen Methoden näher an.

### <a name="creating-an-index"></a>Erstellen eines Index
Nachdem das Objekt `SearchServiceClient` erstellt ist, löscht `Main` den Index „hotels“, sofern dieser bereits vorhanden ist. Dies erfolgt mit folgender Methode:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Diese Methode verwendet das bereitgestellte Objekt `SearchServiceClient` , um zu überprüfen, ob der Index vorhanden ist, und löscht ihn gegebenenfalls.

> [!NOTE]
> Der Beispielcode in diesem Artikel verwendet der Einfachheit halber die synchronen Methoden des Azure Search-.NET-SDK. Für Ihre eigenen Anwendungen empfehlen wir aus Gründen der Skalierbarkeit und Reaktionsfähigkeit die asynchronen Methoden. In der oben gezeigten Methode können Sie `Exists` und `Delete` zum Beispiel auch durch `ExistsAsync` und `DeleteAsync` ersetzen.
> 
> 

Als Nächstes ruft `Main` die folgende Methode auf, um einen neuen Index mit dem Namen „hotels“ zu erstellen:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Diese Methode erstellt ein neues `Index`-Objekt mit einer Liste von `Field`-Objekten, die das Schema des neuen Index definiert. Jedes Feld weist einen Namen, einen Datentyp und mehrere Attribute auf, die das Suchverhalten des Felds definieren. Neben Feldern können Sie dem Index auch Wertungsprofile, Vorschläge oder CORS-Optionen hinzufügen, die im Beispiel fehlen, um es einfach zu halten. Weitere Informationen zum Indexobjekt und seinen Bestandteilen finden Sie in der SDK-Referenz auf [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) sowie in der [Azure Search-REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### <a name="populating-the-index"></a>Füllen des Index
Im nächsten Schritt in `Main` wird der neu erstellte Index gefüllt. Dies erfolgt mit folgender Methode:

    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                {
                    HotelId = "1058-441",
                    HotelName = "Fancy Stay",
                    BaseRate = 199.0,
                    Category = "Luxury",
                    Tags = new[] { "pool", "view", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                    Rating = 5,
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "666-437",
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "970-501",
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "956-532",
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel()
                {
                    HotelId = "566-518",
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            var batch = IndexBatch.Upload(documents);
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Diese Methode besteht aus vier Teilen. Im ersten Teil wird ein `Hotel` -Objektarray erstellt, das die Eingabedaten für den Upload in den Index bereitstellt. Diese Daten sind der Einfachheit halber fest codiert. In Ihrer eigenen Anwendung stammen die Daten vermutlich aus einer externen Datenquelle, beispielsweise aus einer SQL-­Datenbank.

Im zweiten Teil wird ein `IndexBatch` mit den Dokumenten erstellt. Sie geben den Vorgang an, den Sie auf den Batch zum Zeitpunkt seiner Erstellung anwenden möchten, in diesem Fall durch Aufruf von `IndexBatch.Upload`. Der Batch wird dann durch die Methode `Documents.Index` in den Azure Search-Index hochgeladen.

> [!NOTE]
> In diesem Beispiel werden nur Dokumente hochgeladen. Wenn Sie z.B. Änderungen in vorhandenen Dokumenten zusammenführen oder Dokumente löschen möchten, können Sie zum Erstellen von Batches `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` oder `IndexBatch.Delete` aufrufen. Sie können auch verschiedene Vorgänge in einem einzigen Batch kombinieren, indem Sie `IndexBatch.New` aufrufen. Dieses akzeptiert eine Auflistung von `IndexAction`-Objekten, und jedes dieser Objekte weist Azure Search an, einen bestimmten Vorgang für das Dokument auszuführen. Sie können jede `IndexAction` mit ihrem eigenen Vorgang erstellen, indem Sie die entsprechende Methode aufrufen, z.B. `IndexAction.Merge`, `IndexAction.Upload` usw.
> 
> 

Der dritte Teil dieser Methode ist ein Catch-Block, der einen wichtigen Fehlerfall bei der Indizierung abfängt. Falls der Azure Search-Dienst Dokumente des Batch nicht indizieren kann, löst `Documents.Index` eine `IndexBatchException` aus. Dies kann bei der Indizierung von Dokumenten geschehen, wenn der Dienst stark ausgelastet ist. **Es wird dringend empfohlen, diesen Fall in Ihrem Code explizit zu behandeln.** Zum Beispiel kann die Indizierung der zuvor nicht indizierten Dokumente nach einer Weile wieder aufgenommen werden oder der Vorgang kann, wie im Beispiel gezeigt, nach der Aufzeichnung des Fehlers fortgeführt werden. Je nach Datenkonsistenzanforderungen Ihrer Anwendung sind aber auch andere Lösungen möglich.

Der letzte Teil der Methode fügt eine Verzögerung von zwei Sekunden hinzu. Da die Indizierung in Ihrem Azure Search-Dienst asynchron erfolgt, muss die Beispielanwendung einen Augenblick warten, damit sichergestellt ist, dass die Dokumente für Suchen zur Verfügung stehen. Verzögerungen wie diese sind in der Regel nur in Demos, Tests und Beispielanwendungen erforderlich.

#### <a name="how-the-net-sdk-handles-documents"></a>Behandeln von Dokumenten durch das .NET-SDK
Vielleicht fragen Sie sich, wie das Azure Search-.NET-SDK Instanzen einer benutzerdefinierten Klasse wie `Hotel` in einen Index hochladen kann. Um diese Frage zu beantworten, sehen wir uns die Klasse `Hotel` an:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

Das Erste, was Sie sehen, ist, dass jede öffentliche Eigenschaft von `Hotel` einem Feld in der Indexdefinition entspricht, allerdings mit einem bedeutsamen Unterschied: die Namen der Felder beginnen mit einem Kleinbuchstaben („camel-Schreibweise“), während die Namen der öffentlichen Eigenschaften von `Hotel` mit einem Großbuchstaben („Pascal-Schreibweise“) beginnen. Dies ist ein typisches Szenario für .NET-Anwendungen, die Datenbindungen durchführen, wenn das Zielschema außerhalb der Kontrolle des Anwendungsentwicklers liegt. Anstatt nun die Namenskonventionen von .NET mit Eigenschaftsnamen in camel-Schreibweise zu verletzen, können Sie das SDK mit dem Attribut `[SerializePropertyNamesAsCamelCase]` anweisen, Eigenschaftsnamen automatisch der camel-Schreibweise zuzuordnen.

> [!NOTE]
> Das Azure Search .NET SDK verwendet die [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) -Bibliothek, um die Objekte Ihres benutzerdefinierten Modells nach JSON zu serialisieren und aus JSON zu deserialisieren. Sie können diese Serialisierung bei Bedarf anpassen. Weitere Informationen finden Sie unter [Benutzerdefinierte Serialisierung mit JSON.NET](#JsonDotNet).
> 
> 

Ebenso bemerkenswert an der Klasse `Hotel` sind die Datentypen der öffentlichen Eigenschaften. Die .NET-Typen dieser Eigenschaften stimmen mit den entsprechenden Feldtypen in der Indexdefinition überein. Die Zeichenfolgeeigenschaft `Category` passt zum Beispiel zum Feld `category`, das den Typ `Edm.String` hat. Ähnliche Zuordnungen bestehen auch zwischen `bool?` und `Edm.Boolean`, `DateTimeOffset?`, `Edm.DateTimeOffset` usw. Die jeweiligen Regeln für die Zuordnung eines Typs sind mit der `Documents.Get`-Methode auf [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx) dokumentiert.

Diese Möglichkeit, eigene Klassen als Dokumente zu verwenden, funktioniert in beide Richtungen. Denn ebenso können Sie das SDK, wie im nächsten Abschnitt gezeigt, beim Abrufen von Suchergebnissen anweisen, diese automatisch in einen Typ Ihrer Wahl zu deserialisieren.

> [!NOTE]
> Das Azure Search-.NET-SDK unterstützt durch die Klasse `Document`, einer Schlüssel-/Wertzuordnung von Feldnamen zu Feldwerten, auch dynamisch typisierte Dokumente. Diese Art der Typisierung eignet sich für Szenarien, in denen Sie bei der Entwicklung noch nicht wissen, wie das Indexschema aussehen soll, oder in denen eine Bindung an bestimmte Modellklassen unpassend ist. Alle Methoden des SDK, die Dokumente verarbeiten, verfügen über Überladungen, welche die Klasse `Document` verwenden, wie auch stark typisierte Überladungen, die einen generischen Typparameter verwenden. Nur letztere Art von Überladung wird im Beispielcode dieses Lernprogramms verwendet. Weitere Informationen zur Klasse `Document` erhalten Sie [hier](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).
> 
> 

**Ein wichtiger Hinweis zu Datentypen**

Beim Entwerfen eigener Modellklassen für die Zuordnung zum Azure Search-Index wird empfohlen, Eigenschaften von Werttypen, z. B. `bool` und `int`, als Eigenschaften zu deklarieren, die NULL-Werte zulassen (z. B. `bool?` anstelle von `bool`). Wenn Sie eine Eigenschaft verwenden, die keine NULL-Werte zulässt, müssen Sie **garantieren**, dass keine Dokumente im Index einen NULL-Wert für das entsprechende Feld enthalten. Weder das SDK noch der Azure Search-Dienst helfen Ihnen, dies durchzusetzen.

Dieser Aspekt ist nicht nur hypothetischer Art: Stellen Sie sich ein Szenario vor, bei dem Sie ein neues Feld einem vorhandenen Index vom Typ `Edm.Int32` hinzufügen. Nach dem Aktualisieren der Indexdefinition besitzen alle Dokumente einen NULL-Wert für das neue Feld (da in Azure Search alle Typen NULL-Werte zulassen). Wenn Sie für dieses Feld anschließend eine Modellklasse mit einer `int`-Eigenschaft verwenden, die keine NULL-Werte zulässt, erhalten Sie beim Abrufen von Dokumenten folgendes `JsonSerializationException`-Element:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Aus diesem Grund empfehlen wir als bewährte Methode, in Ihren Modellklassen Typen zu verwenden, die NULL-Werte zulassen.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Benutzerdefinierte Serialisierung mit JSON.NET
Das SDK verwendet JSON.NET zum Serialisieren und Deserialisieren von Dokumenten. Bei Bedarf können Sie die Serialisierung und Deserialisierung anpassen, indem Sie einen eigenen `JsonConverter` oder `IContractResolver` definieren (weitere Informationen finden Sie in der [JSON.NET-Dokumentation](http://www.newtonsoft.com/json/help/html/Introduction.htm)). Dies kann nützlich sein, wenn Sie eine vorhandene Modellklasse aus der Anwendung für die Verwendung mit Azure Search und andere fortgeschrittenere Szenarien anpassen möchten. Bei einer benutzerdefinierten Serialisierung bieten sich zum Beispiel folgende Möglichkeiten:

* Ein- oder Ausschließen bestimmter Eigenschaften der Modellklasse bei der Speicherung als Dokumentfelder
* Zuordnen zwischen Eigenschaftennamen im Code und Feldnamen im Index
* Erstellen benutzerdefinierter Attribute, die sowohl für die Zuordnung von Eigenschaften zu Dokumentfeldern als auch für die Erstellung der entsprechenden Indexdefinition verwendet werden können

Beispiele für die Implementierung der benutzerdefinierten Serialisierung in den Unittests für das Azure Search .NET SDK finden Sie auf GitHub. Ein guter Ausgangspunkt ist [dieser Ordner](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Er enthält Klassen, die in den Tests für die benutzerdefinierte Serialisierung verwendet werden.

### <a name="searching-for-documents-in-the-index"></a>Suchen nach Dokumenten im Index
Im letzten Schritt der Beispielanwendung wird nach Dokumenten im Index gesucht. Dazu wird folgende Methode verwendet:

    private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response.Results)
        {
            Console.WriteLine(result.Document);
        }
    }

Diese Methode erstellt zunächst ein neues `SearchParameters` -Objekt. Damit werden zusätzliche Abfrageoptionen wie Sortierung, Filter, Paging und Facettierung festgelegt. In diesem Beispiel legen wir nur die Eigenschaft `Filter` fest.

Im nächsten Schritt wird die Suchabfrage bereits ausgeführt. Dazu wird die Methode `Documents.Search` verwendet: In diesem Fall übergeben wir den zu verwendenden Suchtext als Zeichenfolge sowie die zuvor erstellten Suchparameter. Außerdem geben wir `Hotel` als Typparameter für `Documents.Search` an, wodurch das SDK angewiesen wird, die Dokumente im Suchergebnis in Objekte des Typs `Hotel` zu deserialisieren.

Schließlich geht diese Methode durch alle Übereinstimmungen im Suchergebnis, wobei sie jedes Dokument auf der Konsole ausgibt.

Wir wollen uns nun näher ansehen, wie diese Methode aufgerufen wird:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Im ersten Aufruf werden alle Dokumente mit den Abfrageausdrücken „fancy“ und „wifi“ gesucht. Im zweiten Aufruf ist der Suchtext auf „*“ gesetzt, d. h., es wird nach allem gesucht. Weitere Informationen zur Syntax von Suchabfrageausdrücken finden Sie [hier](https://msdn.microsoft.com/library/azure/dn798920.aspx).

Der zweite Aufruf verwendet den OData-`$filter`-Ausdruck `category eq 'Luxury'`. Diese Suche gibt damit nur Dokumente zurück, deren `category` -Feld genau der Zeichenfolge „Luxury“ entspricht. Weitere Informationen zur von Azure Search unterstützten OData-Syntax finden Sie [hier](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Nachdem Sie nun wissen, was diese beiden Aufrufe bewirken, wird deren Ausgabe verständlicher:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury    Tags: []

Die erste Suche gibt zwei Dokumente zurück. Das erste enthält im Namen die Zeichenfolge „fancy“, während das zweite im Feld `tags` die Zeichenfolge „wifi“ enthält. Die zweite Suche gibt ebenfalls zwei Dokumente zurück. Dies sind die einzigen Dokumente im Index, deren `category`-Feld auf „Luxury“ gesetzt ist.

Mit diesem Schritt ist das Lernprogramm abgeschlossen. Gerne können Sie sich aber noch weiter mit dem Programm vertraut machen. Im Abschnitt **Nächste Schritte** finden Sie eine Zusammenstellung weiterer Ressourcen zur Einarbeitung in Azure Search.

## <a name="next-steps"></a>Nächste Schritte
* Nutzen Sie die Referenzen für das [.NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) und die [REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) auf MSDN.
* Vertiefen Sie Ihre Kenntnisse mithilfe von [Videos und anderen Beispielen und Lernprogrammen](search-video-demo-tutorial-list.md).
* Erfahren Sie mehr über die Features und Funktionen in dieser Version des Azure Search-SDK: [Übersicht über Azure Search](https://msdn.microsoft.com/library/azure/dn798933.aspx)
* Lesen Sie den Abschnitt [Namenskonventionen](https://msdn.microsoft.com/library/azure/dn857353.aspx) , um sich mit den Namensregeln für Objekte vertraut zu machen.
* Lesen Sie den Abschnitt zu den in Azure Search [unterstützten Datentypen](https://msdn.microsoft.com/library/azure/dn798938.aspx) .

## <a name="sample-application-source-code"></a>Quellcode der Beispielanwendung
Nachfolgend sehen Sie den vollständigen Quellcode der in diesem Artikel besprochenen Beispielanwendung. Wenn Sie das Beispiel erstellen und ausführen möchten, müssen Sie die Platzhalter für den Dienstnamen und den API-Schlüssel in der Datei Program.cs durch Ihre eigenen Werte ersetzen.

Program.cs:

```csharp
using System;
using System.Configuration;
using System.Linq;
using System.Threading;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    class Program
    {
        // This sample shows how to delete, create, upload documents and query an index
        static void Main(string[] args)
        {
            // Put your search service name here. This is the hostname portion of your service URL.
            // For example, if your service URL is https://myservice.search.windows.net, then your
            // service name is myservice.
            string searchServiceName = "myservice";

            string apiKey = "Put your API admin key here.";

            SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

            Console.WriteLine("{0}", "Deleting index...\n");
            DeleteHotelsIndexIfExists(serviceClient);

            Console.WriteLine("{0}", "Creating index...\n");
            CreateHotelsIndex(serviceClient);

            ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

            Console.WriteLine("{0}", "Uploading documents...\n");
            UploadDocuments(indexClient);

            Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
            SearchDocuments(indexClient, searchText: "fancy wifi");

            Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
            SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("hotels"))
            {
                serviceClient.Indexes.Delete("hotels");
            }
        }

        private static void CreateHotelsIndex(SearchServiceClient serviceClient)
        {
            var definition = new Index()
            {
                Name = "hotels",
                Fields = new[]
                {
                    new Field("hotelId", DataType.String)                       { IsKey = true },
                    new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                    new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                    new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                    new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                    new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                    new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                }
            };

            serviceClient.Indexes.Create(definition);
        }

        private static void UploadDocuments(ISearchIndexClient indexClient)
        {
            var documents =
                new Hotel[]
                {
                    new Hotel()
                    {
                        HotelId = "1058-441",
                        HotelName = "Fancy Stay",
                        BaseRate = 199.0,
                        Category = "Luxury",
                        Tags = new[] { "pool", "view", "concierge" },
                        ParkingIncluded = false,
                        LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero),
                        Rating = 5,
                        Location = GeographyPoint.Create(47.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "666-437",
                        HotelName = "Roach Motel",
                        BaseRate = 79.99,
                        Category = "Budget",
                        Tags = new[] { "motel", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                        Rating = 1,
                        Location = GeographyPoint.Create(49.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "970-501",
                        HotelName = "Econo-Stay",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "pool", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(46.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "956-532",
                        HotelName = "Express Rooms",
                        BaseRate = 129.99,
                        Category = "Budget",
                        Tags = new[] { "wifi", "budget" },
                        ParkingIncluded = true,
                        LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                        Rating = 4,
                        Location = GeographyPoint.Create(48.678581, -122.131577)
                    },
                    new Hotel()
                    {
                        HotelId = "566-518",
                        HotelName = "Surprisingly Expensive Suites",
                        BaseRate = 279.99,
                        Category = "Luxury",
                        ParkingIncluded = false
                    }
                };

            try
            {
                var batch = IndexBatch.Upload(documents);
                indexClient.Documents.Index(batch);
            }
            catch (IndexBatchException e)
            {
                // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                // the batch. Depending on your application, you can take compensating actions like delaying and
                // retrying. For this simple demo, we just log the failed document keys and continue.
                Console.WriteLine(
                    "Failed to index some of the documents: {0}",
                    String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
            }

            // Wait a while for indexing to complete.
            Thread.Sleep(2000);
        }

        private static void SearchDocuments(ISearchIndexClient indexClient, string searchText, string filter = null)
        {
            // Execute search based on search text and optional filter
            var sp = new SearchParameters();

            if (!String.IsNullOrEmpty(filter))
            {
                sp.Filter = filter;
            }

            DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
            foreach (SearchResult<Hotel> result in response.Results)
            {
                Console.WriteLine(result.Document);
            }
        }
    }
}
```

Hotel.cs:

```csharp
using System;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;

namespace AzureSearch.SDKHowTo
{
    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }
}
```



<!--HONumber=Nov16_HO3-->


