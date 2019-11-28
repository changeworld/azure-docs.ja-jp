---
title: Visual Studio 2019 をインストールする
description: Azure SQL Data Warehouse 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
services: sql-data-warehouse
ms.custom: seo-lt-2019
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 11/06/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 758c9b323e78e7e86312555f5fabdcc9c4c28330
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123717"
---
# <a name="getting-started-with-visual-studio-2019-for-sql-data-warehouse"></a>SQL Data Warehouse 用の Visual Studio 2019 を始める
Visual Studio **2019** SQL Server Data Tools (SSDT) は次のことを可能にする単一ツールです。

- SQL Data Warehouse 用のアプリケーションを接続、クエリ実行、開発する 
- オブジェクト エクスプローラーを活用し、テーブル、ビュー、ストアド プロシージャなど、データ モデル内のあらゆるオブジェクトを視覚的に調べます。
- オブジェクトの T-SQL データ定義言語 (DDL) スクリプトを生成する
- 状態に基づく手法と SSDT データベース プロジェクトを利用し、データ ウェアハウスを開発する
- Git と Azure Repos など、データベース プロジェクトをソース管理システムと統合する
- Azure DevOps など、自動化サーバーを利用し、継続的インテグレーション/デプロイのパイプラインを設定する

## <a name="install-visual-studio-2019"></a>Visual Studio 2019 をインストールする
Visual Studio **16.3 以上**をダウンロードし、インストールする方法については、[Visual Studio 2019 をダウンロードする][] ページをご覧ください。 インストール中、データ ストレージと処理ワークロードを選択します。 スタンドアロン SSDT インストールは Visual Studio 2019 では必須でなくなりました。

## <a name="unsupported-features-in-ssdt"></a>SSDT でサポートされていない機能

SQL Data Warehouse の機能リリースに SSDT のサポートが含まれない場合があります。 現在サポートされていない機能は次のとおりです。

- [具体化されたビュー](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (作業中)
- [順序指定クラスター化列ストア インデックス](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (作業中)
- [COPY ステートメント](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (作業中)
- [ワークロード管理](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - ワークロード グループと分類子 (作業中)
- [行レベルのセキュリティ](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - この機能がサポートされるようにするには、サポート チケットを送信するか、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)で投票してください。
- [動的データ マスク](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - この機能がサポートされるようにするには、サポート チケットを送信するか、[こちら](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)で投票してください。 
- [PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 関数 

## <a name="next-steps"></a>次の手順

これで、最新バージョンの SSDT がインストールされたので、SQL Data Warehouse に[接続][connect]できるようになりました。

<!--Anchors-->

<!--Image references-->

<!--Articles-->
[connect]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Visual Studio 2019 をダウンロードする]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
