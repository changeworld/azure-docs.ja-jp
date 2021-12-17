---
title: Visual Studio 2019 をインストールする
description: Synapse SQL 用に Visual Studio と SQL Server Development Tools (SSDT) をインストールします
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 378c36f1df7c83c6309be44d24ec671b64e34c08
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131054611"
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
- [行レベルのセキュリティ](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (テーブル値関数を含む)
  - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/d365community/idea/a9192b4c-0b25-ec11-b6e6-000d3a4f07b8)してください。
  - この機能がサポートされるようにするには、[サポート チケットを送信するか投票](https://feedback.azure.com/d365community/idea/ab192b4c-0b25-ec11-b6e6-000d3a4f07b8)してください。
- 次のような特定の T-SQL 機能:
   - [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) 文字列関数内の *WITHIN GROUP* 句。

## <a name="next-steps"></a>次のステップ

これで、最新バージョンの SSDT がインストールされたので、SQL プールに[接続](sql-data-warehouse-query-visual-studio.md)できるようになりました。