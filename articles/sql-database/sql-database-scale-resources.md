---
title: Azure SQL Database のリソースのスケーリング | Microsoft Docs
description: この記事では、リソースの割り当てを追加または削除して、データベースをスケーリングする方法について説明します。
services: sql-database
author: jovanpop-msft
ms.reviewer: carlrab
ms.service: sql-database
ms.topic: conceptual
ms.date: 07/16/2018
ms.author: jovanpop
manager: craigg
ms.openlocfilehash: a6b987d9815cfabed6dd986a0d9842a97f5b5868
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39092054"
---
# <a name="scale-database-resources"></a>データベース リソースをスケーリングする

Azure SQL Database では、最小限のダウンタイムでデータベースにリソースを動的に追加することができます。

## <a name="overview"></a>概要

少数のデバイスや顧客から始まったアプリの需要が何百万という数にまで膨れ上がると、Azure SQL Database は、最小限のダウンタイムですぐにスケーリングします。 スケーラビリティは、必要なときにサービスにリソースを動的に追加することができる、PaaS の最も重要な特性の 1 つです。 Azure SQL Database では、データベースに割り当てられたリソース (CPU パワー、メモリ、IO スループット、ストレージ) を簡単に変更することができます。  
インデックス付けやクエリ書き換えの方法では修正できない、アプリケーション使用量増加によるパフォーマンスの問題を軽減することができます。 リソースを追加すると、データベースが現在のリソース制限に達し、受信ワークロードの処理にいっそうのパワーが必要なときに、迅速に対応できます。 Azure SQL Database では、必要のないリソースをスケールダウンしてコストを抑えることもできます。
ハードウェアの購入や、基になるインフラストラクチャの変更について、心配する必要はありません。 データベースのスケーリングは、Azure portal でスライダーを使って簡単に実行できます。

![データベースのパフォーマンスをスケーリングする](media/sql-database-scalability/scale-performance.svg)

Azure SQL Database には、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)があります。 
-   [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)には、データベースの軽量ワークロードから重量ワークロードまでをサポートする、計算リソース、メモリ リソース、および IO リソースの組み合わせがそれぞれ異なる、Basic、Standard、Premium の 3 つのサービス レベルがあります。 各レベルにおけるパフォーマンス レベルでは、これらのリソースのさまざまな組み合わせが提供され、ストレージ リソースを追加することができます。
-   [仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)では、仮想コアの数、メモリの量、およびストレージの量と速度を選択できます。
最初にアプリを月数ドルの小さな 1 つのデータベースでビルドし、後でいつでもソリューションのニーズに合わせて手動またはプログラムでサービス レベルを変更することができます。 アプリにも顧客にもダウンタイムを発生させずにパフォーマンスを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができ、必要なときに必要な分のリソースにのみ課金されます。


> [!NOTE]
> 動的スケーラビリティは自動スケールとは異なります。 自動スケールは、基準に基づいてサービスが自動的にスケールされるのに対し、動的スケーラビリティでは、ダウンタイムなしで手動スケールすることができます。
>


単一の Azure SQL Database は、手動の動的スケーラビリティをサポートしますが、自動スケールはサポートしていません。 *自動*操作を増やすには、エラスティック プールの使用を検討してください。エラスティック プールを使用すると、データベースが個々のデータベースのニーズに基づいてプール内のリソースを共有できます。
ただし、単一の Azure SQL Database のスケーラビリティを自動化できるスクリプトがあります。 例については、「[PowerShell を使用して単一の SQL データベースを監視およびスケーリングする](scripts/sql-database-monitor-and-scale-database-powershell.md)」を参照してください。


Azure SQL Database の 3 種類すべてに、データベースを動的にスケーリングする何らかの機能があります。
-   [Azure SQL Single Database](sql-database-single-database-scale.md) では[DTU](sql-database-dtu-resource-limits-single-databases.md) モデルまたは[仮想コア](sql-database-vcore-resource-limits-single-databases.md) モデルを使って、各データベースに割り当てられる最大リソース量を定義できます。
-   [Azure SQL Managed Instance](sql-database-managed-instance.md) は[仮想コア](/azure/sql-database/sql-database-managed-instance#vcore-based-purchasing-model-preview) モードを使い、インスタンスに割り当てられる最大 CPU コア数と最大ストレージ量を定義できます。 インスタンス内のすべてのデータベースが、インスタンスに割り当てられたリソースを共有します。
-   [Azure SQL エラスティック プール](sql-database-elastic-pool-scale.md)を使用すると、プール内のデータベースのグループごとの最大リソース制限を定義できます。

## <a name="alternative-scale-methods"></a>別のスケーリング方法
リソースのスケーリングは、データベースまたはアプリケーション コードを変更することなく、データベースのパフォーマンスを向上させることができる、最も簡単で効果的な方法です。
場合によっては、最高のパフォーマンス レベルであっても、パフォーマンスを最適化するだけでは、コスト効果の高い方法でワークロードを正常に処理できない可能性があります。 そのような場合は、データベースをスケーリングするための他のオプションがあります。
-   [読み取りスケールアウト](sql-database-read-scale-out.md)は、データの 1 つの読み取り専用レプリカを取得している場合に利用できる機能であり、レポートなどの負荷が高い読み取り専用クエリを実行できます。 読み取り専用レプリカは、プライマリ データベースのリソース使用量に影響を与えずに、読み取り専用ワークロードを処理します。
-   [データベース シャーディング](sql-database-elastic-scale-introduction.md)は、データを複数のデータベースに分割してそれらを個別にスケーリングできる一連のテクニックです。

## <a name="next-steps"></a>次の手順
- データベース コードの変更によってデータベースのパフォーマンスを向上させる方法の詳細については、「[パフォーマンスに関する推奨事項の検索と適用](sql-database-advisor-portal.md)」をご覧ください。
- 組み込みのデータベース インテリジェンスを使ってデータベースを最適化する方法については、「[自動チューニング](sql-database-automatic-tuning.md)」をご覧ください。
- Azure SQL Database サービスでの読み取りスケールアウトについては、「[読み取り専用レプリカを使用して読み取り専用クエリ ワークロードを負荷分散する](sql-database-read-scale-out.md)」をご覧ください。
- データベース シャーディングの詳細については、「[Azure SQL Database によるスケール アウト](sql-database-elastic-scale-introduction.md)」をご覧ください。

