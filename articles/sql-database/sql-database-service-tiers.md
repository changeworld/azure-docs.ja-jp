<properties
   pageTitle="SQL Database のサービス階層"
   description="Azure SQL Database のサービス階層のパフォーマンスとビジネス継続性機能を比較し、ダウンタイムもなくオンデマンドで拡張する際のコストと機能の適切なバランスを見つけてください。"
   services="sql-database"
   documentationCenter=""
   authors="shontnew"
   manager="jeffreyg"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="04/15/2015"
   ms.author="shkurhek"/>

# サービス階層

[SQL Database](sql-database-technical-overview.md) は、Basic、Standard、Premium のサービス階層で使用できます。これら 3 つのサービス階層は、いずれも稼働時間の [SLA](http://azure.microsoft.com/support/legal/sla/) が 99.99% で、予測可能なパフォーマンス、柔軟なビジネス継続性のオプション、セキュリティ機能、1 時間単位の課金体系などが提供されます。各サービス階層内に複数のパフォーマンス レベルがあるため、ワークロードのニーズに最も合ったレベルを柔軟に選択できます。スケールアップまたはスケールダウンする必要がある場合は、Azure ポータルでデータベースの階層を簡単に変更することができます。このときアプリケーションのダウンタイムはありません。詳細については、「[データベースのサービス階層とパフォーマンス レベルの変更](https://msdn.microsoft.com/library/azure/dn369872.aspx)」を参照してください。

> [AZURE.IMPORTANT]Web および Business エディションは廃止される予定です。[Web および Business エディションのアップグレード](sql-database-upgrade-new-service-tiers.md)方法に関するドキュメントを参照してください。Web エディションと Business エディションの使用を続行する場合は、[終了に関する FAQ](http://azure.microsoft.com/pricing/details/sql-database/web-business/) のページをお読みください。

## 基本

Basic 階層は、トランザクションのワークロードが軽いアプリケーション用に設計されています。一般的なユース ケースとして、指定された時点で単一の操作を実行する小規模なデータベースが必要な軽量のアプリケーションがあります。

**パフォーマンス、サイズ、機能**


| サービス階層 | 基本 |
| :-- | :-- |
| データベース スループット単位 (DTU) | 5 |
| データベースの最大サイズ | 2 GB |
| 特定の時点に復元 (PITR) | 最後の 7 日間のミリ秒まで |
| 障害復旧 | 地理リストア、任意の Azure リージョンに復元 |
| パフォーマンス目標 | 1 時間あたりのトランザクション レート |


## 標準

Standard 階層は、トランザクション ワークロードを開始するための主要オプションです。このオプションにより、Basic 階層に比べてパフォーマンスが向上し、より優れた組み込みのビジネス継続性機能を提供します。一般的なユース ケースとして、複数の同時実行トランザクションを使用するアプリケーションがあります。

**パフォーマンスとサイズ**


| サービス階層 | Standard S0 | Standard S1 | Standard S2 | Standard S3 |
| :-- | :-- | :-- | :-- | :-- |
| データベース スループット単位 (DTU) | 10 | 20 | 50 | 100 |
| データベースの最大サイズ | 250 GB | 250 GB | 250 GB | 250 GB |


**機能**


| サービス階層 | Standard (S0、S1、S2、S3) |
| :-- | :-- |
| 特定の時点に復元 (PITR) | 最後の 14 日間のミリ秒まで |
| 障害復旧 | 標準 geo レプリケーション、オフライン セカンダリ 1 |
| パフォーマンス目標 | 1 分あたりのトランザクション レート |


## プレミアム

Premium 階層は、ミッション クリティカルなアプリケーション用に設計されています。最高レベルのパフォーマンスのほか、最大 4 つの Azure リージョンを自由に選択できるアクティブ geo レプリケーションなど、高度なビジネス継続性機能へのアクセスを提供します。一般的なユース ケースとして、トランザクション ボリュームが大きく、同時ユーザーが多数存在するミッション クリティカルなアプリケーションがあります。

**パフォーマンスとサイズ**


| サービス階層 | Premium P1 | Premium P2 | Premium P3 |
| :-- | :-- | :-- | :-- |
| データベース スループット単位 (DTU) | 125 | 250 | 1,000 |
| データベースの最大サイズ | 500 GB | 500 GB | 500 GB |


**機能**


| サービス階層 | Premium (P1、P2、P3) |
| :-- | :-- |
| 特定の時点に復元 (PITR) | 最後の 35 日間のミリ秒まで |
| 障害復旧 | アクティブ geo レプリケーション、最大 4 つのオンラインの読み取り可能なセカンダリ |
| パフォーマンス目標 | 1 秒あたりのトランザクション レート |


これらの階層の料金の詳細については、「[SQL Database 料金](http://azure.microsoft.com/pricing/details/sql-database/)」を参照してください。

SQL Database の階層については、これで理解できました。では、[無料試用版](http://azure.microsoft.com/pricing/free-trial/)で実際に試してみましょう。また、[初めて SQL Database を作成する方法](sql-database-get-started.md)について詳しく学びましょう。

<!---HONumber=58--> 