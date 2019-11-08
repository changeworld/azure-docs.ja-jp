---
title: SQL データ同期を使用して Azure SQL Database Edge からデータを同期する | Microsoft Docs
description: Azure SQL Database Edge と Azure SQL データ同期の間でデータを同期する方法について説明します
keywords: sql database edge,sql database edge からデータを同期する, sql database edge データ同期
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 857cee30ac4c1002fb7ca57d6be5fa461a14e9ee
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509196"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-sql-database-using-sql-data-sync"></a>チュートリアル:SQL データ同期を使用して SQL Database Edge から Azure SQL Database にデータを同期する

このチュートリアルでは、SQL データ同期の*同期グループ*を使用して、Azure SQL Database Edge から Azure SQL Database にデータを増分同期する方法を説明します。 SQL データ同期は、Azure SQL Database 上に構築されているサービスであり、選択したデータを複数の SQL データベースや SQL Server インスタンスの間で双方向に同期させることができます。 Azure SQL データ同期の詳細については、[Azure SQL データ同期](../sql-database/sql-database-sync-data.md)に関するページを参照してください。

Azure SQL Database Edge は最新バージョンの [Microsoft SQL Server データベース エンジン](/sql/sql-server/sql-server-technical-documentation/)に基づいて構築されているため、オンプレミスの SQL Server インスタンスに適用できるデータ同期メカニズムを使用して、エッジ デバイスで実行されている SQL Database Edge インスタンスとの間でデータを同期することもできます。

## <a name="prerequisites"></a>前提条件

このチュートリアルには、[Azure SQL データ同期エージェント](../sql-database/sql-database-data-sync-agent.md)を使用して構成された Windows コンピューターが必要です。

## <a name="before-you-begin"></a>開始する前に

* Azure SQL Database を作成します。 Azure portal を使用して Azure SQL Database を作成する方法については、[Azure SQL Database での単一データベースの作成](../sql-database/sql-database-single-database-get-started.md?tabs=azure-portal)に関するページを参照してください。

* Azure SQL Database のデプロイ内に、テーブルおよびその他の必要なオブジェクトを作成します。

* Azure SQL Database Edge のデプロイ内に、必要なテーブルとオブジェクトを作成します。 詳細については、[SQL Database Edge での SQL Database DAC パッケージの使用](stream-analytics.md)に関するページを参照してください。

* Azure SQL Database Edge インスタンスを Azure SQL データ同期エージェントに登録します。 詳細については、[オンプレミスの SQL Server データベースの追加](../sql-database/sql-database-get-started-sql-data-sync.md#add-on-prem)に関するページを参照してください。

## <a name="sync-data-between-an-azure-sql-database-and-sql-database-edge"></a>Azure SQL Database と SQL Database Edge の間でデータを同期する

SQL データ同期を使用した Azure SQL Database と SQL Database Edge インスタンスとの間の同期設定には、3 つの主な手順が含まれます。  

1. Azure portal を使用して同期グループを作成します。 同期グループを作成するには、[Azure portal を使用した同期グループの作成](../sql-database/sql-database-get-started-sql-data-sync.md#create-sync-group)に関するページを参照してください。 同じ*ハブ* データベースを使用して複数の異なる同期グループを作成し、異なる SQL Database Edge インスタンスのデータを、Azure の 1 つ以上の SQL データベースに同期することができます。

2. 同期グループに同期メンバーを追加します。 同期グループにメンバーを追加するには、[SQL データ同期グループへのメンバーの追加](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)に関するページを参照してください。

3. 同期グループを構成し、この同期の一部となるテーブルを選択します。 同期グループを構成するには、[同期グループの構成](../sql-database/sql-database-get-started-sql-data-sync.md#add-sync-members)に関するページを参照してください。

上記の手順を完了すると、Azure SQL Database と SQL Database Edge インスタンスを含む同期グループができます。

SQL データ同期の詳細については、以下の記事を参照してください。

* [Azure SQL データ同期用の Data Sync Agent](../sql-database/sql-database-data-sync-agent.md)

* [ベスト プラクティス](../sql-database/sql-database-best-practices-data-sync.md)と [SQL データ同期に関する問題をトラブルシューティングする方法](../sql-database/sql-database-troubleshoot-data-sync.md)

* [Azure Monitor ログによる SQL データ同期の監視](../sql-database/sql-database-sync-monitor-oms.md)

* [Transact-SQL を使用した同期スキーマの更新](../sql-database/sql-database-update-sync-schema.md)または [PowerShell](../sql-database/scripts/sql-database-sync-update-schema.md)

## <a name="next-steps"></a>次の手順

* [PowerShell を使用して Azure SQL Database と Azure SQL Database Edge を同期します](../sql-database/scripts/sql-database-sync-data-between-azure-onprem.md)。 このチュートリアルでは、*OnPremiseServer* データベースの詳細を、Azure SQL Database Edge の詳細で置き換えます。
