<properties 
   pageTitle="Azure SQL Database の価格レベルの提案" 
   description="Azure ポータルで価格レベルを変更するとき、価格レベルの提案により、既存の Azure SQL Database のワークロードを実行するのに最適なレベルが提案されます。価格レベルは、SQL Database のサービス レベルとパフォーマンス レベルを説明します。" 
   services="sql-database" 
   documentationCenter="" 
   authors="stevestein" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management" 
   ms.date="08/08/2016"
   ms.author="sstein"/>

# SQL Database の価格レベルの提案

 価格レベルの提案では、既存の Azure SQL Database のワークロードを実行するのに最適なサービス レベルとパフォーマンス レベルを提案します。

> [AZURE.NOTE] 価格レベルの提案は、Web データベース、ビジネス データベース、およびエラスティック データベース プールのみを対象として、[Azure ポータル](https://portal.azure.com/)でのみ利用できます。


価格レベルの提案は、次のタスクの実行中に取得します。

- [SQL Database のサービス階層とパフォーマンス レベル (価格レベル) を変更する](sql-database-scale-up.md)
- [Azure SQL サーバーを V12 にアップグレードする](sql-database-upgrade-server-portal.md)
- V12 サーバーに移動します。「[SQL Database の価格レベルの提案](sql-database-service-tier-advisor.md)」を参照してください。
- [エラスティック データベース プールを作成します](sql-database-elastic-pool.md#elastic-database-pool-pricing-tier-recommendations)





## Overview

SQL Database サービスでは、SQL Database の過去のリソースの使用状況を評価して、現在のパフォーマンス要件と機能要件が分析されます。また、最小限許容可能なサービス階層については、データベースのサイズと有効になっている[ビジネス継続性](sql-database-business-continuity.md)に基づいて判断されます。

この情報を分析することにより、データベースの一般的なワークロードを実行し、現在の機能セットを維持するために最適なサービス階層とパフォーマンス レベルが提案されます。

- 過去 15 ～ 30 日間の履歴データ (リソースの使用状況、データベース サイズ、データベース アクティビティ) が検査され、使用されたリソースの量と、現在利用可能なサービス階層とパフォーマンス レベルの実際の制限とが比較されます。
- データは 15 秒間隔で分析されます。それぞれの間隔の結果セットは、その結果セットのワークロードを処理するために最適な既存のサービス階層とパフォーマンス レベルに分類されます。
- これらの 15 秒間隔のサンプルはより大きな 15 ～ 30 日間隔の分析に集計され、履歴のワークロードの 95% を最適に処理できるサービス階層とパフォーマンス レベルが提案されます。

### 推奨事項

データベースの使用量に基づく提案は、現在次の 2 つのカテゴリに分類されます。


| 推奨 | Description |
| :--- | :--- |
| アップグレード | 新しいレベルにアップグレードします。 |
| 使用不可 | データベースは、最小のワークロードまたは約 35 日間のアクティビティを必要とします。有効な提案を行ううえで十分なデータがありません。 |

## 価格レベルの提案を受ける

価格レベルの提案を受けるには、既存の Web または Business データベースを選択して、**[すべての設定]** をクリックし、**[価格レベル (DTU のスケール)]** をクリックします。(価格レベルの提案は、[SQL サーバーを V12 にアップグレードする](sql-database-upgrade-server-portal.md)ときにも利用できます)。

1. [Azure ポータル](https://portal.azure.com/)にサインインします。
2. **[参照]**、**[SQL データベース]** の順にクリックします。
4. **[SQL データベース]** ブレードで、推奨情報を見るデータベースをクリックします。

    ![データベースの選択][1]

5. データベース ブレードで、**[すべての設定]** を選択し、**[価格レベル (DTU のスケール)]** を選択します。


7. **[お勧めの価格レベル]** が開き、提案されたレベルをクリックして **[選択]** をクリックすると、そのレベルに変更できます。

    ![プレビューのサインアップ][4]

8. オプションで、**[使用状況の詳細の表示]** をクリックして **[お勧めの価格レベルの詳細]** ブレードを開きます。このブレードでは、データベースに対して提案されるレベル、現在のレベルと提案されたレベルでの機能の比較、過去のリソース利用状況の分析グラフを表示できます。

    ![プレビューのサインアップ][5]



## まとめ

価格レベルの提案では、自動的に各 SQL データベースの製品利用統計情報データが収集され、データベースの実際のパフォーマンス ニーズと機能要件に基づいてサービス階層とパフォーマンス レベルの最適な組み合わせが提案されます。[設定] ブレードで、**[価格レベル (DTU のスケール)]** をクリックして、Web および Business データベースの価格レベルの提案を確認します。



## 次のステップ

データベースの内容によっては、アップグレードまたはダウングレードをすぐに実行することはできません。ポータルでは、データベースが新しいレベルに移行されるときに通知が提供されます。また、SQL Database サーバーのマスター データベースで [sys.dm\_operation\_status (Azure SQL Database )](https://msdn.microsoft.com/library/dn270022.aspx) ビューを照会することにより、アップグレードを監視できます。


<!--Image references-->
[1]: ./media/sql-database-service-tier-advisor/select-database.png
[4]: ./media/sql-database-service-tier-advisor/choose-pricing-tier.png
[5]: ./media/sql-database-service-tier-advisor/usage-details.png


 

<!---HONumber=AcomDC_0810_2016-->