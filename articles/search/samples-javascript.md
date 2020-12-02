---
title: JavaScript のサンプル
titleSuffix: Azure Cognitive Search
description: JavaScript 用の Azure .NET SDK を使用する Azure Cognitive Search デモ JavaScript コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 234c70fffb6f353c670d23624cc446fdaf6bd886
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498967"
---
# <a name="javascript-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の JavaScript コード サンプル

Azure Cognitive Search の機能を示す JavaScript コード サンプルについて説明します。 主なリポジトリは次のとおりです。

| リポジトリ | Description |
|------------|-------------|
| [azure-sdk-for-js/tree/master/sdk/search/search-documents](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents) | Azure SDK チームが作成したサンプル (SDK の Azure.Search.Documents クライアント ライブラリに付属)。 クライアント ライブラリの[単体テスト](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/test)を調査して、さまざまな API がどのように呼び出されるかを確認することもできます。 |
| [Azure-Samples/azure-search-javascript-samples](https://github.com/Azure-Samples/azure-search-javascript-samples) | ハウツー記事に付随するコード サンプル。"[クイック スタート:JavaScript で検索インデックスを作成する](search-get-started-javascript.md)" のような記事。|

> [!Tip]
> Github で製品、サービス、言語でフィルター処理された Microsoft コード サンプルを検索するために、[サンプル ブラウザー](/samples/browse/?languages=csharp&products=azure-cognitive-search)を試してください。

## <a name="javascript-sdk-samples"></a>JavaScript SDK サンプル

Azure SDK for Java には、パッケージのインストール、クライアントのセットアップ、トラブルシューティングを取り上げた多数のサンプルと[作業開始ページ](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/README.md#getting-started)が含まれています。 このページには、利便性のため、ここに一覧で示した以下のサンプル カテゴリについての説明もあります。

| サンプル | 説明 |
|---------|-------------|
| [インデックス](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexes) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 このサンプル カテゴリには、サービス統計のサンプルも含まれます。 |
| [dataSourceConnections (インデクサー用)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/dataSourceConnections) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [インデクサー](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/indexers) |  [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法が示されています。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/skillSets) |   アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/javascript/src/synonymMaps) | [シノニム マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法が示されています。  |
| [クエリ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/javascript/src/readonlyQuery.js) | Microsoft によってホストされている、読み取り専用のパブリック インデックスに対するクエリの実行が示されています。  |

## <a name="typescript-samples"></a>TypeScript のサンプル

SDK では、利便性のため、ここに記載されている TypeScript の例も提供されています。

| サンプル | 説明 |
|---------|-------------|
| [インデックス](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexes) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 このサンプル カテゴリには、サービス統計のサンプルも含まれます。 |
| [dataSourceConnections (インデクサー用)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/dataSourceConnections) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [インデクサー](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/indexers) |  [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法が示されています。|
| [skillSet](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/skillSets) |   アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成、更新、取得、一覧表示、削除する方法が示されています。 |
| [synonymMaps](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/search/search-documents/samples/typescript/src/synonymMaps) | [シノニム マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法が示されています。  |
| [クエリ](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/search/search-documents/samples/typescript/src/readonlyQuery.ts) | Microsoft によってホストされている、読み取り専用のパブリック インデックスに対するクエリの実行が示されています。  |

## <a name="documentation-samples"></a>ドキュメントとサンプル

以下のサンプルには、「[Azure Cognitive Search のドキュメント](./index.yml)」に関連記事があります。

| サンプル | 説明 | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/quickstart/v11) | 次の記事のソース コード。"[クイック スタート:JavaScript で検索インデックスを作成する](search-get-started-javascript.md)" のような記事。  |

## <a name="standalone-samples"></a>独立したサンプル

| サンプル | 説明 |
|---------|-------------|
| [azure-search-react-template](https://github.com/dereklegenzoff/azure-search-react-template) | Azure Cognitive Search 用の React テンプレート (github.com) |