---
title: "Tutorial: Web-App mit mehrinstanzfähiger Datenbank und Verwendung von Entity Framework und Sicherheit auf Zeilenebene"
description: "Erfahren Sie, wie Sie eine ASP.NET MVC 5-Web-App mit einem mehrinstanzenfähigen SQL-Datenbank-Back-End und Verwendung von Entity Framework und Sicherheit auf Zeilenebene entwickeln."
metakeywords: azure asp.net mvc entity framework multi tenant row level security rls sql database
services: app-service\web
documentationcenter: .net
manager: jeffreyg
author: tmullaney
ms.assetid: 8fdc47a5-6fc3-4d29-ab6a-33e79f50699f
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 04/25/2016
ms.author: thmullan
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ba1bb3d84b462dfebbb2564569517d7336bf54fd
ms.lasthandoff: 04/10/2017


---
# <a name="tutorial-web-app-with-a-multi-tenant-database-using-entity-framework-and-row-level-security"></a>Tutorial: Web-App mit mehrinstanzfähiger Datenbank und Verwendung von Entity Framework und Sicherheit auf Zeilenebene
In diesem Tutorial wird veranschaulicht, wie Sie eine mehrinstanzenfähige Web-App mit einem Mandantenmodell vom Typ „[Freigegebene Datenbank, Freigegebenes Schema](https://msdn.microsoft.com/library/aa479086.aspx)“ und Verwendung von Entity Framework und [Sicherheit auf Zeilenebene](https://msdn.microsoft.com/library/dn765131.aspx) erstellen. In diesem Modell enthält eine Einzeldatenbank Daten für viele Mandanten, und jeder Zeile einer Tabelle ist eine „Mandanten-ID“ zugeordnet. Die Sicherheit auf Zeilenebene (Row-Level Security, RLS), ein neues Features von Azure SQL-Datenbank, wird verwendet, um für Mandaten den gegenseitigen Zugriff auf die Daten zu verhindern. Dies erfordert nur eine einzelne, kleine Änderung an der Anwendung. Indem die Mandantenzugriffslogik direkt in der Datenbank zentralisiert wird, vereinfacht RLS den Anwendungscode und verringert das Risiko versehentlicher Datenlecks zwischen Mandanten.

Wir beginnen mit der einfachen Contact Manager-Anwendung aus [Erstellen einer ASP.NET-MVP-App mit „auth“ und SQL DB und Bereitstellung für Azure App Service](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Bisher lässt die Anwendung für alle Benutzer (Mandanten) das Anzeigen aller Kontakte zu:

![Contact Manager-Anwendung vor Aktivierung von RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-Before.png)

Mit wenigen Änderungen fügen wir die Unterstützung für die Mehrmandantenfähigkeit hinzu, damit Benutzer nur die Kontakte sehen können, die zu ihnen gehören.

## <a name="step-1-add-an-interceptor-class-in-the-application-to-set-the-sessioncontext"></a>Schritt 1: Hinzufügen einer Interceptor-Klasse in der Anwendung zum Festlegen von SESSION_CONTEXT
Wir müssen eine Anwendungsänderung vornehmen. Da alle Anwendungsbenutzer die Verbindung mit der Datenbank mit der gleichen Verbindungszeichenfolge herstellen (also der gleichen SQL-Anmeldung), kann eine RLS-Richtlinie derzeit nicht ermitteln, nach welchem Benutzer gefiltert werden soll. Dieser Ansatz wird häufig in Webanwendungen verwendet, weil er ein effizientes Verbindungspooling ermöglicht. Aber dies bedeutet, dass wir eine andere Möglichkeit benötigen, um in der Datenbank den aktuellen Anwendungsbenutzer zu identifizieren. Die Lösung besteht darin, dass von der Anwendung ein Schlüssel-Wert-Paar für die aktuelle Benutzer-ID in [SESSION_CONTEXT](https://msdn.microsoft.com/library/mt590806) direkt nach dem Öffnen einer Verbindung festgelegt wird, bevor Abfragen ausgeführt werden. SESSION_CONTEXT ist ein Schlüsselwertspeicher für den Sitzungsbereich, und unsere RLS-Richtlinie verwendet die darin gespeicherte UserId, um den aktuellen Benutzer zu identifizieren.

Wir fügen einen [Interceptor](https://msdn.microsoft.com/data/dn469464.aspx) (genauer gesagt einen [DbConnectionInterceptor](https://msdn.microsoft.com/library/system.data.entity.infrastructure.interception.idbconnectioninterceptor)) hinzu. Dies ist ein neues Feature in Entity Framework (EF) 6 zum automatischen Festlegen der aktuellen Benutzer-ID in SESSION_CONTEXT, indem jeweils eine T-SQL-Anweisung ausgeführt wird, wenn EF eine Verbindung öffnet.

1. Öffnen Sie das ContactManager-Projekt in Visual Studio.
2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner „Models“, und wählen Sie „Hinzufügen“ > „Klasse“.
3. Geben Sie der neuen Klasse den Namen „SessionContextInterceptor.cs“, und klicken Sie auf „Hinzufügen“.
4. Ersetzen Sie den Inhalt von „SessionContextInterceptor.cs“ durch den folgenden Code:

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Web;
using System.Data.Common;
using System.Data.Entity;
using System.Data.Entity.Infrastructure.Interception;
using Microsoft.AspNet.Identity;

namespace ContactManager.Models
{
    public class SessionContextInterceptor : IDbConnectionInterceptor
    {
        public void Opened(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
            // Set SESSION_CONTEXT to current UserId whenever EF opens a connection
            try
            {
                var userId = System.Web.HttpContext.Current.User.Identity.GetUserId();
                if (userId != null)
                {
                    DbCommand cmd = connection.CreateCommand();
                    cmd.CommandText = "EXEC sp_set_session_context @key=N'UserId', @value=@UserId";
                    DbParameter param = cmd.CreateParameter();
                    param.ParameterName = "@UserId";
                    param.Value = userId;
                    cmd.Parameters.Add(param);
                    cmd.ExecuteNonQuery();
                }
            }
            catch (System.NullReferenceException)
            {
                // If no user is logged in, leave SESSION_CONTEXT null (all rows will be filtered)
            }
        }

        public void Opening(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void BeganTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void BeginningTransaction(DbConnection connection, BeginTransactionInterceptionContext interceptionContext)
        {
        }

        public void Closed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Closing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void ConnectionStringGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSet(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionStringSetting(DbConnection connection, DbConnectionPropertyInterceptionContext<string> interceptionContext)
        {
        }

        public void ConnectionTimeoutGetting(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void ConnectionTimeoutGot(DbConnection connection, DbConnectionInterceptionContext<int> interceptionContext)
        {
        }

        public void DataSourceGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DataSourceGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void DatabaseGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void Disposed(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void Disposing(DbConnection connection, DbConnectionInterceptionContext interceptionContext)
        {
        }

        public void EnlistedTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void EnlistingTransaction(DbConnection connection, EnlistTransactionInterceptionContext interceptionContext)
        {
        }

        public void ServerVersionGetting(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void ServerVersionGot(DbConnection connection, DbConnectionInterceptionContext<string> interceptionContext)
        {
        }

        public void StateGetting(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }

        public void StateGot(DbConnection connection, DbConnectionInterceptionContext<System.Data.ConnectionState> interceptionContext)
        {
        }
    }

    public class SessionContextConfiguration : DbConfiguration
    {
        public SessionContextConfiguration()
        {
            AddInterceptor(new SessionContextInterceptor());
        }
    }
}
```

Dies ist die einzige Anwendungsänderung, die erforderlich ist. Nun können Sie die Anwendung erstellen und veröffentlichen.

## <a name="step-2-add-a-userid-column-to-the-database-schema"></a>Schritt 2: Hinzufügen einer UserId-Spalte zum Datenbankschema
Als Nächstes müssen wir der Tabelle „Contacts“ die Spalte „UserId“ hinzufügen, um jeder Zeile einen Benutzer (Mandanten) zuzuordnen. Wir ändern das Schema direkt in der Datenbank, damit wir dieses Feld nicht in unser EF-Datenmodell einbinden müssen.

Stellen Sie die Verbindung mit der Datenbank direkt her, indem Sie entweder SQL Server Management Studio oder Visual Studio verwenden, und führen Sie dann den folgenden T-SQL-Code aus:

```
ALTER TABLE Contacts ADD UserId nvarchar(128)
    DEFAULT CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
```

Der Tabelle „Contacts“ wird die Spalte „UserId“ hinzugefügt. Wir verwenden den Datentyp „nvarchar(128)“, um den Abgleich mit den in der Tabelle „AspNetUsers“ gespeicherten UserIds durchzuführen. Wir erstellen auch eine DEFAULT-Einschränkung, mit der die UserId für neu eingefügte Zeilen auf die UserId festgelegt wird, die derzeit in SESSION_CONTEXT gespeichert ist.

Die Tabelle sieht nun folgendermaßen aus:

![SSMS-Contacts-Tabelle](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-Contacts.png)

Wenn neue Kontakte erstellt werden, werden sie automatische der richtigen UserId zugewiesen. Zu Demonstrationszwecken weisen wir einige dieser vorhandenen Kontakte einem vorhandenen Benutzer zu.

Wenn Sie in der Anwendung bereits einige Benutzer erstellt haben (z. B. über lokale, Google- oder Facebook-Konten), werden diese in der Tabelle „AspNetUsers“ aufgeführt. Im folgenden Screenshot ist bisher nur ein Benutzer zu sehen.

![SSMS-AspNetUsers-Tabelle](./media/web-sites-dotnet-entity-framework-row-level-security/SSMS-AspNetUsers.png)

Kopieren Sie die ID für user1@contoso.com, und fügen Sie unten in die T-SQL-Anweisung ein. Führen Sie diese Anweisung aus, um für drei Kontakte diese UserId zuzuordnen.

```
UPDATE Contacts SET UserId = '19bc9b0d-28dd-4510-bd5e-d6b6d445f511'
WHERE ContactId IN (1, 2, 5)
```

## <a name="step-3-create-a-row-level-security-policy-in-the-database"></a>Schritt 3: Erstellen einer Richtlinie für die Sicherheit auf Zeilenebene in der Datenbank
Der letzte Schritt ist das Erstellen einer Sicherheitsrichtlinie, für die die UserId in SESSION_CONTEXT genutzt wird, um die von den Abfragen zurückgegebenen Ergebnisse automatisch zu filtern.

Führen Sie den folgenden T-SQL-Code aus, während die Verbindung mit der Datenbank besteht:

```
CREATE SCHEMA Security
go

CREATE FUNCTION Security.userAccessPredicate(@UserId nvarchar(128))
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS accessResult
    WHERE @UserId = CAST(SESSION_CONTEXT(N'UserId') AS nvarchar(128))
go

CREATE SECURITY POLICY Security.userSecurityPolicy
    ADD FILTER PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts,
    ADD BLOCK PREDICATE Security.userAccessPredicate(UserId) ON dbo.Contacts
go

```

Dieser Code bewirkt drei Dinge. Erstens wird ein neues Schema als bewährte Methode zum Zentralisieren und Beschränken des Zugriffs auf die RLS-Objekte erstellt. Zweitens wird eine Prädikatfunktion erstellt, die „1“ zurückgibt, wenn die UserId einer Zeile mit der UserId in SESSION_CONTEXT übereinstimmt. Drittens wird eine Sicherheitsrichtlinie erstellt, mit der diese Funktion sowohl als Filter als auch als Blockprädikat der Tabelle „Contacts“ hinzugefügt wird. Mit dem Filterprädikat wird bewirkt, dass Abfragen nur Zeilen zurückgeben, die zum aktuellen Benutzer gehören. Das Blockprädikat dient als Schutz, um zu verhindern, dass die Anwendung versehentlich eine Zeile für den falschen Benutzer einfügt.

Führen Sie nun die Anwendung aus, und melden Sie sich als user1@contoso.com. Diesem Benutzer werden jetzt nur die Kontakte angezeigt, die wir dieser UserId zugewiesen haben:

![Contact Manager-Anwendung vor Aktivierung von RLS](./media/web-sites-dotnet-entity-framework-row-level-security/ContactManagerApp-After.png)

Versuchen Sie, einen neuen Benutzer zu registrieren, um dies zu überprüfen. Für den Benutzer werden keine Kontakte angezeigt, weil ihnen keine Benutzer zugewiesen wurden. Wenn diese Benutzer einen neuen Kontakt erstellen, ist er nur ihnen zugewiesen und nur für sie allein sichtbar.

## <a name="next-steps"></a>Nächste Schritte
Das ist alles! Die einfache Contact Manager-Web-App wurde in eine mehrinstanzenfähige Web-App umgewandelt, in der jeder Benutzer über eine eigene Kontaktliste verfügt. Durch den Einsatz der Sicherheit auf Zeilenebene haben wir die komplexe Aufgabe vermieden, die Mandantenzugriffslogik in unserem Anwendungscode durchzusetzen. Dank dieser Transparenz ist für die Anwendung die Konzentration auf das eigentliche Geschäftsproblem möglich, und außerdem wird das Risiko von versehentlichen Datenlecks verringert, wenn die Codebasis der Anwendung anwächst.

In diesem Tutorial haben wir in Bezug auf die Möglichkeiten von RLS nur an der Oberfläche gekratzt. Beispielsweise ist es möglich, eine anspruchsvollere oder feiner abgestimmte Zugriffslogik zu verwenden und mehr als nur die aktuelle UserId in SESSION_CONTEXT zu speichern. Sie können [RLS auch in Clientbibliotheken mit Tools für elastische Datenbanken integrieren](../sql-database/sql-database-elastic-tools-multi-tenant-row-level-security.md) , um mehrinstanzenfähige Shards auf einer Datenebene mit horizontaler Hochskalierung zu unterstützen.

Außerdem arbeiten wir daran, RLS auch über diese Möglichkeiten hinaus noch besser zu machen. Bitte teilen Sie uns im Kommentarbereich mit, wenn Sie Fragen oder Ideen haben oder Funktionen vorschlagen möchten. Wir schätzen Ihr Feedback!


