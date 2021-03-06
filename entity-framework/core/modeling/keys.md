---
title: Schlüssel (primär) – EF Core
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 912ffef7-86a0-4cdc-a776-55f907459d20
ms.technology: entity-framework-core
uid: core/modeling/keys
ms.openlocfilehash: f3bf3c7f2a28e065b350fe000a5164406cd5ca08
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
ms.locfileid: "26052570"
---
# <a name="keys-primary"></a>Schlüssel (primär)

Ein Schlüssel dient als der primäre eindeutige Bezeichner für jede Entitätsinstanz. Bei Verwendung eine relationale Datenbank entspricht dem das Konzept einer *Primärschlüssel*. Sie können auch einen eindeutigen Bezeichner, der nicht den Primärschlüssel konfigurieren (finden Sie unter [Alternativschlüssel](alternate-keys.md) für Weitere Informationen).

## <a name="conventions"></a>Konventionen

Gemäß der Konvention, eine Eigenschaft mit dem Namen `Id` oder `<type name>Id` wird als Schlüssel für eine Entität konfiguriert werden.

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string Id { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

<!-- [!code-csharp[Main](samples/core/Modeling/Conventions/Samples/KeyTypeNameId.cs?highlight=3)] -->
``` csharp
class Car
{
    public string CarId { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="data-annotations"></a>Datenanmerkungen

Sie können Datenanmerkungen verwenden, so konfigurieren Sie eine einzelne Eigenschaft, um die Schlüssel einer Entität sein.

<!-- [!code-csharp[Main](samples/core/Modeling/DataAnnotations/Samples/KeySingle.cs?highlight=3,4)] -->
``` csharp
class Car
{
    [Key]
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

## <a name="fluent-api"></a>Fluent-API

Sie können die Fluent-API verwenden, so konfigurieren Sie eine einzelne Eigenschaft, um die Schlüssel einer Entität sein.

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeySingle.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => c.LicensePlate);
    }
}

class Car
{
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```

Die Fluent-API können auch um mehrere Eigenschaften für den Schlüssel einer Entität (als einen zusammengesetzten Schlüssel bezeichnet) werden zu konfigurieren. Zusammengesetzte Schlüssel können nur konfiguriert werden mithilfe der Fluent-API - Konventionen richtet einen zusammengesetzten Schlüssel nie und Datenanmerkungen Konfiguration kann nicht verwendet.

<!-- [!code-csharp[Main](samples/core/Modeling/FluentAPI/Samples/KeyComposite.cs?highlight=7,8)] -->
``` csharp
class MyContext : DbContext
{
    public DbSet<Car> Cars { get; set; }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        modelBuilder.Entity<Car>()
            .HasKey(c => new { c.State, c.LicensePlate });
    }
}

class Car
{
    public string State { get; set; }
    public string LicensePlate { get; set; }

    public string Make { get; set; }
    public string Model { get; set; }
}
```
