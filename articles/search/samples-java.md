---
title: Java のサンプル
titleSuffix: Azure Cognitive Search
description: Java 用の Azure .NET SDK を使用する Azure Cognitive Search デモ Java コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 10dff18f7b9db7273fcd6ec92bcca5970bb83b08
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2020
ms.locfileid: "96510371"
---
# <a name="java-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の Java コード サンプル

Azure Cognitive Search の機能を示す Java コード サンプルについて説明します。 主なリポジトリは次のとおりです。

| リポジトリ | Description |
|------------|-------------|
| [azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples) | Azure SDK チームが作成したサンプル (SDK の Azure.Search.Documents クライアント ライブラリに付属)。 クライアント ライブラリの[単体テスト](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/test)を調査して、さまざまな API がどのように呼び出されるかを確認することもできます。 |
| [Azure-Samples/azure-search-java-samples](https://github.com/Azure-Samples/azure-search-java-samples) | ハウツー記事に付随するコード サンプル。 **このリポジトリのサンプルはまだ、Azure SDK for Java を使用するように更新されていません**。 現在のところ、これらのサンプルでは、Java コードで REST API が呼び出されます。|

> [!Tip]
> Github で製品、サービス、言語でフィルター処理された Microsoft コード サンプルを検索するために、[サンプル ブラウザー](/samples/browse/?languages=java&products=azure-cognitive-search)を試してください。

## <a name="java-sdk-samples"></a>Java SDK のサンプル

Azure SDK for Java には、パッケージのインストールを取り上げた多数のサンプルと[作業開始ページ](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/search/azure-search-documents/src/samples)が含まれています。 このページには、幅広い例も記載されています。 便利なように、いくつかのより一般的な操作を以下の一覧に示します。

| サンプル | 説明 |
|---------|-------------|
| [検索インデックスの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexExample.java) | [検索インデックス](search-what-is-an-index.md)の作成方法が示されています。 |
| [シノニムの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SynonymMapsCreateExample.java) | [シノニム マップ](search-synonyms.md)の作成方法が示されています。  |
| [検索インデクサーの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateIndexerExample.java) | [インデクサー](search-indexer-overview.md)の作成方法が示されています。 |
| [検索インデクサー データ ソースの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/DataSourceExample.java) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成する方法が示されています。 |
| [スキルセットの作成](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/indexes/CreateSkillsetExample.java) |  アタッチされたインデクサーであり、インデックス作成中に AI ベースの強化を実行する[スキルセット](cognitive-search-working-with-skillsets.md)を作成する方法が示されています。 |
| [ドキュメントを読み込む](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/IndexContentManagementExample.java) | [データ インポート](search-what-is-data-import.md)操作においてインデックスにドキュメントをアップロードまたはマージする方法が示されています。 |
| [クエリ構文](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/search/azure-search-documents/src/samples/java/com/azure/search/documents/SearchAsyncWithFullyTypedDocumentsExample.java) | [基本的なクエリ](search-query-overview.md)を設定する方法が示されています。 |

## <a name="documentation-samples"></a>ドキュメントとサンプル

次のサンプルには、関連記事が 「[Azure Cognitive Search のドキュメント](./index.yml)」にあります。

| サンプル | 説明 | 
|---------|-------------|
| [quickstart](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/quickstart) | 次の記事のソース コード。"[クイック スタート:Java で検索インデックスを作成する](search-get-started-java.md)" のような記事。 このサンプルでは、REST API を呼び出します。 |
| [search-java-indexer-demo](https://github.com/Azure-Samples/azure-search-java-samples/tree/master/search-java-indexer-demo) | Java での Azure Cosmos DB インデクサーが示されています。 このサンプルでは、REST API を呼び出します。 |