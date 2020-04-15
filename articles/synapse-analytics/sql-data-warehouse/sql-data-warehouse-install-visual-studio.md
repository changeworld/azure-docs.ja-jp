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
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745171"
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

Visual Studio **16.3 以上**をダウンロードし、インストールする方法については、[Visual Studio 2019 のダウンロード](https://visualstudio.microsoft.com/downloads/) ページをご覧ください。 インストール中、データ ストレージと処理ワークロードを選択します。 スタンドアロン SSDT インストールは Visual Studio 2019 では必須でなくなりました。

## <a name="unsupported-features-in-ssdt"></a>SSDT でサポートされていない機能

Synapse SQL の機能リリースに SSDT のサポートが含まれない場合があります。 現在サポートされていない機能は次のとおりです。

- [具体化されたビュー](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [順序指定クラスター化列ストア インデックス](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [COPY ステートメント](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ワークロード管理](sql-data-warehouse-workload-management.md) - ワークロード グループと分類子
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security)してください。
- [動的データ マスク](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking)してください。
- [テーブルの制約](sql-data-warehouse-table-constraints.md#table-constraints)はサポートされていません。 これらのテーブル オブジェクトについては、ビルド アクションを "なし" に設定します。

## <a name="next-steps"></a>次のステップ

これで、最新バージョンの SSDT がインストールされたので、SQL プールに[接続](sql-data-warehouse-query-visual-studio.md)できるようになりました。
