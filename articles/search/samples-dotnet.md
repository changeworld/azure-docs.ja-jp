---
title: .NET のサンプル
titleSuffix: Azure Cognitive Search
description: .Net クライアント ライブラリを使用する Azure Cognitive Search デモ C# コード サンプルを紹介します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686588"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Azure Cognitive Search の .NET (C#) コード サンプル

Azure Cognitive Search の機能を示す C# コード サンプルについて説明します。 主なリポジトリは次のとおりです。

| リポジトリ | Description |
|------------|-------------|
| [azure-sdk-for-net/sdk/search/Azure.Search.Documents/samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Azure SDK チームによって作成されたサンプル (この SDK の Azure.Search.Documents クライアント ライブラリに付属)。 また、クライアント ライブラリの[単体テスト](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests)を確認して、さまざまな API がどのように呼び出されるかを理解することもできます。 |
| [Azure-Samples/azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | ドキュメントの操作方法に関する記事 ([.NET クライアント ライブラリの使用方法](search-howto-dotnet-sdk.md)など) に付随するサンプル。|
| [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started) | ドキュメントのクイックスタートとチュートリアルに付随するサンプル。|

> [!Tip]
> [サンプル ブラウザー](/samples/browse/?languages=csharp&products=azure-cognitive-search)を試して、製品、サービス、言語でフィルター処理された Microsoft コード サンプルを Github 内で検索してください。

## <a name="net-sdk-samples"></a>.NET SDK のサンプル

Azure SDK for .NET には、多数のサンプルと、それぞれについて説明する [サンプルの readme](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) が含まれています。 利便性のために、その一覧を下に記載します。

| サンプル | 説明 |
|---------|-------------|
| ["Hello world" (同期的に)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | 同期メソッドを使用してクライアントの作成、認証、エラー処理を行う方法を示します。|
| ["Hello world" (非同期的に)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | 非同期メソッドを使用してクライアントの作成、認証、エラー処理を行う方法を示します。  |
| [サービス レベルの操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | インデックス、インデクサー、データ ソース、スキルセット、および同意語マップを作成する方法を示します。 このサンプルでは、サービス統計情報を取得する方法と、インデックスのクエリを実行する方法についても示されています。  |
| [インデックスの操作](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | 既存のインデックスに対してアクションを実行する方法を示します。この場合、インデックスに格納されているドキュメントの数を取得します。  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | サポートされていないデータ型を操作する手法を示します。  |
| [ドキュメントのインデックス作成 (プッシュ モデル)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | "プッシュ" モデルのインデックス作成。サービスのインデックスに JSON ペイロードを送信します。   |
| [暗号化キーのサンプル](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | カスタマー マネージド暗号化キーを使用して、機密性の高いコンテンツに保護レイヤーを追加する方法を示します。  |

## <a name="documentation-samples"></a>ドキュメントとサンプル

次のサンプルには、関連記事が 「[Azure Cognitive Search のドキュメント](https://docs.microsoft.com/azure/search/)」にあります。

| サンプル | 説明 |
|---------|-------------|
| [クイックスタート](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | [クイック スタート:検索インデックスの作成](search-get-started-dotnet.md)に関する記事のソース コード。  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | [.NET クライアント ライブラリの使用方法](search-howto-dotnet-sdk.md)に関する記事のソース コード |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | 同意語の一覧は、クエリの拡張に使用され、インデックスの外部にある同等の用語を提供します。 このサンプルは、[例:C# での同意語の追加](search-synonyms-tutorial-sdk.md)に関する記事に含まれています。 |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | さまざまな記事に含まれるインデクサー関連のスニペットの背後にあるソース コード。 この例は、スケジュール、フィールド マッピング、およびパラメーターを含むインデクサーを構成する方法を示します。  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | [データの暗号化のためにカスタマー マネージド キーを構成する方法](search-security-manage-encryption-keys.md)に関する記事のソース コード |
| [C# での最初のアプリの作成](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  [チュートリアル:最初の検索アプリの作成](tutorial-csharp-create-first-app.md)に関する記事のソース コード。 ほとんどのサンプルはコンソール アプリケーションですが、この MVC サンプルでは Web ページを使用してサンプルの Hotels インデックスを配置し、基本的な検索、改ページ位置の自動修正、オートコンプリートおよび提案されたクエリ、ファセット、フィルターを示します。 |
| [multiple-data-sources](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | [チュートリアル:複数のデータ ソースからのインデックス作成](tutorial-multiple-data-sources.md)に関する記事のソース コード。 |
|  [optimize-data-indexing](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | [チュートリアル:プッシュ API を使用したインデックス作成の最適化](tutorial-optimize-indexing-push-api.md)に関する記事のソース コード。  |
| [tutorial-ai-enrichment](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | 「[チュートリアル:.NET SDK を使用して Azure BLOB から AI で生成する検索可能なコンテンツ](cognitive-search-tutorial-blob-dotnet.md)」のソース コード。  |

## <a name="standalone-samples-and-solutions"></a>スタンドアロンのサンプルとソリューション

| サンプル | 説明 |
|---------|-------------|
| [azure-search-power-skills](https://github.com/Azure-Samples/azure-search-power-skills)  | 獲得したソリューションに組み込むことができる、使用可能なカスタム スキルのソース コード。  |
| [ナレッジ マイニング ソリューション アクセラレータ](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | エンドツーエンドのナレッジ マイニング ソリューションのプロトタイプ作成に役立つテンプレート、サポート ファイル、および分析レポートが含まれています。  |
| [Covid-19 Search App リポジトリ](https://github.com/liamca/covid19search) | Cognitive Search ベースの [Covid-19 Search App](https://covid19search.azurewebsites.net/) のソース コード リポジトリ |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | [JFK ソリューション](https://www.microsoft.com/ai/ai-lab-jfk-files)の詳細を確認してください。 |