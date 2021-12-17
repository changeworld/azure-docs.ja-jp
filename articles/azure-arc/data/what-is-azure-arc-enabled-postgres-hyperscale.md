---
title: Azure Arc 対応 PostgreSQL Hyperscale とは
description: Azure Arc 対応 PostgreSQL Hyperscale とは
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 39856b916e93e928c754add5ed25cf5192fba04f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132298249"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対応 PostgreSQL Hyperscale とは

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 対 Azure Arc 対応データ サービス対 Azure Arc 対応 PostgreSQL Hyperscale とは何ですか。

**Azure Arc** は、Azure ハイブリッド ファミリである Azure Arc、Azure Stack、Azure IoT の柱の 1 つです。 Azure Arc は、お客様のエクスペリエンスを簡素化することで、ハイブリッド デプロイの複雑さに対応するのに役立ちます。 Azure Stack では、マイクロソフトまたはパートナーは、ハードウェアとソフトウェア (アプライアンス) を提供します。 Azure Arc では、マイクロソフトはソフトウェアのみを提供します。 お客様またはパートナーは、サポート インフラストラクチャを提供し、ソリューションを運用します。 Azure Arc は、Azure Stack でサポートされます。 Azure Arc を使用すると、Azure データ センターの外部に存在するインフラストラクチャで Azure サービスを実行し、そうしたい場合は他の Azure マネージド サービスと統合することができます。

**Azure Arc 対応データ サービス** は、Azure Arc の一部です。これは、お客様がデータを管理できるようにする製品とサービスのスイートです。 お客様は次の操作を行えるようになります。

- 任意の物理インフラストラクチャで Azure データ サービスを実行する
- あらゆる場所で同じクラウド テクノロジを使用して運用を最適化する
- アプリケーションまたはデータベースがホストされている場所 (Azure PaaS または Azure Arc) に関係なく、同じ基になるテクノロジを使用してアプリケーション開発を最適化します
- 独自のデータ センターでクラウド テクノロジを使用し、規制要件 (データ所在地と顧客制御) を満たします。 言い換えると、"クラウドに行けない場合は、クラウドが来ます。"

Azure Arc 対応データ サービスで提供される値の一部を次に示します。
- 常に最新
- エラスティック スケール
- セルフサービス プロビジョニング
- 統合された管理
- クラウドの課金
- 接続された (Azure に対する) シナリオと、時々接続される (Azure に対する) シナリオのサポート。 (直接対間接接続モード)

**Azure Arc 対応 PostgreSQL Hyperscale** は、Azure Arc 対応データ サービスの一部として使用できるデータベース エンジンの 1 つです。 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Azure で Microsoft が提供する Postgres ソリューションを比較する

Microsoft は、次の 2 つの方法で Azure で Postgres データベース サービスを提供しています。
- Azure PaaS のマネージド サービスとして (サービスとしてのプラットフォーム)
- お客様またはパートナーまたはベンダーによって運用される Azure Arc を使用した半管理型サービスとして

### <a name="in-azure-paas"></a>Azure PaaS 内
**[Azure PaaS](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)** では、Microsoft は Postgres のマネージド サービスとしていくつかのデプロイ オプションを提供しています。

:::row:::
    :::column:::
        Azure Database for Postgres 単一サーバーと Azure Database for Postgres フレキシブル サーバー。 これらのサービスは、Microsoft が管理する単一ノード/単一インスタンスの Postgres フォーム ファクターです。 Azure Database for Postgres フレキシブル サーバーは、このサービスの最新の進化版です。
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Azure Database for PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Azure Database for PostgreSQL Hyperscale (Citus)。 このサービスは、Microsoft マネージド マルチノード/マルチインスタンス Postgres フォーム ファクターです。 これは、単一ノード Postgres を分散データベース システムに変換する Postgres への Citus 拡張機能を利用しています。 スケールアウトすると、ワークロードがこれまでにないレベルのスケールとパフォーマンスに到達できる可能性のあるデータとクエリが分散されます。 アプリケーションには、サーバー グループとも呼ばれる単一の Postgres インスタンスが表示されます。 ただし、このサーバー グループは、一緒に動作する複数の Postgres インスタンスで構成されています。 スケールアウトすると、サーバー グループ内の Postgres インスタンスの数が増え、ワークロードのパフォーマンスとスケーラビリティが向上する可能性があります。 必要性とワークロードの特性に応じて、サーバー グループに追加する Postgres インスタンスの数を決定します。 
        詳細については、次のビデオをご覧ください。
        - [デモ - Postgres Hyperscale (Citus) を使用したハイ パフォーマンスの HTAP:](https://youtu.be/W_3e07nGFxY )
        - [デモ - Python と Postgres on Azure を使用した HTAP アプリケーションの構築:](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Azure Database for PostgreSQL Hyperscale (Citus)":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>Azure Arc の場合

:::row:::
    :::column:::
        **Azure Arc** によって、Microsoft では、**単一の** Postgres 製品またはサービスである **Azure Arc 対応 PostgreSQL Hyperscale** を提供しています。 Azure Arc によって、次の機能を備えた **1 つの Postgres 製品** を提供することで、Azure PaaS に比べ、Postgres の製品定義とカスタマー エクスペリエンスが簡素化されるようになります。
        - Azure Database for Postgres 単一サーバーまたはフレキシブル サーバーなどの単一ノードまたは単一インスタンス Postgres のデプロイ
        - Azure Database for PostgreSQL Hyperscale などの複数ノードまたはマルチインスタンス Postgres をデプロイする (Citus)
        - お客様が Postgres のデプロイを 1 ノードから Postgres の複数ノードにモーフィングし、その逆のモーフィングも必要な場合はそれも可能にすることで、優れた柔軟性を実現できます。 データ移行を行うことなく、簡単なエクスペリエンスでこれを実行できます。
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Azure Arc 対応 PostgreSQL Hyperscale":::
    :::column-end:::
:::row-end:::

Azure PaaS の兄弟と同様に、Postgres はマルチノードまたはマルチインスタンス形式で、単一ノード Postgres を分散データベース システムに変換する Citus 拡張機能を利用します。 スケール アウトすると、データとクエリが分散され、ワークロードが前例のないレベルのスケールとパフォーマンスに到達する可能性があります。 アプリケーションには、サーバー グループとも呼ばれる単一の Postgres インスタンスが表示されます。 ただし、このサーバー グループは、一緒に動作する複数の Postgres インスタンスで構成されています。 スケール アウトすると、サーバー グループ内の Postgres インスタンスの数が増え、ワークロードのパフォーマンスとスケーラビリティが向上する可能性があります。 必要性とワークロードの特性に応じて、サーバー グループに追加する Postgres インスタンスの数を決定します。 そうする必要があれば、サーバー グループ内の Postgres インスタンスの数を 1 に減らします。  Azure Arc では、マイクロソフトによって提供される Postgres のデプロイはすべて、Hyperscale の準備ができています。


Azure Arc 対応データ サービスによって提供される直接接続モードでは、Azure Arc 対応 PostgreSQL Hyperscale を Azure portal からデプロイできます。 間接接続モードを使用する場合は、それをホストするインフラストラクチャから、Azure Arc 対応 PostgreSQL Hyperscale をデプロイします。

**Azure Arc 対応 PostgreSQL Hyperscale を使用すると、次のことができます。**
- Postgres を簡単に管理する
    - 1 つのコマンドを使用して Postgres インスタンスをプロビジョニングする、またはプロビジョニング解除する
    - すべてのスケール: スケール アップ、ダウン、アウト、イン 
- 監視、フェールオーバー、バックアップ、修正プログラムの適用またはアップグレード、アクセス制御などを簡素化する
- 前例のない規模とパフォーマンスで Postgres アプリを構築する
    - 複数の Postgres インスタンス間でコンピューティングを水平方向にスケール アウトする
    - データとクエリを分散する
    - Citus 拡張機能を実行する
    - 標準の PostgreSQL を分散データベース システムに変換する
- 任意のインフラストラクチャに Postgres をデプロイする
    - オンプレミス、マルチクラウド (AWS、GCP、Azure)、エッジ
- Azure と統合する (省略可能)
- 従量課金制 (使用量分の課金単位)
- Postgres で Microsoft からサポートを受け取る

**その他の注意点:**
- Azure Arc 対応 PostgreSQL Hyperscale は新しいデータベース エンジンではなく、既存のデータベース エンジンの特定のバージョンでもありません。 これは、Azure PaaS で実行されるのと同じデータベース エンジンです。 Azure Arc では、Microsoft クラウドにアクセスできない場合は、Microsoft クラウドが提供されることを忘れないでください。 Azure Arc のイノベーションは、Microsoft がこのデータベース エンジンを提供する方法と、このデータベース エンジンに関して Microsoft が提供するエクスペリエンスに存在します。 

- Azure Arc 対応 PostgreSQL Hyperscale はデータ レプリケーション ソリューションでもありません。 ビジネス データは Arc デプロイ内に残っています。 Azure クラウドにはレプリケートされません。 データ レプリケーションや読み取りレプリカなど、データベース エンジンの機能を設定することを選択しない限り、同様です。 その場合、データは Postgres デプロイの外部でレプリケートされる可能性があります。Azure Arc のためではなく、データ レプリケーション機能を設定したためです。

- ワークロードを Azure Arc 対応 PostgreSQL Hyperscale に対して実行するために、特定のドライバーまたはプロバイダーを使用する必要はありません。 すべての "Postgres アプリケーション" は、Azure Arc 対応 PostgreSQL Hyperscale に対して実行できる必要があります。

- スケールアウト操作とスケールイン操作は、自動ではありません。 ユーザーによって制御されます。 ユーザーは、これらの操作をスクリプト化し、それらのスクリプトの実行を自動化できます。 すべてのワークロードがスケール アウトの恩恵を受けるわけではありません。このトピックの詳細については、「次のステップ」セクションで提案されている内容をお読みください。

**これらの機能の詳細については、この Data Exposed エピソードを参照することもできます。**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>ロールと責任: Azure マネージド サービス (サービスとしてのプラットフォーム (PaaS)) _対_ Azure Arc 対応データ サービス
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Azure PaaS 対 Azure Arc のロールと責任":::

## <a name="next-steps"></a>次のステップ
- **実際に使ってみてください。** Azure Kubernetes Service (AKS)、AWS Elastic Kubernetes Service (EKS)、Google Cloud Kubernetes Engine (GKE)、または Azure VM 上で [Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) をすぐに開始できます。 

- **デプロイし、独自に作成します。** 独自の Kubernetes クラスターを作成するには、こちらの手順に従います。 
   1. [クライアント ツールをインストールする](install-client-tools.md)
   2. [Azure Arc データ コントローラーを作成する](create-data-controller.md)
   3. [Azure Arc で Azure Database for PostgreSQL Hyperscale サーバー グループを作成する](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - Azure Arc 対応データ サービスは[ここ](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)と[ここ](overview.md)
   - [接続モードと要件](connectivity.md)



- **複数の PostgreSQL Hyperscale ノードにわたってデータを分散させ、パフォーマンス向上の可能性があるという利点を得るため、Azure Database for PostgreSQL Hyperscale の概念と使い方に関するガイドを参照してください。**
    * [ノードとテーブル](../../postgresql/concepts-hyperscale-nodes.md)
    * [アプリケーションの種類の決定](../../postgresql/concepts-hyperscale-app-type.md)
    * [ディストリビューション列の選択](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [テーブル コロケーション](../../postgresql/concepts-hyperscale-colocation.md)
    * [テーブルの配布と変更](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [マルチテナント データベースを設計する](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [リアルタイム分析ダッシュボードを設計する](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

