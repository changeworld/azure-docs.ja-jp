---
title: Visual Studio 2019 をインストールする
description: SQL Analytics 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: d0a1772706fa838f51322d5f5d5bd1b46eb9144a
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351617"
---
# <a name="getting-started-with-visual-studio-2019"></a>Visual Studio 2019 の概要
Visual Studio **2019** SQL Server Data Tools (SSDT) は次のことを可能にする単一ツールです。

- アプリケーションを接続、クエリ実行、開発する
- オブジェクト エクスプローラーを活用し、テーブル、ビュー、ストアド プロシージャなど、データ モデル内のあらゆるオブジェクトを視覚的に調べます。
- オブジェクトの T-SQL データ定義言語 (DDL) スクリプトを生成する
- 状態に基づく手法と SSDT データベース プロジェクトを利用し、データ ウェアハウスを開発する
- Git と Azure Repos など、データベース プロジェクトをソース管理システムと統合する
- Azure DevOps など、自動化サーバーを利用し、継続的インテグレーション/デプロイのパイプラインを設定する

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 をインストールする
Visual Studio [Visual Studio 2019 をダウンロードする][]をダウンロードし、インストールする方法については、**Visual Studio 2019 のダウンロード** ページをご覧ください。 インストール中、データ ストレージと処理ワークロードを選択します。 スタンドアロン SSDT インストールは Visual Studio 2019 では必須でなくなりました。

## <a name="unsupported-features-in-ssdt"></a>SSDT でサポートされていない機能

SQL Analytics の機能リリースに SSDT のサポートが含まれない場合があります。 現在サポートされていない機能は次のとおりです。

- [具体化されたビュー](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (作業中)
- [順序指定クラスター化列ストア インデックス](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (作業中)
- [COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (作業中)
- [ワークロード管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - ワークロード グループと分類子 (作業中)
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - この機能がサポートされるようにするには、サポート チケットを送信するか、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)で投票してください。
- [動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - この機能がサポートされるようにするには、サポート チケットを送信するか、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)で投票してください。 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 関数 
- [テーブルの制約](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints)はサポートされていません。 これらのテーブル オブジェクトについては、ビルド アクションを "なし" に設定します。

## <a name="next-steps"></a>次のステップ

これで、最新バージョンの SSDT がインストールされたので、SQL プールに[接続](sql-data-warehouse-query-visual-studio.md)できるようになりました。




<!--Other-->

[Visual Studio 2019 をダウンロードする]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
