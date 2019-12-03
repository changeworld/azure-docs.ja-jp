---
title: Azure Cognitive Search を使用したモバイル アプリケーション開発向けの AI 搭載クラウド検索サービス
description: モバイル アプリケーション開発向けの AI 搭載クラウド検索サービスを使用するためのサービスについて説明します。
author: elamalani
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 335df16e6a6436485521611aee0f0a62edca8a3b
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123456"
---
# <a name="ai-powered-cloud-service-with-azure-cognitive-search"></a>Azure Cognitive Search を使用した AI 搭載クラウド サービス
[Azure Cognitive Search](https://azure.microsoft.com/services/search/) は、Web、モバイル、およびエンタープライズ アプリケーションのプライベートな異種コンテンツに対するリッチな検索機能を追加するための API とツールを開発者に提供する、サービスとしての検索クラウド ソリューションです。 カスタム コードを使用して、データ インジェスト (インデックス作成) を呼び出し、インデックスを作成して読み込みます。 一方で、アプリケーション コードを使用して、クエリ要求を発行し、応答を処理します。 検索エクスペリエンスは、Azure Cognitive Search の機能を使用してクライアント内で定義します。クエリは、自分で作成および所有し、サービスに保存する永続化されたインデックスに対して実行します。

## <a name="azure-cognitive-search-features"></a>Azure Cognitive Search の機能
- フルテキスト検索、シンプルなクエリ構文、Lucene クエリ構文を使用する**自由形式のテキスト検索**。
- ドキュメント自体の値の関数として、関連性をモデル化する**シンプルなスコアリング プロファイル**。
- **地域検索**により、ユーザーは物理的な場所に対する検索結果の近接度に基づいてデータを探索できます。
- オートコンプリート、検索候補、並べ替え、ページングを使用した**ユーザー エクスペリエンス機能**。
- イメージおよびテキスト分析のための**コグニティブ検索**をインデックス作成パイプラインに適用して、生のコンテンツからテキスト情報を抽出できます。
- インデックス付けの際に作成されたエンリッチメントを保存する**ナレッジ ストア**。
- **データ ソース**として Azure SQL Database、Azure Cosmos DB、または Azure Blob Storage に接続して、プライマリ データ ストア内の検索可能なコンテンツを抽出します。
- **データのインポート ウィザード**を使用してインデクサーを構成したり、**インデックス デザイナー**を使用してインデックスを設定したりできます。また、**Search エクスプローラー**を使用してクエリをテストしたり、スコアリング プロファイルを調整したりできます。

Azure Cognitive Search [機能](/azure/search/search-what-is-azure-search#feature-descriptions)の詳細をご覧ください。

## <a name="references"></a>参照
- [Azure Portal](https://portal.azure.com) 
- [ドキュメント](/azure/search/)
- [開発者ガイド](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Azure Cognitive Search の使用方法](/azure/search/search-what-is-azure-search#how-to-use-azure-cognitive-search)
- [クイック スタート](/azure/search/search-create-service-portal)

  