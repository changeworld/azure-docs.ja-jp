---
title: '.NET チュートリアル: 検索統合のハイライト'
titleSuffix: Azure Cognitive Search
description: このチート シートでは、検索が有効な Web サイトで使用される .NET SDK 検索統合クエリについて説明します。
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 04/23/2021
ms.custom: devx-track-csharp
ms.devlang: dotnet
ms.openlocfilehash: d548822bb7140c25884a44a45725ff09f16ac0e5
ms.sourcegitcommit: 42ac9d148cc3e9a1c0d771bc5eea632d8c70b92a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/13/2021
ms.locfileid: "109848125"
---
# <a name="4---net-search-integration-cheat-sheet"></a>4 - .NET 検索統合のチート シート

前のレッスンでは、静的 Web アプリに検索を追加しました。 このレッスンでは、統合を確立するための基本的な手順について説明します。 検索を Web アプリに統合する方法に関するチート シートをお探しの場合に、この記事では知っておく必要があることについて説明します。

アプリケーションは利用可能です。 
* [サンプル](https://github.com/azure-samples/azure-search-dotnet-samples/tree/master/search-website)
* [デモ Web サイト - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="azure-sdk-azuresearchdocuments"></a>Azure SDK Azure.Search.Documents

関数アプリでは、Azure SDK for Cognitive Search を使用します。

* NuGet: [Azure.Search.Documents](https://www.nuget.org/packages/Azure.Search.Documents/)
* リファレンス ドキュメント: [クライアント ライブラリ](/dotnet/api/overview/azure/search)

関数アプリは、リソース名、リソース キー、およびインデックス名を使用して、SDK を通じてクラウドベースの Cognitive Search API に対して認証を行います。 シークレットは静的 Web アプリの設定に格納されており、環境変数として関数に取り込まれます。 

## <a name="configure-secrets-in-a-localsettingsjson-file"></a>local.settings.json ファイルでシークレットを構成する

1. `./api/` に `local.settings.json` という名前の新しいファイルを作成し、次の JSON オブジェクトをこのファイルにコピーします。

    ```json
    {
      "IsEncrypted": false,
      "Values": {
        "AzureWebJobsStorage": "",
        "FUNCTIONS_WORKER_RUNTIME": "dotnet",
        "SearchApiKey": "YOUR_SEARCH_QUERY_KEY",
        "SearchServiceName": "YOUR_SEARCH_RESOURCE_NAME",
        "SearchIndexName": "good-books"
      }
    }
    ```

1. 以下を独自の Search リソースの値に変更します。 
    * YOUR_SEARCH_RESOURCE_NAME
    * YOUR_SEARCH_QUERY_KEY

## <a name="azure-function-search-the-catalog"></a>Azure 関数: カタログを検索する

`Search` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Search.cs) は検索語句を受け取り、検索インデックス内のドキュメントを検索して、一致項目の一覧を返します。 

Azure 関数は、検索の構成情報を取り込んで、クエリを実行します。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Search.cs" highlight="22-24, 55" :::

## <a name="client-search-from-the-catalog"></a>クライアント: カタログから検索する

次のコードを使用して、React クライアントで Azure 関数を呼び出します。 

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure 関数: カタログからの検索候補

`Suggest` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Suggest.cs) は、ユーザーが入力している間は検索語句を受け取り、検索インデックス内のドキュメントに含まれる書籍のタイトルや著者名などの検索候補を提示して、一致する小さな一覧を返します。 

検索 suggester、`sg` は、一括アップロードの際に使用される[スキーマ ファイル](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/bulk-insert/BookSearchIndex.cs)に定義されています。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Suggest.cs" highlight="21-23, 50-52" :::

## <a name="client-suggestions-from-the-catalog"></a>クライアント: カタログからの検索候補

Suggest 関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\components\SearchBar\SearchBar.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure 関数: 特定のドキュメントを取得する 

`Lookup` [API](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/search-website/api/Lookup.cs) は ID を受け取り、検索インデックスからドキュメント オブジェクトを返します。 

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Lookup.cs" highlight="19-21, 42" :::

## <a name="client-get-specific-document"></a>クライアント: 特定のドキュメントを取得する 

この関数 API は、コンポーネント初期化の一部として、React アプリの中で `\src\pages\Details\Detail.js` で呼び出されます。

:::code language="javascript" source="~/azure-search-dotnet-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="c-models-to-support-function-app"></a>関数アプリをサポートする C# モデル

以下のモデルは、このアプリで関数をサポートするために使用されます。

:::code language="csharp" source="~/azure-search-dotnet-samples/search-website/api/Models.cs" :::

## <a name="next-steps"></a>次のステップ

* [Azure SQL データのインデックスを作成する](search-indexer-tutorial.md)
