---
title: SQL Analytics のレコメンデーション
description: SQL Analytics のレコメンデーションとその生成方法について説明します
services: synapse-analytics
author: kevinvngo
manager: craigg-msft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/05/2020
ms.author: kevin
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 6d57fdb035e076c75363d23fbf36d39eeb72bb3f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350683"
---
# <a name="sql-analytics-recommendations"></a>SQL Analytics のレコメンデーション

この記事では、Azure Advisor によって提供される SQL Analytics のレコメンデーションについて説明します。  

SQL Analytics からは、データ ウェアハウスのワークロードのパフォーマンスを常に最適化するためのレコメンデーションが提供されます。 レコメンデーションは [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations) と強固に統合され、[Azure portal](https://aka.ms/Azureadvisor) 内でベスト プラクティスを直接提供します。 SQL Analytics を使うと、1 日の間にアクティブなワークロードのテレメトリが収集され、レコメンデーションが提示されます。 以下にサポートされるレコメンデーションのシナリオの概要と、推奨されるアクションを適用する方法を示します。

[レコメンデーションは今すぐ確認](https://aka.ms/Azureadvisor)することができます。 現在、この機能は Gen2 データ ウェアハウスにのみ適用できます。 

## <a name="data-skew"></a>データ スキュー

データ スキューは、ワークロードの実行時に余分なデータ移動やリソースのボトルネックを引き起こす可能性があります。 次のドキュメントでは、データ スキューを特定し、最適な配布キーを選択することでその発生を回避する方法について説明します。

- [スキューを特定して削除する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>統計情報がない、または統計情報が古い

最適でない統計情報は、SQL のクエリ オプティマイザーが最適でないクエリ計画を作成する原因となるため、クエリ パフォーマンスに深刻な影響を及ぼす可能性があります。 次のドキュメントでは、統計情報の作成や更新に関するベスト プラクティスについて説明します。

- [テーブル統計情報の作成と更新](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

これらのレコメンデーションによって影響を受けるテーブルの一覧を表示するには、次の [T-SQL スクリプト](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)を実行します。 Advisor は、同じ T-SQL スクリプトを継続的に実行してこれらのレコメンデーションを生成します。

## <a name="replicate-tables"></a>テーブルのレプリケート

レプリケート テーブルのレコメンデーションについて、Advisor は、以下の物理的特性に基づいてテーブルの候補を検出します。

- レプリケート テーブルのサイズ
- 列の数
- テーブルの分散の種類
- パーティションの数

Advisor は、テーブル アクセスの頻度、返された平均行数、データ ウェアハウスのサイズとアクティビティに関連するしきい値などのワークロード ベースのヒューリスティックを継続的に活用し、品質の高いレコメンデーションが生成されるようにしています。 

以下は、レプリケート テーブルの各レコメンデーションについて Azure portal に示されることがある、ワークロード ベースのヒューリスティックについての説明です。

- [Scan avg]\(スキャン平均値) - 各テーブル アクセスでテーブルから返された行数の割合の、過去 7 日間にわたる平均値
- Frequent read, no update(頻繁な読み取り、更新なし - アクセス アクティビティを示しているが過去 7 日間にテーブルは更新されていないことを示します
- [Read/update ratio]\(読み取り/更新の比率) - 過去 7 日間にわたる、テーブルがアクセスされた頻度とテーブルが更新された回数との比率
- [Activity]\(アクティビティ) - アクセスのアクティビティに基づいて、使用状況を測定します。 これは、テーブル アクセス アクティビティを、過去 7 日間の、データ ウェアハウス間にわたる平均テーブル アクセス アクティビティと比較します。 

現在、Advisor に一度に表示されるのは、最上位のアクティビティを優先順位付けするクラスター化列ストア インデックスを含め、最大 4 つのレプリケート テーブルの候補のみです。

> [!IMPORTANT]
> レプリケート テーブルのレコメンデーションは、完全に試験済みの状態ではなく、アカウント データの移動操作は考慮されていません。 これをヒューリスティックとして追加することには取り組んでいますが、それまでは、レコメンデーションの適用後、実際のワークロードを必ず検証する必要があります。 ワークロードの状況を悪化させるレプリケート テーブルのレコメンデーションが見つかった場合は、sqldwadvisor@service.microsoft.com までご連絡ください。 レプリケート テーブルに関する詳細については、次の[ドキュメント](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)を参照してください。
