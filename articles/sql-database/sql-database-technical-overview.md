---
title: "SQL Database とは SQL Database の概要 | Microsoft Docs"
description: 'Get an introduction to SQL Database: technical details and capabilities of Microsoft''s relational database management system (RDBMS) in the cloud.'
keywords: "sql の概要,sql の紹介,sql database とは"
services: sql-database
documentationcenter: 
author: shontnew
manager: jhubbard
editor: cgronlun
ms.assetid: c561f600-a292-4e3b-b1d4-8ab89b81db48
ms.service: sql-database
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 11/08/2016
ms.author: shkurhek
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 23d68a523fa9a79d2b5154eef04a5f6e706bdeb2


---
# <a name="what-is-sql-database-introduction-to-sql-database"></a>SQL Database とは SQL Database の概要
SQL Database は、ミッション クリティカルな機能を備えた、市場をリードする Microsoft SQL Server エンジンに基づくクラウド内のリレーショナル データベース サービスです。 SQL Database は、予測可能なパフォーマンス、ダウンタイムなしの拡張性、ビジネス継続性、データ保護を提供し、いずれの場合も管理をほとんど必要としません。 仮想マシンとインフラストラクチャの管理に煩わされることなく、迅速なアプリケーション開発および製品化に要する時間の短縮化に専念することができます。 SQL Database は、[SQL Server](https://msdn.microsoft.com/library/bb545450.aspx) エンジンを基礎としているため、既存の SQL Server ツール、ライブラリ、および API をサポートしています。したがって、クラウドへの移行およびクラウドへの拡張が簡単です。

この記事では、パフォーマンス、スケーラビリティ、および管理容易性に関連する、SQL Database の中心概念および機能について紹介し、詳細を参照するためのリンクも提供します。 先に進む準備ができたら、[最初の SQL Database の作成](sql-database-get-started.md)または[エラスティック データベース プールの作成](sql-database-elastic-pool-create-portal.md)を数分で実行できます。 さらに詳しく知りたい場合は、この 30 分間のビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON326/player]
> 
> 

## <a name="adjust-performance-and-scale-without-downtime"></a>ダウンタイムなしでのパフォーマンスの調整とスケール
SQL Database は、Basic、Standard、Premium の *サービス階層*で使用できます。 各サービス階層では、軽量のデータベース ワークロードから重量のデータベース ワークロードに至るまでサポートできるように、 [さまざまなレベルのパフォーマンスと機能](sql-database-service-tiers.md) を提供しています。 最初のアプリケーションを月数ドルで小規模データベースにビルドし、やがてアプリケーションが世界中に急速に広まってきたら、アプリケーションやお客様にダウンタイムを発生させずに、いつでも手動またはプログラムで [サービス層を変更](sql-database-scale-up.md) することができます。

特に使用パターンが比較的予測可能である場合、多くのビジネスとアプリについては、データベースを作成し、要求に応じて単一のデータベースのパフォーマンスを調整する能力は十分です。 しかし、使用パターンが予測できない場合、コストおよびビジネス モデルを管理するのが難しくなる可能性があります。

この問題は、SQL Database の[エラスティック プール](sql-database-elastic-pool.md)によって解決されます。 概念は単純です。 プールにパフォーマンスを割り当てたら、単一のデータベースのパフォーマンスに対してではなく、プールの全体的なパフォーマンスに対して支払いを行います。 データベースのパフォーマンスを高くしたり低くしたりして調整する必要はありません。 "*エラスティック データベース*" と呼ばれるプール内のデータベースでは、需要に合わせて自動的にスケールアップおよびスケールダウンが行われます。 エラスティック データベースでの使用はプールの上限を超えることはありません。したがってデータベースの使用状況が予測できなくても、コストが予測可能なことに変わりはありません。 さらに、[プールに対してデータベースの追加および削除を行う](sql-database-elastic-pool-manage-portal.md)ことで、完全に設定予算内で、アプリケーションを数個のデータベースから何千ものデータベースに及ぶ範囲でスケーリングすることができます。 エラスティック プールを使用する SaaS アプリケーションの設計パターンの詳細については、「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

単一のデータベースまたはエラスティック データベースのどちらを使用したとしても、それに固定されることはありません。 Single Database をエラスティック データベース プールと組み合わせ、Single Database とプールのサービス階層を変更することで、革新的なデザインを作成することができます。 さらに、Azure の強力さと幅広さを利用して、さまざまな Azure サービスを SQL Database とうまく組み合わせることにより、独自の最新アプリ設計のニーズを満たし、コストとリソースの効率性を向上させ、新たなビジネス チャンスを開くことができます。

しかし、データベースとデータベース プールの相対的なパフォーマンスはどのようにして比較できますか? パフォーマンスの高低を調整するとき、何に基づいて右クリックでストップをかけますか? 答えは、単一のデータベースの場合はデータベース トランザクション ユニット (DTU)、エラスティック データベースとデータベース プールの場合はエラスティック DTU (eDTU) です。 詳細については、「 [SQL Database のオプションとパフォーマンス: 各サービス階層で使用できる内容について理解します](sql-database-service-tiers.md) 」を参照してください。

## <a name="keep-your-app-and-business-running"></a>アプリケーションとビジネスの継続的な稼働
Microsoft が管理するデータセンターのグローバル ネットワークによって強化された、Azure の業界をリードする可用性 99.99% のサービス レベル アグリーメント [(SLA)](http://azure.microsoft.com/support/legal/sla/) により、アプリケーションの 24 時間 365 日の継続的な稼働が可能になります。 すべての SQL Database で、組み込みのデータ保護やフォールト トレランスを利用できます。これらは、本来なら自身で設計、購入、構築、および管理する必要があります。 それでも、ビジネスの要求に応じて、災害やエラーなどが発生した場合にアプリケーションおよびビジネスが迅速に回復するように、追加の保護レイヤーを必要とする場合があります。 SQL Database では、サービス階層ごとに、異なる機能メニューが用意されており、それを使用すると、稼働させてその状態を維持することができます。 ポイントインタイム リストアを使用すると、データベースを 35 日間分遡って以前の状態に戻すことができます。 さらに、データベースをホストしているデータ センターで障害が発生した場合は、別のリージョンにあるデータベース レプリカにフェールオーバーことができます。 あるいは、レプリカを使用して、アップグレードまたは異なる地域への再配置を行うことができます。

![SQL Database Geo-Replication](./media/sql-database-technical-overview/azure_sqldb_map.png)

それぞれのサービス階層で使用できるさまざまなビジネス継続性機能の詳細については、「 [ビジネス継続性](sql-database-business-continuity.md) 」をご覧ください。

## <a name="secure-your-data"></a>データのセキュリティ保護
SQL Server は、アクセスの制限、データの保護、および監視アクティビティの支援を行う機能を備えた SQL Database によって、信頼性の高いデータ セキュリティを従来から維持してきました。 SQL Database で提供されているセキュリティ オプションの迅速なランダウンについては、「 [SQL Database のセキュリティ保護](sql-database-security.md) 」をご覧ください。 セキュリティ機能をより包括的に把握したい場合は、「 [SQL Server データベース エンジンと SQL Database のセキュリティ センター](https://msdn.microsoft.com/library/bb510589) 」をご覧ください。 Azure のプラットフォーム セキュリティについては、「 [Azure セキュリティ センター](https://azure.microsoft.com/support/trust-center/security/) 」をご覧ください。

## <a name="next-steps"></a>次のステップ
ここでは、SQL Database の概要を紹介し、"SQL Database とは何か" という問いに対する答えを示したので、次のステップに進むことができます。

* 単一データベースとエラスティック データベースのコストの比較と計算ツールについては、 [価格に関するページ](https://azure.microsoft.com/pricing/details/sql-database/) を参照してください。
* [エラスティック プール](sql-database-elastic-pool.md)について学習します。
* [最初のデータベースを作成する](sql-database-get-started.md)ことによって作業を開始します。
* [SSMS を使用した接続とクエリ](sql-database-connect-query-ssms.md)
* 初めてのアプリを C#、Java、Node.js、PHP、Python、Ruby で作成します。「[SQL Database と SQL Server の接続ライブラリ](sql-database-libraries.md)」を参照してください。
* 「 [Azure SQL Database サービスに関するすべてのトピック](sql-database-index-all-articles.md)」のタイトルと説明のインデックスを確認します。




<!--HONumber=Nov16_HO2-->


