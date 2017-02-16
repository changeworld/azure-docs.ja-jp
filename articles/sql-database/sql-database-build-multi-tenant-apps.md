---
title: "Azure SQL Database による分離性と効率性を備えたマルチテナント アプリの構築"
description: "SQL Database でマルチテナント アプリを構築する方法を説明します"
keywords: 
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 8ebd5499-ca03-4e4e-8755-4cb34339285c
ms.service: sql-database
ms.custom: benefits
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 187954f3ddafdbc17e341ce41f5b109cb95f8a24
ms.openlocfilehash: fb62e82865ac39031f86daa329f93261ec4ffd7b


---
# <a name="build-multi-tenant-apps-with-azure-sql-database-with-isolation-and-efficiency"></a>Azure SQL Database による分離性と効率性を備えたマルチテナント アプリの構築
## <a name="leverage-elastic-pools-and-build-more-efficient-multi-tenant-apps"></a>エラスティック プールを利用して効率的なマルチテナント アプリを構築する
多くの顧客を処理するマルチテナント アプリを作成する場合、カスタマー パフォーマンス、管理、セキュリティにおいてトレードオフを強いられることがよくあります。 Azure SQL Database のエラスティック プールがあれば、もう妥協をする必要はありません。 エラスティック データベース プールを使用すると、マルチテナント アプリを管理および監視し、データベースあたり&1; ユーザーという分離の利点が得られます。 「 [Azure SQL Database を使用するマルチテナント SaaS アプリケーションの設計パターン](sql-database-design-patterns-multi-tenancy-saas-applications.md)」を参照してください。

![build-multi-tenant-apps](./media/sql-database-build-multi-tenant-apps/sql-database-build-multi-tenant-apps.png)

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>
>

## <a name="auto-scaling-you-control"></a>制御を自動スケールする
プールは、必要に応じてエラスティック データベースのパフォーマンスとストレージ容量を自動的にスケーリングします。 プールに割り当てられているパフォーマンスを制御し、必要に応じてエラスティック データベースを追加または削除し、プールの全体的なコストに影響を与えずにエラスティック データベースのパフォーマンスを定義できます。 つまり、個々のデータベースの使用状況の管理について心配する必要はありません。

[ドキュメントを参照する](sql-database-elastic-pool.md)

## <a name="intelligent-management-of-your-environment"></a>環境のインテリジェントな管理
組み込みのサイズ設定の推奨は、事前にプールからメリットを得られるデータベースを識別します。 これらの推奨は、"what-if" 分析を行ってパフォーマンス目標を満たすようにすばやく最適化します。 充実したパフォーマンス監視およびトラブルシューティング ダッシュ ボードは、プール使用率履歴の視覚化に役立ちます。

[ドキュメントを参照する](sql-database-elastic-pool-guidance.md)

## <a name="performance-and-price-to-meet-your-needs"></a>ニーズを満たすパフォーマンスと価格
Basic、Standard、Premium プールは、さまざまなパフォーマンス、ストレージ、価格のオプションを提供します。 プールは、最大 400 個のエラスティック データベースを含むことができます。 エラスティック データベースは、最大 1000 エラスティック データベース トランザクション ユニット (eDTU) まで自動的にスケール アップできます。

[ドキュメントを参照する](https://azure.microsoft.com/pricing/details/sql-database/?b=16.50)

## <a name="elastic-tools"></a>エラスティック ツール
エラスティック プールだけでなく、複数のデータベースにまたがる運用アクティビティの管理に役立つ SQL Database の機能があります。

**複数のデータベースにまたがるクエリとレポートを実行する**  
[エラスティック データベース クエリ](sql-database-elastic-query-overview.md) を使用すると、エラスティック プール内の複数のデータベースに対するクエリとレポートを実行し、プールの多くのデータベースに格納されているデータに同時にリモート アクセスできます。

**複数のデータベースにまたがるトランザクションを実行する**  
[エラスティック データベース トランザクション](sql-database-elastic-transactions-overview.md) を使用すると、SQL Database の複数のデータベースにまたがるトランザクションと操作を実行できます (つまり、複数のデータベースで金融トランザクションを処理するとき、または&1; つのデータベースと注文で在庫を更新するとき)。

**複数のデータベースに対して同じ操作を実行する**  
[エラスティック データベース ジョブ](sql-database-elastic-jobs-overview.md) は、エラスティック プール内の各データベースに対してインデックスの再構築やスキーマの更新などの管理操作を実行します。

SQL Database の他の機能についてはホーム ページを
[ご覧ください](https://azure.microsoft.com/services/sql-database/)。 

## <a name="next-steps"></a>次のステップ
[無料の Azure サブスクリプション](https://azure.microsoft.com/get-started/)を入手し、[最初の Azure SQL Database を作成](sql-database-get-started.md)します。

## <a name="additional-resources"></a>その他のリソース
[SQL Database の機能](https://azure.microsoft.com/services/sql-database/)すべてを確認します。

[SQL Database の技術概要](sql-database-technical-overview.md)を確認します。  




<!--HONumber=Jan17_HO2-->


