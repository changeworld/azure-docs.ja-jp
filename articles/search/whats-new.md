---
title: 新機能のお知らせ
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: overview
ms.date: 01/30/2020
ms.openlocfilehash: 74f69f789ecb0d529e52054eeb1ac7ccb51bd6c2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588827"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。

<a name="new-service-name"></a>

## <a name="new-service-name"></a>新しいサービス名

Azure Search は、コア操作での拡張された (ただし、省略可能な) 認知スキルと AI 処理の使用を反映するために、**Azure Cognitive Search** に名前が変更されました。 API のバージョン、NuGet のパッケージ、名前空間、およびエンドポイントは変更されません。 新規および既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="feature-announcements"></a>機能のお知らせ

### <a name="february-2020"></a>2020 年 2 月

+ [PII 検出 (プレビュー)](cognitive-search-skill-pii-detection.md) は、入力テキストから個人を特定できる情報を抽出する認知スキルであり、インデックス作成中に使用されます。入力テキストから個人を特定できる情報をさまざまな方法でマスクするためのオプションがあります。

+ [カスタム エンティティの参照 (プレビュー)](cognitive-search-skill-custom-entity-lookup.md ) では、ユーザーが定義したカスタムの単語と語句のリストからテキストが検索されます。 このリストを使用して、エンティティが一致するすべての文書がラベル付けされます。 このスキルは、ある程度のあいまい一致もサポートしており、類似しているが完全一致ではない一致を見つけるために適用できます。 

### <a name="january-2020"></a>2020 年 1 月

+ [カスタマー マネージド暗号化キー](search-security-manage-encryption-keys.md)が一般提供されるようになりました。 REST を使用している場合は、`api-version=2019-05-06` を使用して機能にアクセスできます。 マネージ コードの場合は、機能はプレビューではなくなりましたが、正しいパッケージはまだ [.NET SDK バージョン 8.0-preview](search-dotnet-sdk-migration-version-9.md) です。 

+ 検索サービスへのプライベート アクセスは、次の 2 つのメカニズムで利用できます (どちらも現在プレビュー段階です)。

  + Management REST API `api-version=2019-10-01-Preview` を使用してサービスを作成することにより、特定の IP アドレスにアクセスを制限できます。 プレビュー API の [CreateOrUpdate API](https://docs.microsoft.com/rest/api/searchmanagement/services/createorupdate) には、新しい **IpRule** および **NetworkRuleSet** プロパティがあります。 このプレビュー機能は、特定のリージョンで使用できます。 詳しくは、「[Management REST API の使用方法](https://docs.microsoft.com/rest/api/searchmanagement/search-howto-management-rest-api)」をご覧ください。

  + 現在はアクセスが制限されたプレビューで利用でき、同じ仮想ネットワーク上のクライアントからの接続に対して Azure プライベート エンドポイントをサポートする Azure Search Service をプロビジョニングすることができます。 詳しくは、[安全な接続のためのプライベート エンドポイントの作成](service-create-private-endpoint.md)に関する記事をご覧ください。

### <a name="december-2019"></a>2019 年 12 月

+ [アプリの作成 (プレビュー)](search-create-app-portal.md) はポータルの新しいウィザードです。これを使ってダウンロード可能な HTML ファイルを生成できます。 このファイルには、検索サービスのインデックスにバインドされた、操作可能な "localhost" スタイルの Web アプリをレンダリングする埋め込みスクリプトが付属しています。 ページはウィザードで構成できます。また、検索バー、結果領域、サイドバー ナビゲーション、および先行入力クエリのサポートを含めることができます。 HTML をオフラインに変更して、ワークフローや外観を拡張したりカスタマイズしたりすることができます。

+ [安全な接続のためのプライベート エンドポイントの作成 (プレビュー)](service-create-private-endpoint.md) に関する記事では、検索サービスへのセキュリティで保護された接続のために Private Link を設定する方法について説明されています。 このプレビュー機能は要求することで利用できるようになり、ソリューションの一部として [Azure Private Link](../private-link/private-link-overview.md) と [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) が使用されます。

### <a name="november-2019---ignite-conference"></a>2019 年 11 月 - Ignite Conference

+ [インクリメンタル エンリッチメント (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) では、既に処理されているコンテンツを失うことなく特定のステップまたはフェーズを操作できるように、エンリッチメント パイプラインにキャッシュとステートフル性が追加されます。 これまでは、エンリッチメント パイプラインを変更すると、完全なリビルドが必要でした。 インクリメンタル エンリッチメントを使用すると、コストのかかる分析の出力 (特に画像分析) が維持されます。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [ドキュメント抽出 (プレビュー)](cognitive-search-skill-document-extraction.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、スキルセット内からファイルのコンテンツを抽出できます。 ドキュメント解析は、これまでスキルセットの実行前にのみ行われていました。 このスキルを追加することで、スキルセットの実行内でこの操作を実行することもできます。

+ [テキスト翻訳](cognitive-search-skill-text-translation.md)は、インデックスの作成中に使用される認知スキルで、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)は、Power BI デスクトップのナレッジ ストアで、強化されたコンテンツの視覚化と分析をすぐに開始することができます。 このテンプレートは、[データのインポート ウィザード](knowledge-store-create-portal.md)を使用して作成された Azure テーブルのプロジェクション向けに設計されています。

+ インデクサーで [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md)、[Cosmos DB Cassandra API (プレビュー)](search-howto-index-cosmosdb.md) のサポートを開始しました。 [こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)を使ってサインアップできます。 プレビュー プログラムへの参加が承認されると、確認メールが届きます。

### <a name="july-2019"></a>2019 年 7 月

+ [Azure Government クラウド](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)で一般提供されています。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。