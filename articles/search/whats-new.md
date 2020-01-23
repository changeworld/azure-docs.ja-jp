---
title: 新機能のお知らせ
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: e115316daf3673d9ad854e7ccd6d5256d729b5af
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/13/2020
ms.locfileid: "75921009"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure Search の新しいサービス名

コア操作での認識スキルと AI 処理の拡張された使用を反映するため、Azure Search の名前が **Azure Cognitive Search** に変更されました。 認知スキルによって新機能が追加されますが、AI の使用は厳密には省略可能です。 AI を使用せずに Azure Cognitive Search を引き続き使用して、クラウドで作成および管理するインデックスにある非公開で、異種の、テキストベースのコンテンツに対して、リッチな、フル テキスト検索ソリューションを構築することができます。 

API バージョン、Nuget パッケージ、名前空間、およびエンドポイントは変更されません。 既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="feature-announcements"></a>機能のお知らせ

### <a name="january-2020"></a>2020 年 1 月

+ [カスタマー マネージド暗号化キー](search-security-manage-encryption-keys.md)が一般提供されるようになりました。 REST を使用している場合は、`api-version=2019-05-06` を使用して機能にアクセスできます。 マネージ コードの場合は、機能はプレビューではなくなりましたが、正しいパッケージはまだ [.NET SDK バージョン 8.0-preview](search-dotnet-sdk-migration-version-9.md) です。 

+ 検索サービスへのプライベート アクセスは、次の 2 つのメカニズムで利用できます。

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

+ [テキスト翻訳 (プレビュー)](cognitive-search-skill-text-translation.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)は、Power BI デスクトップのナレッジ ストアで、強化されたコンテンツの視覚化と分析をすぐに開始することができます。 このテンプレートは、[データのインポート ウィザード](knowledge-store-create-portal.md)を使用して作成された Azure テーブルのプロジェクション向けに設計されています。

+ インデクサーで [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md)、[Cosmos DB Cassandra API (プレビュー)](search-howto-index-cosmosdb.md) のサポートを開始しました。 [こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)を使ってサインアップできます。 プレビュー プログラムへの参加が承認されると、確認メールが届きます。

### <a name="july-2019"></a>2019 年 7 月

+ [Azure Government クラウド](../azure-government/documentation-government-services-webandmobile.md#azure-cognitive-search)で一般提供されています。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。