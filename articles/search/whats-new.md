---
title: 新機能のお知らせ
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search への Azure Search のサービス名の変更を含む、新機能や拡張機能についてのお知らせです。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 26f6c651b78099eff80b6af57d2047cc8696f4c2
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112200"
---
# <a name="whats-new-in-azure-cognitive-search"></a>Azure Cognitive Search の新機能

サービス内の新機能について説明します。 このページをブックマークして、常にサービスの最新情報を確認してください。

<a name="new-service-name"></a>

## <a name="new-service-name-for-azure-search"></a>Azure Search の新しいサービス名

コア操作での認識スキルと AI 処理の拡張された使用を反映するため、Azure Search の名前が **Azure Cognitive Search** に変更されました。 認知スキルによって新機能が追加されますが、AI の使用は厳密には省略可能です。 AI を使用せずに Azure Cognitive Search を引き続き使用して、クラウドで作成および管理するインデックスにある非公開で、異種の、テキストベースのコンテンツに対して、リッチな、フル テキスト検索ソリューションを構築することができます。 

API バージョン、Nuget パッケージ、名前空間、およびエンドポイントは変更されません。 既存の検索ソリューションは、サービス名の変更の影響を受けません。

## <a name="feature-announcements"></a>機能のお知らせ

2019 年 11 月 4 日 - Ignite カンファレンス

+ [増分インデックス (プレビュー)](cognitive-search-incremental-indexing-conceptual.md) は、エンリッチメント パイプラインに変更を加えるときに絶対に必要なステップのみを処理または再処理できるようにします。 これは、以前に分析したイメージ コンテンツがある場合に特に便利です。 コストのかかる分析の出力が保存され、追加のインデックス作成またはエンリッチメントの基礎として使用されます。

<!-- 
+ Custom Entity Lookup is a cognitive skill used during indexing that allows you to provide a list of custom entities (such as part numbers, diseases, or names of locations you care about) that should be found within the text. It supports fuzzy matching, case-insensitive matching, and entity synonyms. -->

+ [ドキュメント抽出 (プレビュー)](cognitive-search-skill-document-extraction.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、スキルセット内からファイルのコンテンツを抽出できます。 ドキュメント解析は、これまでスキルセットの実行前にのみ行われていました。 このスキルを追加することで、スキルセットの実行内でこの操作を実行することもできます。

+ [テキスト翻訳 (プレビュー)](cognitive-search-skill-text-translation.md) は、インデックスの作成中に使用されるコグニティブ スキルであり、テキストを評価し、各レコードについて、指定したターゲット言語に翻訳されたテキストを返します。

+ [Power BI テンプレート](https://github.com/Azure-Samples/cognitive-search-templates/blob/master/README.md)は、Power BI デスクトップのナレッジ ストアで、強化されたコンテンツの視覚化と分析をすぐに開始することができます。 このテンプレートは、[データのインポート ウィザード](knowledge-store-create-portal.md)を使用して作成された Azure テーブルのプロジェクション向けに設計されています。

+ インデクサーで [Azure Data Lake Storage Gen2 (プレビュー)](search-howto-index-azure-data-lake-storage.md)、[Cosmos DB Gremlin API (プレビュー)](search-howto-index-cosmosdb.md)、[Cosmos DB Cassandra API (プレビュー)](search-howto-index-cosmosdb.md) のサポートを開始しました。 [こちらのフォーム](https://aka.ms/azure-cognitive-search/indexer-preview)を使ってサインアップできます。 プレビュー プログラムへの参加が承認されると、確認メールが届きます。

## <a name="service-updates"></a>サービスの更新情報

Azure Cognitive Search の[サービス更新のお知らせ](https://azure.microsoft.com/updates/?product=search&status=all)に関する情報については、Azure の Web サイトで参照できます。