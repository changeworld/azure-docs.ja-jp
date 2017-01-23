---
title: "Azure SQL Database のリソース制限 | Microsoft Docs"
description: "このページでは、Azure SQL Database に対するいくつかの一般的なリソース制限について説明します。"
services: sql-database
documentationcenter: na
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 884e519f-23bb-4b73-a718-00658629646a
ms.service: sql-database
ms.custom: overview
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 12/14/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 145cdc5b686692b44d2c3593a128689a56812610
ms.openlocfilehash: 4f4d5a4f3c401974157182afa9575f6f28a0673b


---
# <a name="azure-sql-database-resource-limits"></a>Azure SQL Database のリソース制限
## <a name="overview"></a>Overview
Azure SQL Database では、**リソース ガバナンス**と**制限の適用**という 2 つの異なるメカニズムを使用して、データベースで使用できるリソースを管理します。 このトピックでは、リソース管理のこれら 2 つの主な領域について説明します。

## <a name="resource-governance"></a>リソース ガバナンス
Basic、Standard、および Premium サービス プランの設計目標の 1 つは、Azure SQL Database の動作を、データベースが他のデータベースから完全に分離された専用のコンピューターで稼働しているかのようにすることです。 リソース ガバナンスは、この動作をエミュレートします。 集計されたリソース使用率が、データベースに割り当てられた利用可能な CPU、メモリ、ログ I/O、データ I/O のリソースの最大値に達すると、リソース ガバナンスは、実行中のクエリをキューに配置し、リソースが解放されると、キューに配置されたクエリにそのリソースを割り当てます。

専用のコンピューターの場合と同様、利用可能なリソースをすべて利用すると、現在実行中のクエリの実行時間が長くなり、コマンドがクライアントでタイムアウトする可能性があります。 積極的な再試行ロジックを使用するアプリケーションやデータベースに対して高い頻度でクエリを実行するアプリケーションでは、同時要求の制限に達したときに新しいクエリを実行しようとするとエラー メッセージが表示される場合があります。

### <a name="recommendations"></a>推奨事項:
データベースの最大使用率に近づいてきたら、リソース使用率に加えて、クエリの平均応答時間も監視します。 クエリの待機時間が長くなる場合、一般的には 3 つのオプションがあります。

1. データベースへの着信要求を少なくして、タイムアウトと大量の要求を防ぎます。
2. データベースにより高いパフォーマンス レベルを割り当てます。
3. クエリを最適化して、各クエリのリソース使用率を引き下げます。 詳細については、Azure SQL Database パフォーマンス ガイダンスのクエリのチューニングとヒント設定に関するセクションを参照してください。

## <a name="enforcement-of-limits"></a>制限の適用
制限に達すると、新しい要求を拒否することによって CPU、メモリ、ログ I/O、およびデータ I/O 以外のリソースが適用されます。 達した制限に応じて、クライアントは [エラー メッセージ](sql-database-develop-error-messages.md) を受け取ります。

たとえば、SQL Database への接続数と処理可能な同時要求の数が制限されます。 SQL Database では、接続プールをサポートするために、データベースへの接続数が同時要求の数を上回ることを許可します。 利用可能な接続数はアプリケーションで簡単に制御できるのに対し、並列要求の数の見積もりや制御は困難であることがよくあります。 特に、負荷のピーク時に、アプリケーションから送信される要求が多すぎる場合や、データベースがそのリソースの制限に達し、クエリの実行時間が長くなることが原因でワーカー スレッドが蓄積され始める場合に、エラーが発生することがあります。

## <a name="service-tiers-and-performance-levels"></a>サービス プランとパフォーマンス レベル
スタンドアロン データベースとエラスティック プールの両方に、サービス階層とパフォーマンス レベルがあります。

### <a name="standalone-databases"></a>スタンドアロン データベース
スタンドアロン データベースの場合、データベースの制限はデータベース サービス階層とパフォーマンス レベルによって決まります。 次の表では、パフォーマンス レベルごとの Basic、Standard、Premium の各データベースの特性について説明します。

[!INCLUDE [SQL DB service tiers table](../../includes/sql-database-service-tiers-table.md)]

### <a name="elastic-pools"></a>エラスティック プール
[エラスティック プール](sql-database-elastic-pool.md) は、プール内のデータベース全体のリソースを共有します。 次の表では、Basic、Standard、および Premium のエラスティック プールの特性について説明します。

[!INCLUDE [SQL DB service tiers table for elastic databases](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

前の表に示されている各リソースの詳細な定義いついては、「 [サービス レベルの機能と制限](sql-database-performance-guidance.md#service-tier-capabilities-and-limits)」の説明を参照してください。 サービス階層の概要については、 [Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)に関するページを参照してください。

## <a name="other-sql-database-limits"></a>その他の SQL Database の制限
| 領域 | 制限 | Description |
| --- | --- | --- |
| サブスクリプションあたりの自動エクスポートを使用するデータベース |10 |自動エクスポートを使用すると、カスタム スケジュールを作成して、SQL Database をバックアップできます。 この機能のプレビューは、2017 年 3 月 1 日に終了します。  |
| サーバーあたりのデータベース |最大 5000 |V12 サーバーでは、サーバーあたり最大 5000 個のデータベースが許可されています。 |
| サーバーあたりの DTU |45000 |データベース、エラスティック プール、データ ウェアハウスをプロビジョニングする場合、V12 サーバーではサーバーあたり 45000 DTU を使用できます。 |

## <a name="resources"></a>Resources
[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)

[Azure SQL Database のサービス階層とパフォーマンス レベル](sql-database-service-tiers.md)

[SQL Database クライアント プログラムのエラー メッセージ](sql-database-develop-error-messages.md)




<!--HONumber=Dec16_HO2-->


