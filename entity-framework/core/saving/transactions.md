---
title: Transaktionen – EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: d3e6515b-8181-482c-a790-c4a6778748c1
ms.technology: entity-framework-core
uid: core/saving/transactions
ms.openlocfilehash: fe4c0d6ad7ccb2e97dc94fbf2eb26a41e7fbcb19
ms.sourcegitcommit: 7113e8675f26cbb546200824512078bf360225df
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30202496"
---
# <a name="using-transactions"></a>Verwenden von Transaktionen

Mit Transaktionen können mehrere Datenbankvorgänge einzeln verarbeitet werden. Wenn die Transaktion committet wird, werden alle Vorgänge erfolgreich auf die Datenbank angewendet. Falls für die Transaktion ein Rollback ausgeführt wird, wird keiner der Vorgänge für die Datenbank übernommen.

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Transactions/) finden Sie auf GitHub.

## <a name="default-transaction-behavior"></a>Standardtransaktionsverhalten

Wenn der Datenbankanbieter Transaktionen unterstützt, werden standardmäßig alle Änderungen in einem einzigen Aufruf von `SaveChanges()` in einer Transaktion angewendet. Wenn bei einer der Änderungen ein Fehler auftritt, wird ein Rollback ausgeführt, und keine der Änderungen wird für die Datenbank übernommen. `SaveChanges()` wird also entweder vollständig ausgeführt oder überhaupt nicht, wenn ein Fehler auftritt. Die Datenbank bleibt in diesem Fall unverändert.

Bei den meisten Anwendungen ist dieses Standardverhalten ausreichend. Sie sollten Transaktionen nur manuell steuern, wenn die Anforderungen Ihrer Anwendung dies notwendig machen.

## <a name="controlling-transactions"></a>Steuern von Transaktionen

Mit der `DbContext.Database`-API können Sie Transaktionen beginnen, committen und ein Rollback dafür ausführen. Im folgenden Beispiel werden zwei `SaveChanges()`-Vorgänge und eine LINQ-Abfrage dargestellt, die in einer einzelnen Transaktion ausgeführt wird.

Nicht alle Datenbankanbieter unterstützen Transaktionen. In diesem Fall wird ein Fehler ausgelöst oder kein Vorgang gestartet, wenn Transaktions-APIs aufgerufen werden.

<!-- [!code-csharp[Main](samples/core/Saving/Saving/Transactions/ControllingTransaction/Sample.cs?highlight=3,17,18,19)] -->
``` csharp
        using (var context = new BloggingContext())
        {
            using (var transaction = context.Database.BeginTransaction())
            {
                try
                {
                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/dotnet" });
                    context.SaveChanges();

                    context.Blogs.Add(new Blog { Url = "http://blogs.msdn.com/visualstudio" });
                    context.SaveChanges();

                    var blogs = context.Blogs
                        .OrderBy(b => b.Url)
                        .ToList();

                    // Commit transaction if all commands succeed, transaction will auto-rollback
                    // when disposed if either commands fails
                    transaction.Commit();
                }
                catch (Exception)
                {
                    // TODO: Handle failure
                }
            }
        }
```

## <a name="cross-context-transaction-relational-databases-only"></a>Kontextübergreifende Transaktionen (nur relationale Datenbanken)

Sie können Transaktionen auch für mehrere Kontextinstanzen freigeben. Diese Funktion ist nur bei Anbietern von relationalen Datenbanken verfügbar, da `DbTransaction` und `DbConnection` verwendet werden müssen, die charakteristisch für relationale Datenbanken sind.

Um eine Transaktion freigeben zu können, müssen die Kontexte `DbConnection` und `DbTransaction` verwenden.

### <a name="allow-connection-to-be-externally-provided"></a>Zulassen von extern bereitgestellten Verbindungen

Um `DbConnection` freigeben zu können, muss eine Verbindung an einen Kontext übergeben werden können, während dieser erstellt wird.

Die einfachste Möglichkeit, eine externe Bereitstellung von `DbConnection` zuzulassen, ist, den Kontext nicht mehr mit der `DbContext.OnConfiguring`-Methode zu konfigurieren, sondern extern `DbContextOptions` zu erstellen und es an den Kontextkonstruktor zu übergeben.

> [!TIP]  
> `DbContextOptionsBuilder` ist die API, die Sie in `DbContext.OnConfiguring` zum Konfigurieren des Kontexts verwendet haben. Jetzt werden Sie es extern verwenden und `DbContextOptions` erstellen.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Context&highlight=3,4,5)]

Alternativ können Sie weiterhin `DbContext.OnConfiguring` verwenden und eine `DbConnection` akzeptieren, die gespeichert und dann in `DbContext.OnConfiguring` verwendet wird.

``` csharp
public class BloggingContext : DbContext
{
    private DbConnection _connection;

    public BloggingContext(DbConnection connection)
    {
      _connection = connection;
    }

    public DbSet<Blog> Blogs { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer(_connection);
    }
}
```

### <a name="share-connection-and-transaction"></a>Freigeben der Verbindung und der Transaktion

Sie können nun mehrere Kontextinstanzen erstellen, die die gleiche Verbindung verwenden. Anschließend tragen Sie mit der `DbContext.Database.UseTransaction(DbTransaction)`-API beide Kontexte in derselben Transaktion ein.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/SharingTransaction/Sample.cs?name=Transaction&highlight=1,2,3,7,16,23,24,25)]

## <a name="using-external-dbtransactions-relational-databases-only"></a>Verwenden von externen DbTransactions (nur relationale Datenbanken)

Wenn Sie mit verschiedenen Datenzugriffstechnologien auf eine relationale Datenbank zugreifen, sollten Sie eine gemeinsame Transaktion für die Vorgänge einrichten, die von diesen verschiedenen Technologien ausgeführt werden.

Im folgenden Beispiel wird gezeigt, wie ein ADO.NET SqlClient-Vorgang und ein Entity Framework Core-Vorgang in derselben Transaktion ausgeführt werden.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/ExternalDbTransaction/Sample.cs?name=Transaction&highlight=4,10,21,26,27,28)]

## <a name="using-systemtransactions"></a>Verwenden von System.Transactions

> [!NOTE]  
> Dieses Feature ist neu in EF Core 2.1.

Es ist möglich, Ambient-Transaktionen zu verwenden, wenn Sie einen größeren Bereich koordinieren müssen.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/AmbientTransaction/Sample.cs?name=Transaction&highlight=1,24,25,26)]

Sie können auch eine Eintragung in einer expliziten Transaktion vornehmen.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Transactions/CommitableTransaction/Sample.cs?name=Transaction&highlight=1,13,26,27,28)]

### <a name="limitations-of-systemtransactions"></a>Einschränkungen von System.Transactions  

1. In EF Core müssen die Datenbankanbieter die Unterstützung für System.Transactions implementieren. Unterstützung ist unter den ADO.NET-Anbietern für .NET Framework zwar recht üblich, die API wurde jedoch erst vor Kurzem zu .NET Core hinzugefügt. Daher ist die Unterstützung noch nicht weit verbreitet. Wenn ein Anbieter keine Unterstützung für System.Transactions implementiert, ist es möglich, dass Aufrufe dieser APIs vollständig ignoriert werden. SqlClient für .NET Core bietet Unterstützung ab Version 2.1. In SqlClient für .NET Core 2.0 wird eine Ausnahme ausgelöst, wenn Sie dieses Feature ausführen. 

   > [!IMPORTANT]  
   > Daher sollten Sie testen, ob die API ordnungsgemäß mit Ihrem Anbieter funktioniert, bevor Sie sie für die Verwaltung von Transaktionen einsetzen. Sollte die API nicht funktionieren, wenden Sie sich bitte an den Maintainer des Datenbankanbieters. 

2. Ab Version 2.1 enthält die System.Transactions-Implementierung in .NET Core keine Unterstützung für verteilte Transaktionen. Aus diesem Grund können Sie weder mit `TransactionScope` noch mit `CommitableTransaction` Transaktionen in mehreren Ressourcen-Managern koordinieren. 
