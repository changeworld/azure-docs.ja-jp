---
title: Visual Studio 2019 をインストールする
description: Synapse SQL 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 47002a59bf085b2a943a40a0804fd9b9a2a75092
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422231"
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

Visual Studio **16.3 以上** をダウンロードし、インストールする方法については、[Visual Studio 2019 のダウンロード](https://visualstudio.microsoft.com/downloads/) ページをご覧ください。 インストール中、データ ストレージと処理ワークロードを選択します。 スタンドアロン SSDT インストールは Visual Studio 2019 では必須でなくなりました。

## <a name="unsupported-features-in-ssdt"></a>SSDT でサポートされていない機能

Synapse SQL の機能リリースに SSDT のサポートが含まれない場合があります。 現在サポートされていない機能は次のとおりです。


- [ワークロード管理](sql-data-warehouse-workload-management.md) - ワークロード グループと分類子
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (テーブル値関数を含む)
  - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)してください。
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)してください。
- [ID 列](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)があるテーブル
- 次のような特定の T-SQL 機能:
   - [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql?view=sqlallproducts-allversions) 文字列関数内の *WITHIN GROUP* 句。

## <a name="next-steps"></a>次のステップ

これで、最新バージョンの SSDT がインストールされたので、SQL プールに[接続](sql-data-warehouse-query-visual-studio.md)できるようになりました。
