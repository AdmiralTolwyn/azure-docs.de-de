---
title: C++-Tutorial für Azure Cosmos DB | Microsoft-Dokumentation
description: C++-Tutorial, in dem Sie eine C++-Datenbank und eine Konsolenanwendung mit einem von Azure Cosmos DB unterstützten SDK für C++ erstellen. Azure Cosmos DB ist ein weltweit verfügbarer Datenbankdienst.
services: cosmos-db
author: SnehaGunda
manager: kfile
editor: ''
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: cpp
ms.topic: tutorial
ms.date: 06/05/2018
ms.author: sngun
ms.openlocfilehash: 0e142eaf4182331e0a5803c54d2cc1284e21b221
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807174"
---
# <a name="azure-cosmos-db-c-console-application-tutorial-for-the-sql-api"></a>Azure Cosmos DB: Tutorial zur C++-Konsolenanwendung für die SQL-API
> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Node.js für MongoDB](mongodb-samples.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [C++](sql-api-cpp-get-started.md)
>  
> 

Willkommen beim C++-Tutorial für das SDK für C++, das von der SQL-API von Azure Cosmos DB unterstützt wird! Im Rahmen dieses Tutorials erstellen Sie eine Konsolenanwendung, mit der Azure Cosmos DB-Ressourcen (einschließlich einer C++-Datenbank) erstellt und abgefragt werden können.

In diesem Schnellstart werden folgende Themen behandelt:

* Erstellen eines Azure Cosmos DB-Kontos und Herstellen der Verbindung
* Einrichten der Anwendung
* Erstellen einer C++-Azure Cosmos DB-Datenbank
* Erstellen einer Sammlung
* Erstellen von JSON-Dokumenten
* Abfragen der Sammlung
* Ersetzen eines Dokuments
* Löschen eines Dokuments
* Löschen der C++-Azure Cosmos DB-Datenbank

Sie haben nicht genügend Zeit? Keine Sorge! Die vollständige Lösung ist auf [GitHub](https://github.com/stalker314314/DocumentDBCpp)verfügbar. In [Abrufen der vollständigen Lösung](#GetSolution) finden Sie eine Kurzanleitung.

Lassen Sie uns anfangen.

## <a name="prerequisites-for-the-c-tutorial"></a>Voraussetzungen für das C++-Tutorial
Stellen Sie sicher, dass Sie über die folgenden Ressourcen verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) mit installierten C++-Sprachkomponenten. Falls Sie Visual Studio 2017 noch nicht installiert haben, können Sie die **kostenlose** [Visual Studio 2017 Community-Edition](https://www.visualstudio.com/downloads/) herunterladen und verwenden. Aktivieren Sie beim Setup von Visual Studio die Option **Azure-Entwicklung**.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Schritt 1: Erstellen eines Azure Cosmos DB-Kontos
Wir erstellen nun ein Azure Cosmos DB-Konto. Wenn Sie bereits über ein Konto verfügen, das Sie verwenden möchten, können Sie diesen Schritt überspringen und mit [Einrichten Ihrer C++-Anwendung](#SetupC++) fortfahren.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupC++"></a>Schritt 2: Einrichten Ihrer C++-Anwendung
1. Klicken Sie in Visual Studio im Menü **Datei** auf **Neu** und anschließend auf **Projekt**. 
2. Erweitern Sie im Fenster **Neues Projekt** im Bereich **Installiert** den Knoten **Visual C++**, klicken Sie auf **Win32**, und klicken Sie anschließend auf **Win32-Konsolenanwendung**. Nennen Sie das Projekt „hellodocumentdb“, und klicken Sie dann auf **OK**. 
   
    ![Screenshot des Assistenten „Neues Projekt“](media/sql-api-cpp-get-started/hello.png)
3. Wenn der Win32-Anwendungs-Assistent gestartet wird, klicken Sie auf **Fertig stellen**.
4. Öffnen Sie nach der Projekterstellung den NuGet-Paket-Manager. Klicken Sie hierzu im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **hellodocumentdb**, und klicken Sie anschließend auf **NuGet-Pakete verwalten**. 
   
    ![Screenshot mit „NuGet-Pakete verwalten“ im Projektmenü](media/sql-api-cpp-get-started/nuget.png)
5. Klicken Sie auf der Registerkarte **NuGet: hellodocumentdb** auf **Durchsuchen**, und suchen Sie nach *documentdbcpp*. Wählen Sie das Ergebnis „DocumentDbCpp“ aus, wie im folgenden Screenshot dargestellt:   
   
    ![Screenshot mit hervorgehobenem DocumentDbCpp-Paket](media/sql-api-cpp-get-started/cpp.png)
   
    Dieses Paket installiert Verweise auf das C++ REST SDK (eine Abhängigkeit für das DocumentDbCpp-Paket). Sobald die Pakete dem Projekt hinzugefügt wurden, können Sie mit dem Schreiben von Code beginnen.   

## <a id="Config"></a>Schritt 3: Kopieren von Verbindungsdetails aus dem Azure-Portal für Ihre Azure Cosmos DB-Datenbank
Rufen Sie das [Azure-Portal](https://portal.azure.com) auf, und navigieren Sie zu dem Azure Cosmos DB-Konto, das Sie erstellt haben. Sie benötigen den URI und den Primärschlüssel aus dem Azure-Portal, um im nächsten Schritt über den C++-Codeausschnitt eine Verbindung herstellen zu können. 

![Azure Cosmos DB-URI und -Schlüssel im Azure-Portal](media/sql-api-cpp-get-started/nosql-tutorial-keys.png)

## <a id="Connect"></a>Schritt 4: Herstellen einer Verbindung mit einem Azure Cosmos DB-Konto
1. Fügen Sie Ihrem Quellcode nach `#include "stdafx.h"` die folgenden Header und Namespaces hinzu.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Fügen Sie als Nächstes Ihrer main-Funktion den folgenden Code hinzu, und ersetzen Sie die Kontokonfiguration und den Primärschlüssel durch Ihre Azure Cosmos DB-Einstellungen aus Schritt 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Nachdem Sie nun über den Code zum Initialisieren des Clients verfügen, beschäftigen wir uns im nächsten Schritt mit der Verwendung von Azure Cosmos DB-Ressourcen.

## <a id="CreateDBColl"></a>Schritt 5: Erstellen einer C++-Datenbank und -Sammlung
Bevor Sie diesen Schritt ausführen, wird für Leser, die noch nicht mit Azure Cosmos DB vertraut sind, auf die Interaktion zwischen Datenbank, Sammlung und Dokumenten eingegangen. Eine [Datenbank](sql-api-resources.md#databases) ist ein logischer Container mit auf Sammlungen aufgeteiltem Dokumentspeicher. Eine [Sammlung](sql-api-resources.md#collections) ist ein Container mit JSON-Dokumenten und der dazugehörigen JavaScript-Anwendungslogik. Weitere Informationen zum hierarchischen Ressourcenmodell und zu den Konzepten von Azure Cosmos DB finden Sie unter [Hierarchisches Ressourcenmodell und Konzepte von Azure Cosmos DB](sql-api-resources.md).

Fügen Sie am Ende der main-Funktion den folgenden Code hinzu, um eine Datenbank und eine entsprechende Sammlung zu erstellen. Dadurch werden unter Verwendung der im vorherigen Schritt deklarierten Clientkonfiguration eine Datenbank namens „FamilyRegistry“ und eine Sammlung namens „FamilyCollection“ erstellt.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a id="CreateDoc"></a>Schritt 6: Erstellen eines Dokuments
[Dokumente](sql-api-resources.md#documents) sind benutzerdefinierte (beliebige) JSON-Inhalte. Sie können nun ein Dokument in Azure Cosmos DB einfügen. Zum Erstellen eines Dokuments können Sie den folgenden Code kopieren und am Ende der main-Funktion hinzufügen. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Dieser Code erstellt also eine Azure Cosmos DB-Datenbank, eine Sammlung und Dokumente, die Sie im Azure-Portal über den Daten-Explorer abfragen können. 

![C++-Tutorial – Diagramm zur Veranschaulichung der hierarchischen Beziehung zwischen dem Konto, der Datenbank, der Sammlung und den Dokumenten](media/sql-api-cpp-get-started/docs.png)

## <a id="QueryDB"></a>Schritt 7: Abfragen von Azure Cosmos DB-Ressourcen
Azure Cosmos DB unterstützt umfassende [Abfragen](sql-api-sql-query.md) der in jeder Sammlung gespeicherten JSON-Dokumente. Der folgende Beispielcode zeigt eine Abfrage mit SQL-Syntax, die Sie für die im vorherigen Schritt erstellten Dokumente ausführen können.

Die Funktion akzeptiert als Argumente den eindeutigen Bezeichner oder die Ressourcen-ID für die Datenbank und die Sammlung (zusammen mit dem Dokumentclient). Fügen Sie diesen Code vor der main-Funktion hinzu.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Replace"></a>Schritt 8: Ersetzen eines Dokuments
Azure Cosmos DB unterstützt das Ersetzen von JSON-Dokumenten, wie im folgenden Code gezeigt. Fügen Sie diesen Code nach der executesimplequery-Funktion hinzu.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a id="Delete"></a>Schritt 9: Löschen eines Dokuments
Azure Cosmos DB unterstützt das Löschen von JSON-Dokumenten. Kopieren Sie hierzu den folgenden Code, und fügen Sie ihn nach der replacedocument-Funktion ein. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="DeleteDB"></a>Schritt 10: Löschen einer Datenbank
Wenn Sie die erstellte Datenbank löschen, werden auch alle untergeordneten Ressourcen (Sammlungen, Dokumente usw.) gelöscht.

Kopieren Sie den folgenden Codeausschnitt (cleanup-Funktion), und fügen Sie ihn nach der deletedocument-Funktion ein, um die Datenbank und alle untergeordneten Ressourcen zu entfernen.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a id="Run"></a>Schritt 11: Ausführen der gesamten C++-Anwendung
Sie haben bereits Code zum Erstellen, Abfragen, Ändern und Löschen verschiedener Azure Cosmos DB-Ressourcen hinzugefügt.  Nun fügen Sie der main-Funktion in „hellodocumentdb.cpp“ noch Aufrufe für diese verschiedenen Funktionen sowie einige Diagnosemeldungen hinzu.

Ersetzen Sie hierzu die main-Funktion Ihrer Anwendung durch den folgenden Code. Dadurch werden der Kontokonfigurations-URI und der Primärschlüssel überschrieben, die Sie in Schritt 3 in den Code kopiert haben. Speichern Sie daher entweder die entsprechende Zeile, oder kopieren Sie die Werte erneut aus dem Portal. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Der Code sollte sich nun erstellen und ausführen lassen. Drücken Sie hierzu in Visual Studio die F5-TASTE, oder suchen Sie im Terminalfenster nach der Anwendung, und führen Sie die ausführbare Datei aus. 

Die Ausgabe der GetStarted-Anwendung sollte angezeigt werden. Diese sollte wie im folgenden Screenshot aussehen:

![Ausgabe der Azure Cosmos DB-C++-Anwendung](media/sql-api-cpp-get-started/console.png)

Glückwunsch! Sie haben das C++-Tutorial abgeschlossen und Ihre erste Azure Cosmos DB-Konsolenanwendung erstellt.

## <a id="GetSolution"></a>Herunterladen der vollständigen Projektmappe für das C++-Tutorial
Wenn Sie die GetStarted-Lösung mit sämtlichen Beispielen aus diesem Artikel erstellen möchten, benötigen Sie Folgendes:

* [Azure Cosmos DB-Konto][create-account].
* [GetStarted-Projektmappe](https://github.com/stalker314314/DocumentDBCpp) (erhältlich auf GitHub)

## <a name="next-steps"></a>Nächste Schritte
* Informieren Sie sich über das [Überwachen eines Azure Cosmos DB-Kontos](monitor-accounts.md).
* Führen Sie im [Query Playground](https://www.documentdb.com/sql/demo) Abfragen für ein Beispieldataset durch.
* Weitere Informationen zum Programmiermodell finden Sie auf der [Azure Cosmos DB-Dokumentationsseite](https://azure.microsoft.com/documentation/services/cosmos-db/) im Abschnitt „Develop“ (Entwickeln).

[create-account]: create-sql-api-dotnet.md#create-account


