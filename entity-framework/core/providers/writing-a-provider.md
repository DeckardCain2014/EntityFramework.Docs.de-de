---
title: Schreiben eines Datenbank-Anbieters - EF Core
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d6d3748a9097b3b8eeee2a8a516c53f3b2afa58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2017
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="139ed-102">Schreiben eines Datenbank-Anbieters</span><span class="sxs-lookup"><span data-stu-id="139ed-102">Writing a Database Provider</span></span>

<span data-ttu-id="139ed-103">Informationen über das Schreiben eines Entity Framework Core-Datenbank-Anbieters finden Sie unter [, damit Sie einen Core EF-Anbieter schreiben möchten](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) von [Arthur Vickers](https://github.com/ajcvickers).</span><span class="sxs-lookup"><span data-stu-id="139ed-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

<span data-ttu-id="139ed-104">Die Codebasis EF Core ist eine open Source und enthält mehrere Datenbankanbieter, die als Verweis verwendet werden können.</span><span class="sxs-lookup"><span data-stu-id="139ed-104">The EF Core code base is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="139ed-105">Sie finden den Quellcode zur https://github.com/aspnet/EntityFramework.</span><span class="sxs-lookup"><span data-stu-id="139ed-105">You can find the source code at https://github.com/aspnet/EntityFramework.</span></span>

## <a name="the-providers-beware-label"></a><span data-ttu-id="139ed-106">Der Anbieter Vorsicht vor Bezeichnung</span><span class="sxs-lookup"><span data-stu-id="139ed-106">The providers-beware label</span></span>

<span data-ttu-id="139ed-107">Nachdem Sie die Arbeit für einen Anbieter beginnen, sehen Sie sich für die [ `providers-beware` ](https://github.com/aspnet/EntityFramework/labels/providers-beware) Bezeichnung auf unserer GitHub-Problemen und Pull-Anforderungen.</span><span class="sxs-lookup"><span data-stu-id="139ed-107">Once you begin work on a provider, watch for the [`providers-beware`](https://github.com/aspnet/EntityFramework/labels/providers-beware) label on our GitHub issues and pull requests.</span></span> <span data-ttu-id="139ed-108">Wir verwenden diese Bezeichnung zum Identifizieren von Änderungen, die Anbieterwriter auswirken können.</span><span class="sxs-lookup"><span data-stu-id="139ed-108">We use this label to identify changes that may impact provider writers.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="139ed-109">Vorgeschlagene Benennung von Service-Drittanbietern</span><span class="sxs-lookup"><span data-stu-id="139ed-109">Suggested naming of third party providers</span></span>

<span data-ttu-id="139ed-110">Es wird empfohlen, die folgenden Benennung für das NuGet-Pakete verwenden.</span><span class="sxs-lookup"><span data-stu-id="139ed-110">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="139ed-111">Dies ist konsistent mit den Namen der Pakete, die vom Team EF Core übermittelt.</span><span class="sxs-lookup"><span data-stu-id="139ed-111">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="139ed-112">Zum Beispiel:</span><span class="sxs-lookup"><span data-stu-id="139ed-112">For example:</span></span>
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`