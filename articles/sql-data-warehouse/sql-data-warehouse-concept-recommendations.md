---
title: SQL Data Warehouse のレコメンデーション - 概念 | Microsoft Docs
description: SQL Data Warehouse のレコメンデーションとそれらがどのように生成されるかについて説明します
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 57bce631a570f549d46a9b0beefcb5adce4decfc
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380116"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse のレコメンデーション

この記事では、Azure Advisor を通じて SQL Data Warehouse によって提供されるレコメンデーションについて説明します。  

SQL Data Warehouse は、お使いのデータ ウェアハウスのパフォーマンスが終始最適化されるように、レコメンデーションを提供します。 データ ウェアハウスのレコメンデーションは [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) と強固に統合され、[Azure portal](https://aka.ms/Azureadvisor) 内でベスト プラクティスを直接提供します。 SQL Data Warehouse はお使いのデータ ウェアハウスの現在の状態を分析し、利用統計情報を収集して、アクティブなワークロードに対するレコメンデーションを毎日提供します。 以下にサポートされるデータ ウェアハウスのレコメンデーションのシナリオの概要と、推奨されるアクションを適用する方法を示します。

SQL Data Warehouse Advisor についてフィードバックがある、または問題が発生した場合は、[sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com) にお問い合わせください。   

今すぐ[こちら](https://aka.ms/Azureadvisor)をクリックしてレコメンデーションを確認してください。 現在、この機能は Gen2 データ ウェアハウスにのみ適用できます。 

## <a name="data-skew"></a>データ スキュー

データ スキューは、ワークロードの実行時に余分なデータ移動やリソースのボトルネックを引き起こす可能性があります。 次のドキュメントでは、データ スキューを特定し、最適な配布キーを選択することでその発生を回避する方法について説明します。

- [スキューを特定して削除する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>統計情報がない、または統計情報が古い

最適でない統計情報は、SQL Data Warehouse のクエリ オプティマイザーが最適でないクエリ計画を作成する原因となるため、クエリ パフォーマンスに深刻な影響を及ぼす可能性があります。 次のドキュメントでは、統計情報の作成や更新に関するベスト プラクティスについて説明します。

- [テーブル統計情報の作成と更新](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

これらのレコメンデーションによって影響を受けるテーブルの一覧を表示するには、次の[T-SQL スクリプト](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)を実行します。 Advisor は、同じ T-SQL スクリプトを継続的に実行してこれらのレコメンデーションを生成します。
