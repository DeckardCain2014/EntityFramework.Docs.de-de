---
title: 'Grundlegender Speichervorgang: EF Core'
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 850d842e-3fad-4ef2-be17-053768e97b9e
ms.technology: entity-framework-core
uid: core/saving/basic
ms.openlocfilehash: deead323301dc4a0ee0748b4536ddff4596b99e6
ms.sourcegitcommit: 4997314356118d0d97b04ad82e433e49bb9420a2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31006662"
---
# <a name="basic-save"></a>Grundlegender Speichervorgang

Erfahren Sie, wie Sie Daten mithilfe Ihrer Kontext- und Entitätsklassen hinzufügen, ändern und entfernen können.

> [!TIP]  
> Das in diesem Artikel verwendete [Beispiel](https://github.com/aspnet/EntityFramework.Docs/tree/master/samples/core/Saving/Saving/Basics/) finden Sie auf GitHub.

## <a name="adding-data"></a>Hinzufügen von Daten

Verwenden Sie die Methode *DbSet.Add*, um Ihren Entitätsklassen neue Instanzen hinzuzufügen. Die Daten werden in die Datenbank eingefügt, wenn Sie *SaveChanges* aufrufen.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Add)]

> [!TIP]  
> Die Methoden Add, Attach und Update wirken sich auf den vollständigen Graph an Entitäten aus, die an sie übergeben werden, wie im Abschnitt [Relevante Daten](related-data.md) beschrieben. Die Eigenschaft EntityEntry.State kann alternativ zum Festlegen des Zustands einer einzelnen Entität verwendet werden. Beispielsweise `context.Entry(blog).State = EntityState.Modified`.

## <a name="updating-data"></a>Aktualisieren von Daten

EF erkennt automatisch Änderungen an einer vorhandenen Entität, die nach dem Kontext nachverfolgt wird. Dazu gehören Entitäten, die Sie aus der Datenbank laden oder abfragen, und Entitäten, die vorher der Datenbank hinzugefügt und darin gespeichert wurden.

Ändern Sie einfach die Werte der Eigenschaften, und rufen Sie dann *SaveChanges* auf.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Update)]

## <a name="deleting-data"></a>Löschen von Daten

Verwenden Sie die Methode *DbSet.Remove*, um Instanzen Ihrer Entitätsklassen zu löschen.

Wenn die Entität bereits in der Datenbank vorhanden ist, wird sie während des Aufrufs von *SaveChanges* gelöscht. Wenn die Entität noch nicht in der Datenbank gespeichert wurde (d.h. sie wird als „hinzugefügt“ nachverfolgt), wird sie aus dem Kontext entfernt und nicht mehr eingefügt, wenn *SaveChanges* aufgerufen wird.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#Remove)]

## <a name="multiple-operations-in-a-single-savechanges"></a>Mehrere Vorgänge in einem Aufruf von SaveChanges

Sie können Hinzufügungs-, Update- und Entfernungsvorgänge in einem einzelnen Aufruf von *SaveChanges* kombinieren.

> [!NOTE]  
> Für die meisten Datenbankanbieter ist *SaveChanges* transaktional. Das bedeutet, dass alle Vorgänge entweder erfolgreich oder gar nicht abgeschlossen werden und niemals nur teilweise angewendet werden.

[!code-csharp[Main](../../../samples/core/Saving/Saving/Basics/Sample.cs#MultipleOperations)]
