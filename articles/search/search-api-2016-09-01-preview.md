---
title: Azure Search Service REST API バージョン 2016-09-01-Preview | Microsoft Docs
description: Azure Search Service REST API バージョン 2016-09-01-Preview には、moreLikeThis 検索などの試験的機能が含まれています。
author: mhko
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: reference
ms.date: 04/18/2018
ms.author: nateko
ms.openlocfilehash: 8eae54c912711a11c015737903b6898b98fd5159
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32181625"
---
# <a name="azure-search-service-rest-api-version-2016-09-01-preview"></a>Azure Search Service REST API: バージョン 2016-09-01-Preview
この記事は、 `api-version=2016-09-01-Preview`のリファレンス ドキュメントです。 このプレビューは、以下の試験的機能を提供することによって、現在一般公開されているバージョン [api-version=2016-09-01](https://docs.microsoft.com/rest/api/searchservice) を拡張するものです。

* 特定のドキュメントに関連する別のドキュメントを検索するための [`moreLikeThis` クエリ パラメーター](search-more-like-this.md)。

これらの実験的な機能を試すには、プレビュー API バージョン `api-version=2016-09-01-Preview` がターゲットになっていることを確認してください。 次の例は、プレビュー API バージョンで more-like-this クエリを実行するように指定しています。

    GET https://[service name].search.windows.net/indexes/[index name]/docs?moreLikeThis=a1&api-version=2016-09-01-Preview

> [!NOTE]
> プレビュー機能は、フィードバックの収集を目的とするテストと実験のために提供されており、変更される可能性があります。 **実稼働アプリケーションではプレビュー API を使用しないことを強くお勧めします。**

Azure Search サービスは複数のバージョンで使用できます。 詳細については、「 [Azure Search サービスのバージョン](https://docs.microsoft.com/azure/search/search-api-versions) 」を参照してください。
