---
title: Azure SQL Database - 自動チューニング | Microsoft Docs
description: Azure SQL Database は SQL クエリを分析し、ユーザーのワークロードに自動的に適応します。
services: sql-database
author: danimir
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: v-daljep
ms.reviewer: carlrab
ms.openlocfilehash: dd6e8f5f46e9fdf6887cc2a0b0c7b15bbd00fabd
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39626201"
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Azure SQL Database での自動チューニング

Azure SQL Database の自動チューニングは、人工知能を利用した継続的なパフォーマンス チューニングによって、最大限のパフォーマンスと安定したワークロードを実現します。

自動チューニングはフル マネージドのサービスであり、組み込みのインテリジェンスを使用してデータベースに対して実行されるクエリを継続的に監視し、パフォーマンスを自動的に改善します。 これは変動するワークロードに対してデータベースを適応させること、およびチューニングの推奨事項を適用することを動的に実施することで実現しています。 自動チューニングは、人工知能を介して Azure 上のすべてのデータベースから水平方向に学習し、チューニング操作を動的に改善します。 自動チューニングが有効な状態で Azure SQL Database を稼動している時間が長くなるほど、パフォーマンスが向上します。

Azure SQL Database の自動チューニングは、安定した最大限のワークロードの実行を可能にするために利用できる機能の中で、最も重要な機能の 1 つと言えます。

## <a name="what-can-automatic-tuning-do-for-you"></a>自動チューニングでできること

- Azure SQL Database のパフォーマンスの自動チューニング
- パフォーマンス向上の自動検証
- 自動ロールバックと自己修正
- チューニングの履歴ログ
- 手動でデプロイするためのアクション T-SQL スクリプトのチューニング
- ワークロードのパフォーマンスのプロアクティブな監視
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="safe-reliable-and-proven"></a>安全性、信頼性、実証済み

Azure SQL Database に適用されるチューニング操作は、最も負荷の高いワークロードのパフォーマンスに対して、安全性の高い操作になっています。 このシステムは、ユーザーのワークロードに干渉しないように注意して設計されています。 自動チューニングの推奨事項は、使用率が低い時間帯にのみ適用されます。 ワークロードのパフォーマンスを損なわないように、自動チューニング操作を、システムによって一時的に無効にすることもできます。 その場合、システムによって無効化されていることを示すメッセージが Azure Portal に表示されます。 自動チューニングはリソースの優先順位が最も高いワークロードに注意を払います。

自動チューニングは非常に成熟したしくみですが、Azure で実行されている数十万のデータベース上でさらに洗練されたものになりました。 適用される自動チューニング操作は自動的に検証され、ワークロードのパフォーマンスに積極的な改善がみられるようにします。 パフォーマンスの後退に関する推奨事項が動的に検出され、すばやく以前の設定に戻されます。 チューニングの履歴ログを見ると、各 Azure SQL Database に対して実行されたチューニングによる改善の痕跡をはっきりと確認できます。 

![自動チューニングのしくみ](./media/sql-database-automatic-tuning/how-does-automatic-tuning-work.png)

Azure SQL Database の自動チューニングの核となるロジックは、SQL Server の自動チューニング エンジンでも使用されています。 組み込みインテリジェンスのしくみに関する技術的な情報の詳細については、[SQL Server の自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)に関する記事をご覧ください。

## <a name="use-automatic-tuning"></a>自動チューニングを使用する

自動チューニングは、お使いのサブスクリプションで手動で有効にする必要があります。 Azure Portal を使用して自動チューニングを有効にするには、「[自動チューニングの有効化](sql-database-automatic-tuning-enable.md)」をご覧ください。

自動チューニングは、パフォーマンス向上の自動検証などの、チューニングの推奨事項を自動的に適用することで自律的な動作が可能です。 

細かく制御する場合は、チューニングの推奨事項が自動的に適用されるのを無効にできます。チューニングの推奨事項は Azure Portal から手動で適用できます。 ソリューションを使用して自動チューニングに関する推奨事項のみを表示し、任意のスクリプトやツールを使用して手動で適用することもできます。 

自動チューニングのしくみの概要と一般的な使用シナリオについては、埋め込みのビデオをご覧ください。


> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Improve-Azure-SQL-Database-Performance-with-Automatic-Tuning/player]
>

## <a name="automatic-tuning-options"></a>自動チューニング オプション

Azure SQL Database で使用可能な自動チューニング オプションは次のとおりです。
 1. **CREATE INDEX** - ワークロードのパフォーマンスを改善させる可能性があるインデックスを特定し、インデックスを作成して、クエリのパフォーマンスが改善されたことを確認します。
 2. **DROP INDEX** - 冗長なインデックスや重複するインデックス、また長期間使用されていないインデックスを特定します。 なお、このオプションはパーティション切り替えやインデックス ヒントを使用するアプリケーションと互換性がありません。
 3. **FORCE LAST GOOD PLAN** - 以前の良好なプランよりも速度の低い実行プランを使用している SQL クエリを特定し、その低速なプランの代わりに、最後に確認された良好なプランを使用してクエリを実行します。

Azure SQL Database は、データベースを最適化できる **CREATE INDEX**、**DROP INDEX**、および **FORCE LAST GOOD PLAN** の推奨事項を識別し、Azure Portal でそれらを表示します。 変更する必要があるインデックスの識別の詳細については、「[Azure Portal を使用した SQL Database Advisor](sql-database-advisor-portal.md)」参照してください。 ポータルを使用して推奨事項を手動で適用することも、Azure SQL Database で自動的に推奨事項を適用し、変更後のワークロードを監視して、推奨事項によりワークロードのパフォーマンスが向上したことを確認することもできます。 

自動チューニング オプションをデータベースごとに個別に有効または無効にすることも、論理サーバーで構成し、サーバーから設定を継承するすべてのデータベースに適用することもできます。 論理サーバーでは、自動チューニングの設定について、Azure の既定値を継承できます。 現時点の Azure の既定値では、FORCE_LAST_GOOD_PLAN と CREATE_INDEX が有効に、DROP_INDEX が無効に設定されています。

サーバーでの自動チューニング オプションの構成と、親サーバーに属するデータベースの設定の継承は、自動チューニングを構成するための推奨される方法です。この方法では、多数のデータベースの自動チューニング オプションの管理が簡素化されるためです。

## <a name="next-steps"></a>次の手順

- Azure SQL Database で自動チューニングを有効にするには、「[自動チューニングの有効化](sql-database-automatic-tuning-enable.md)」をご覧ください。
- 自動チューニングの推奨設定を手動で確認して適用するには、「[パフォーマンスに関する推奨事項の検索と適用](sql-database-advisor-portal.md)」をご覧ください。
- 自動チューニングの推奨情報に関するメール通知の作成については、「[自動チューニングの電子メール通知](sql-database-automatic-tuning-email-notifications.md)」を参照してください。
- 自動チューニングに使用されている組み込みインテリジェンスの詳細については、「[人工知能による Azure SQL Database のチューニング](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/)」をご覧ください。
- Azure SQL Database と SQL server 2017 における自動チューニングの動作の詳細については、[SQL Server の自動チューニング](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning)に関する記事をご覧ください。
