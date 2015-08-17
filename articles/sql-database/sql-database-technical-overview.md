<properties
   pageTitle="SQL Database とは"
   description="Azure SQL Database、Microsoft のリレーショナル データベース管理システム (RDBMS)、クラウドの PaaS ソリューションの技術的な詳細情報と機能をご確認ください。"
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
   ms.date="07/17/2015"
   ms.author="shkurhek"/>

# SQL Database の概要

SQL Database は、ミッション クリティカルな機能を組み込んだ、Microsoft SQL Server エンジンに基づくクラウド内のリレーショナル データベース サービスです。SQL Database は、予測可能なパフォーマンス、拡張性、ビジネス継続性、データ保護、ゼロに近い管理をクラウド開発者とソリューション設計者に提供します。特に SQL Database は、既存の SQL Server のツール、ライブラリ、API をサポートしているため、容易にクラウドに移行できます。SQL Database は[数秒で稼働](sql-database-get-started.md)でき、仮想マシンやインフラストラクチャを管理する必要もありません。DBA に似たプログラムの機能により、効率的な DevOps が可能になります。また自己管理機能により、データベースのメンテナンスがほとんど不要になります。これにより、アプリケーションの開発と管理に重点を置くことができます。

SQL Database を使用すると、ダウンタイムなしで単一のデータベースを[スケールアップ](sql-database-service-tiers.md)し、必要に応じてダイヤルアップまたはダウンできる予測可能なパフォーマンスを得ることができます。その一方、[エラスティック データベース](sql-database-elastic-pool.md)を選択することで、管理している予算内で数千ものデータベースをスケールアウトし、[エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md)の強力な管理機能と[エラスティック データベース ツール](sql-database-elastic-scale-get-started.md)のプログラム可能な機能を活用することができます。

可用性の [SLA](http://azure.microsoft.com/support/legal/sla/) は業界トップレベルの 99.99% であり、アプリケーションのダウンタイムはほとんどありません。監査、復元、geo レプリケーションを介した組み込みのデータ保護により、確実にデータを保護できます。SQL Database では、過去 35 日間のトランザクションから、任意の時点のデータを復元できます。また、任意の Azure リージョンにデータをレプリケートし、さまざまなビジネス ニーズに対応する地理的な障害復旧ポリシーを簡単に実装できます。[最新バージョンの SQL Database](sql-database-preview-whats-new.md) (V12) では、SQL Server エンジンとのほぼ完全な互換性を提供します。

SQL Database は、Basic、Standard、Premium の[サービス階層](sql-database-service-tiers.md)で使用できます。これらのサービス階層では、軽量のデータベースから大規模なデータベースまで、さまざまなワークロードをサポートしているため、サービス階層を移動したり組み合わせたりして、革新的なアプリケーションを設計できます。Azure の強力さと幅広さを利用して、さまざまな Azure サービスを SQL Database とうまく組み合わせることにより、独自の最新アプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

[無料評価版](http://azure.microsoft.com/pricing/free-trial/)を使用し、少しだけ時間を割いて、[最初の SQL Database を作成](sql-database-get-started.md)しましょう。
 

<!---HONumber=August15_HO6-->