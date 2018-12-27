---
title: General Purpose レベル - Azure SQL Database サービス | Microsoft Docs
description: Azure SQL Database General Purpose レベルについて説明します
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: carlrab
manager: craigg
ms.date: 12/04/2018
ms.openlocfilehash: 8b708818584be6cdb84530ce008295ccee080d94
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52885850"
---
# <a name="general-purpose-tier---azure-sql-database"></a>General Purpose レベル - Azure SQL Database

> [!NOTE]
> General Purpose レベルは DTU 購入モデルで Standard と呼ばれています。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-service-tiers.md)に関する記事をご覧ください。

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に適合された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。
- 汎用 
- Business Critical
- ハイパースケール

General Purpose モデルは、計算とストレージの分離に基づいています。 このアーキテクチャ モデルは、データベース ファイルを透過的にレプリケートし、基盤となるインフラストラクチャの障害が発生した場合にデータ損失が発生しないことが保証されている Azure Premium Storage の高可用性と信頼性に依存しています。

次の図は、計算レイヤーとストレージ レイヤーが分離されている Standard アーキテクチャ モデルの 4 ノードを示しています。

![計算とストレージの分離](media/sql-database-managed-instance/general-purpose-service-tier.png)

General Purpose モデルには、次の 2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlserver.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみが含まれています (プラン キャッシュ、バッファー プール、列のストア プールなど)。 このステートレス SQL Server ノードは、プロセスの初期化、ノードの正常性の制御、および他の場所へのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) が Azure Premium Storage に保存されています。 Azure Storage では、データベース ファイル内にあるレコードのデータが消失しないことが保証されています。 Azure Storage には、データの可用性と冗長性が組み込まれています。そのため、たとえ SQL Server プロセスがクラッシュしても、ログ ファイルのレコードやデータ ファイルのページはすべて維持されます。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、基となるインフラストラクチャの一部で障害が発生した場合、または SQL Server プロセスで重大な問題が検出された場合、Azure Service Fabric は、ステートレス SQL Server プロセスを別のステートレス計算ノードに移行します。 フェールオーバー時間を最小限に抑えるために、プライマリ ノードのフェールオーバーが発生した場合に新しい計算サービスの実行を待機している一連のスペア ノードがあります。 Azure Storage レイヤーのデータは影響を受けず、データ/ログ ファイルは、新しく初期化された SQL Server プロセスにアタッチされます。 このプロセスは、99.99% の可用性を保証していますが、移行時間や、新しい SQL Server ノードの起動にコールド キャッシュを使用することが原因で、実行中の大きなワークロードに対しては、パフォーマンス上の影響が若干生じる場合があります。

## <a name="when-to-choose-this-service-tier"></a>このサービス レベルを選択する場合

General Purpose サービス レベルは、ほとんどの一般的なワークロード向けに設計されている Azure SQL Database の既定のサービス レベルです。 ほとんどのケースで、Azure SQL IaaS に匹敵する、ストレージ待機時間が 5 から 10 ミリ秒で SLA が 99.99% のフル マネージド データベース エンジンが必要な場合は、General Purpose レベルをお勧めします。

## <a name="next-steps"></a>次の手順

- [Business Critical](sql-database-service-tier-business-critical.md) レベルと [Hyperscale](sql-database-service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
