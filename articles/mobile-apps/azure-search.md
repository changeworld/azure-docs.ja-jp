---
title: Azure Search を使用したモバイル アプリケーション開発向けの AI 搭載クラウド検索サービス
description: モバイル アプリケーション開発向けの AI 搭載クラウド検索サービスを使用するためのサービスについて説明します。
author: elamalani
ms.assetid: 34a8a070-0123-8982-8345-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 59dc729dc17fae6bd4b190d21b157eda1d772b42
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795139"
---
# <a name="ai-powered-cloud-service-with-azure-search"></a>Azure Search を使用した AI 搭載クラウド サービス
[Azure Search](https://azure.microsoft.com/services/search/) は、Web、モバイル、およびエンタープライズ アプリケーションのプライベートな異種コンテンツに対するリッチな検索機能を追加するための API とツールを開発者に提供する、サービスとしての検索クラウド ソリューションです。 カスタム コードを使用して、データ インジェスト (インデックス作成) を呼び出し、インデックスを作成して読み込みます。 一方で、アプリケーション コードを使用して、クエリ要求を発行し、応答を処理します。 検索エクスペリエンスは、Azure Search の機能を使用してクライアント内で定義します。クエリは、自分で作成および所有し、サービスに保存する永続化されたインデックスに対して実行します。

## <a name="azure-search-features"></a>Azure Search の機能
- フルテキスト検索、シンプルなクエリ構文、Lucene クエリ構文を使用する**自由形式のテキスト検索**。
- ドキュメント自体の値の関数として、関連性をモデル化する**シンプルなスコアリング プロファイル**。
- **地域検索**により、ユーザーは物理的な場所に対する検索結果の近接度に基づいてデータを探索できます。
- オートコンプリート、検索候補、並べ替え、ページングを使用した**ユーザー エクスペリエンス機能**。
- イメージおよびテキスト分析のための**コグニティブ検索**をインデックス作成パイプラインに適用して、生のコンテンツからテキスト情報を抽出できます。
- インデックス付けの際に作成されたエンリッチメントを保存する**ナレッジ ストア**。
- **データ ソース**として Azure SQL Database、Azure Cosmos DB、または Azure Blob Storage に接続して、プライマリ データ ストア内の検索可能なコンテンツを抽出します。
- **データのインポート ウィザード**を使用してインデクサーを構成したり、**インデックス デザイナー**を使用してインデックスを設定したりできます。また、**Search エクスプローラー**を使用してクエリをテストしたり、スコアリング プロファイルを調整したりできます。

Azure Search 機能の詳細については、[こちら](/azure/search/search-what-is-azure-search#feature-descriptions)を参照してください。

## <a name="references"></a>参照
- [Azure Portal](https://portal.azure.com) 
- [ドキュメント](/azure/search/)
- [開発者ガイド](https://azure.microsoft.com/resources/iot-developers-guide/)
- [Azure Search を使用する方法](/azure/search/search-what-is-azure-search#how-to-use-azure-search)
- [クイック スタート](/azure/search/search-create-service-portal)

  