---
author: baanders
description: Azure Digital Twins のインクルード ファイル - インスタンスを管理する方法
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2020
ms.author: baanders
ms.openlocfilehash: 47de00780fb259e7476c16dea9b7c63bc512a211
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2020
ms.locfileid: "94533894"
---
この記事では、[Azure Digital Twins .NET (C#) **SDK**](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true) を使用して、さまざまな管理操作を実行する方法について説明します。 また、その他の言語の SDK を使用して、これらの同じ管理呼び出しを作成することもできます。その他の言語の SDK については、"[*Azure Digital Twins の API および SDK を使用する方法*](../articles/digital-twins/how-to-use-apis-sdks.md)" に関するページで参照してください。

> [!TIP] 
> すべての SDK メソッドに同期バージョンと非同期バージョンがあることに注意してください。 ページング呼び出しの場合、非同期メソッドは `AsyncPageable<T>` を返し、同期バージョンが `Pageable<T>` を返します。

別の管理オプションとして、このトピック領域の Azure Digital Twins [**REST API**](/rest/api/azure-digitaltwins/) を直接呼び出すこともできます。

最後に、Azure Digital Twins **CLI** を使用して、同じ管理操作を実行できます。 CLI の使用に関する詳細については、["*Azure Digital Twins CLI を使用する方法*](../articles/digital-twins/how-to-use-cli.md)" に関するページを参照してください。