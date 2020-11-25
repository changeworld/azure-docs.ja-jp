---
title: Python のサンプル
titleSuffix: Azure Cognitive Search
description: Python または REST 用の Azure .NET SDK を使用する Azure Cognitive Search デモ Python コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 3de630552f7ad2cc941fe23369398c10ffce5870
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686464"
---
# <a name="python-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の Python コード サンプル

Azure Cognitive Search の機能を示す Python コード サンプルについて説明します。 主なリポジトリは次のとおりです。

| リポジトリ | Description |
|------------|-------------|
| [azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples/](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples) | Azure SDK チームによって作成されたサンプル (この SDK の Azure.Search.Documents クライアント ライブラリに付属)。 また、クライアント ライブラリの[単体テスト](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/tests)を確認して、さまざまな API がどのように呼び出されるかを理解することもできます。 |
| [Azure-Samples/azure-search-python-samples](https://github.com/Azure-Samples/azure-search-python-samples) | 操作方法に関する記事に付随するコード サンプル。例えば、[クイック スタート:.Python で検索インデックスを作成する](search-get-started-python.md)。|

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=csharp&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="python-sdk-samples"></a>Python SDK のサンプル

Azure SDK for Python には、多数のサンプルと、前提条件とパッケージのインストールが記載されている[概要ページ](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/search/azure-search-documents/samples)があります。 このページには、利便性のためにここに一覧で示している次のサンプルへのリンクも含まれています。

| サンプル | 説明 |
|---------|-------------|
| [認証](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_authentication.py) | クライアントを構成し、サービスに対して認証する方法を示します。 | 
| [インデックスの作成、読み取り、更新、削除の操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_index_crud_operations.py) | [検索インデックス](search-what-is-an-index.md)を作成、更新、取得、一覧表示、削除する方法を示します。 |
| [インデクサーの作成、読み取り、更新、削除の操作](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexers_operations.py) | [インデクサー](search-indexer-overview.md)を作成、更新、取得、一覧表示、リセット、削除する方法を示します。 |
| [検索インデクサー データ ソース](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_indexer_datasource_skillset.py) | [サポートされている Azure データ ソース](search-indexer-overview.md#supported-data-sources)のインデクサーベースのインデックス作成に必要なインデクサー データ ソースを作成、更新、取得、一覧表示、削除する方法を示します。 |
| [シノニム](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_synonym_map_operations.py) | [同意語マップ](search-synonyms.md)を作成、更新、取得、一覧表示、削除する方法を示します。  |
| [ドキュメントを読み込む](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_crud_operations.py) | [データ インポート](search-what-is-data-import.md)操作においてインデックスにドキュメントをアップロードまたはマージする方法を示します。 |
| [単純なクエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_simple_query.py) | [基本的なクエリ](search-query-overview.md)を設定する方法を示します。 |
| [フィルター クエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_filter_query.py) | [フィルター式](search-filters.md)の設定方法を示します。 |
| [ファセット クエリ](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/search/azure-search-documents/samples/sample_facet_query.py) | [ファセット](search-filters-facets.md)の使用方法を示します。 |

## <a name="documentation-samples"></a>ドキュメントとサンプル

次のサンプルには、関連記事が 「[Azure Cognitive Search のドキュメント](https://docs.microsoft.com/azure/search/)」にあります。

| サンプル | 説明 | 
|---------|-------------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Quickstart) | [クイック スタート:.Python で検索インデックスを作成する](search-get-started-python.md)に関する記事のソース コード。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)  | 「[チュートリアル:Python と AI を使用して Azure Blob から検索可能なコンテンツを生成する](cognitive-search-tutorial-blob-python.md)」のソース コード。  |
| [AzureML-Custom-Skill](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/AzureML-Custom-Skill)  | 「[例:Python を使用してカスタム スキルを作成する](cognitive-search-custom-skill-python.md)」のソース コード。  |