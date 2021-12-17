---
title: 概要 - Azure Database for MySQL - フレキシブル サーバー
description: MySQL Community Edition をベースとした、Microsoft クラウドのリレーショナル データベース サービスである Azure Database for MySQL フレキシブル サーバーについて説明します。
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, references_regions
ms.topic: overview
ms.date: 08/10/2021
ms.openlocfilehash: 13d220dc0b168ade0bd6493025d858e01772a980
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893957"
---
# <a name="azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL - フレキシブル サーバー 

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


MySQL コミュニティ エディションを搭載した Azure Database for MySQL は、次の 2 つのデプロイ モードで利用できます。

- フレキシブル サーバー
- シングル サーバー

この記事では、フレキシブル サーバー デプロイ モデルの概要を示し、主要概念について概説します。 ワークロードに適したデプロイ オプションを決定する方法については、「[Azure で適切な MySQL サーバー オプションを選択する](./../select-right-deployment-type.md)」をご覧ください。

## <a name="overview"></a>概要

Azure Database for MySQL フレキシブル サーバーは、データベース管理機能と構成設定のよりきめ細かな制御と柔軟性を提供するように設計された、運用環境対応のフル マネージド データベース サービスです。 フレキシブル サーバー アーキテクチャにより、ユーザーは単一の可用性ゾーン内および複数の可用性ゾーンにまたがる高可用性を選択できます。 また、フレキシブル サーバーでは、より優れたコスト最適化制御によって、サーバーを停止/起動する機能や、完全なコンピューティング能力を継続的には必要としないワークロードに最適な、バースト可能なコンピューティング層を実現できます。 フレキシブル サーバーでは、予約インスタンスもサポートされ、最大 63% のコストを削減できます。コンピューティング容量要件が予測できる運用環境ワークロードに最適です。 サービスでは、MySQL 5.7 と 8.0 のコミュニティ バージョンがサポートされています。 このサービスは現時点で一般提供されており、さまざまな [Azure リージョン](overview.md#azure-regions)で利用できます。

フレキシブル サーバー デプロイ オプションには、バースト可能、汎用目的、メモリ最適化という 3 つのコンピューティング レベルが用意されています。 各レベルには、データベース ワークロードをサポートする異なるコンピューティング容量とメモリ容量が用意されています。 バースト可能レベルで月あたり数ドル払い、最初のアプリを構築し、後から実際のソリューションのニーズに応じて、スケールを調整することができます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 必要なときに必要な分のリソースにのみ課金されます。 詳細については、[コンピューティングとストレージ](concepts-compute-storage.md)に関するページを参照してください。

フレキシブル サーバーは、以下に適しています
- バックアップ、高可用性、セキュリティ、監視などの機能の簡単なデプロイ、シンプルなスケーリング、データベース管理の安い経費
- 制御とカスタマイズに優れた、MySQL のコミュニティ バージョンを必要とするアプリケーション開発
- 同じゾーン、ゾーン冗長による高可用性、メンテナンス期間の管理機能のある運用環境ワークロード
- シンプルな開発体験 
- エンタープライズ グレードのセキュリティ、コンプライアンス、プライバシー

フレキシブル サーバーの最新情報については、[Azure Database for MySQL - フレキシブル サーバーの新機能](whats-new.md)に関するページを参照してください。

![フレキシブル サーバーの概念図](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="free-12-month-offer"></a>12 か月間の無料プラン

[Azure 無料アカウント](https://azure.microsoft.com/free/)を使用すると、フレキシブル サーバーを 12 か月無料で使用できます。1 か月あたりの上限は次のとおりです。
* **Burstable B1MS インスタンスを 750 時間**。これは、データベース インスタンスを毎月十分に継続実行できるだけの時間です。
* **32 GB** のストレージと **32 GB** のバックアップ ストレージ。 

このプランを利用して、Azure Database for MySQL - フレキシブル サーバーを使用するアプリケーションを開発し、デプロイできます。 Azure 無料アカウントを使用して、フレキシブル サーバーを無料で作成および使用する方法について詳しくは、[こちらのチュートリアル](how-to-deploy-on-azure-free-account.md)を参照してください。 

## <a name="high-availability-within-and-across-availability-zones"></a>可用性ゾーン内および可用性ゾーン間での高可用性

Azure Database for MySQL フレキシブル サーバーでは、自動フェールオーバーによる高可用性を構成できます。 高可用性ソリューションは、障害によってコミットされたデータが失われることがないように、またアプリケーションの全体的な稼働時間が増加するように設計されています。高可用性が構成されている場合、フレキシブル サーバーでは、スタンバイ レプリカが自動的にプロビジョニングされ、管理されます。 高可用性アーキテクチャ モデルには、次の 2 つがあります。 

- **ゾーン冗長の高可用性 (HA):** このオプションは、複数の可用性ゾーンにわたってインフラストラクチャの完全な分離と冗長性を実現する場合に適しています。 最高レベルの可用性が提供されますが、複数のゾーンにわたってアプリケーションの冗長性を構成する必要があります。 ゾーン冗長 HA は、可用性ゾーン内のインフラストラクチャ障害に対して最高レベルの可用性を実現する必要があり、可用性ゾーン全体の待機時間を許容できる場合に推奨されます。 ゾーン冗長 HA は、リージョンが複数の Availability Zones をサポートし、ゾーン冗長 Premium ファイル共有を使用できる  [Azure リージョンのサブセット](overview.md#azure-regions) で使用できます。 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="ゾーン冗長 HA":::

- **同一ゾーンの高可用性 (HA):** このオプションは、プライマリ サーバーとスタンバイ サーバーの両方が同じ可用性ゾーンに含まれ、ネットワーク待機時間が短いインフラストラクチャの冗長性に適しています。 ゾーン間でアプリケーションの冗長性を構成せずに高可用性を実現します。 単一の可用性ゾーン内で、ネットワーク待機時間が最も短い最高レベルの可用性を実現する必要がある場合は、同一ゾーン HA が推奨されます。 同一ゾーン HA は、Azure Database for MySQL フレキシブル サーバーを作成できる[すべての  Azure リージョン](overview.md#azure-regions)で使用できます。 

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="同じ冗長高可用性":::

詳細については、[高可用性の概念](concepts-high-availability.md)に関するページを参照してください。

## <a name="automated-patching-with-managed-maintenance-window"></a>マネージド メンテナンス期間によるパッチの自動適用

このサービスでは、基になるハードウェア、OS、およびデータベース エンジンの自動修正が実行されます。 パッチには、セキュリティとソフトウェアの更新プログラムが含まれています。 MySQL エンジンの場合、マイナー バージョンのアップグレードも、計画メンテナンス リリースの一部として含まれています。 ユーザーは、パッチ適用のスケジュールをシステム管理として構成することも、カスタム スケジュールを定義することもできます。 メンテナンス スケジュールの間に、パッチが適用され、パッチ適用プロセスの一環として更新を完了するためにサーバーの再起動が必要になる場合があります。 カスタム スケジュールを使用すると、ユーザーはパッチ適用のサイクルを予測可能にし、ビジネスへの影響が最小限のメンテナンス期間を選択できます。 一般に、サービスは、継続的インテグレーションとリリースの一環として、毎月のリリース スケジュールに従います。

詳細については、[予定メンテナンス](concepts-maintenance.md)に関するページを参照してください。 

## <a name="automatic-backups"></a>自動バックアップ

フレキシブル サーバー サービスにより、サーバーのバックアップが自動的に作成され、ユーザーが構成したローカル冗長または geo 冗長のストレージにそれが保存されます。 バックアップを使用すると、サーバーを、バックアップのリテンション期間内の任意の時点に復元できます。 バックアップの既定のリテンション期間は 7 日です。 必要に応じて、リテンション期間を 1 日から 35 日までの範囲で構成できます。 すべてのバックアップが、AES 256 ビット暗号化を使用して暗号化されます。

詳細については、[バックアップの概念](concepts-backup-restore.md)に関する記事を参照してください。

## <a name="network-isolation"></a>ネットワーク分離

Azure Database for MySQL フレキシブル サーバーに接続するには、2 つのネットワーク オプションがあります。 **プライベート アクセス (VNet 統合)** オプションと **パブリック アクセス (許可された IP アドレス)** オプションです。 

- **プライベート アクセス (VNet 統合)** – お使いの [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md) 内にフレキシブル サーバーをデプロイできます。 Azure の仮想ネットワークでは、非公開の、セキュリティで保護されたネットワーク通信が提供されます。 仮想ネットワーク内のリソースでは、プライベート IP アドレスを通した通信が可能です。

  以下の機能が必要な場合は、VNet 統合オプションを選択します。

  - プライベート IP アドレスを使用して、同じ仮想ネットワーク内の Azure リソースからフレキシブル サーバーに接続する
  - VPN または ExpressRoute を使用して Azure 以外のリソースからフレキシブル サーバーに接続する
  - パブリック エンドポイントがない

- **パブリック アクセス (許可された IP アドレス)** – パブリック エンドポイントを使用してフレキシブル サーバーをデプロイできます。 パブリック エンドポイントは、パブリックに解決できる DNS アドレスです。 "許可されている IP アドレス" という語句は、サーバーへのアクセス許可を付与することが選択された IP の範囲を意味します。 これらのアクセス許可は、**ファイアウォール規則** と呼ばれます。

詳細については、[ネットワークの概念](concepts-networking.md)に関する記事を参照してください。

## <a name="adjust-performance-and-scale-within-seconds"></a>数秒以内でのパフォーマンスの調整とスケール

フレキシブル サーバー サービスは、次の 3 つの SKU レベルで使用できます: Burstable、General Purpose、Memory Optimized。 Burstable レベルは、完全なコンピューティング能力を継続的には必要としない低コストの開発およびコンカレンシーの低いワークロードに最適です。 General Purpose および Memory Optimized は、高いコンカレンシー、スケール、予測可能なパフォーマンスを必要とする運用ワークロードに適しています。 最初は月数ドルの小規模データベースでアプリを構築し、後から実際のソリューションのニーズに応じて、スケールをシームレスに調整することができます。 ストレージのスケーリングはオンラインであり、ストレージの自動拡張がサポートされています。 フレキシブル サーバーを使用すると、ストレージに関係なく、無償の IOPS 制限を超えて最大 20K IOPS まで追加の IOPS をプロビジョニングできます。 この機能を使用すると、ワークロードの要件に基づいてプロビジョニングされる IOPS の数をいつでも増減できます。 動的なスケーラビリティにより、データベースは変化の激しいリソース要件に透過的に対処することができます。 消費したリソースについてだけ支払います。 

詳細については、[コンピューティングとストレージの概念](concepts-compute-storage.md)に関する記事を参照してください。

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>最大 10 個の読み取りレプリカを使用して、読み取りワークロードをスケールアウトする

MySQL は、インターネット規模の Web およびモバイル アプリケーションを実行するための一般的なデータベース エンジンの 1 つです。 多くのお客様は、オンライン教育サービス、ビデオ ストリーミング サービス、デジタル支払いソリューション、eコマース プラットフォーム、ゲーム サービス、ニュース ポータル、政府機関、医療機関の Web サイトなどにそれを使用しています。 これらのサービスは、Web またはモバイル アプリケーションでのトラフィックの増加に合わせて、サービスを提供し、スケーリングする必要があります。

アプリケーション側に関しては、通常、アプリケーションは Java または PHP で開発され、 [Azure 仮想マシン スケール セット](../../virtual-machine-scale-sets/overview.md) や  [Azure App Services](../../app-service/overview.md)  で実行できるように移行されたり、 [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md) で実行できるようにコンテナー化されたりします。 仮想マシン スケール セット、App Service、または AKS が基盤のインフラストラクチャである場合、アプリケーションのスケーリングは、新しい VM が即座にプロビジョニングされ、要求に対応するためにアプリケーションのステートレス コンポーネントがレプリケートされることによって簡単に行われますが、多くの場合、データベースが集中的なステートフル コンポーネントとしてボトルネックになります。

読み取りレプリカ機能を使用すると、Azure Database for MySQL フレキシブル サーバーから読み取り専用サーバーに、データをレプリケートできます。 ソース サーバーから **最大で 10 個のレプリカ** にレプリケートできます。 レプリカは、MySQL エンジンのネイティブな[バイナリ ログ (binlog) ファイルの位置ベースのレプリケーション テクノロジ](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)を使用して、非同期で更新されます。 [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) などのロード バランサー プロキシ ソリューションを使用して、アプリケーションのリファクタリング コストを発生させることなく、アプリケーションのワークロードを読み取りレプリカにシームレスにスケールアウトすることができます。 

詳細については、[読み取りレプリカの概念](concepts-read-replicas.md)に関する記事を参照してください。

## <a name="setup-hybrid-or-multi-cloud-data-synchronization-with-data-in-replication"></a>データイン レプリケーションを使用したハイブリッド データ同期または複数のクラウドのデータ同期をセットアップする

データイン レプリケーションでは、外部の MySQL サーバーから Azure Database for MySQL フレキシブル サービスにデータを同期できます。 外部サーバーとして、オンプレミス、仮想マシン内、Azure Database for MySQL Single Server、または他のクラウド プロバイダーによってホストされるデータベース サービスを使用できます。 データイン レプリケーションは、バイナリ ログ (binlog) ファイルの位置ベースに基づいています。 データイン レプリケーションの使用を検討する主なシナリオは次のとおりです。
* ハイブリッド データ同期
* 複数のクラウドの同期
* [最小限のダウンタイムでのフレキシブル サーバーへの移行](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

詳細については、[データイン レプリケーションの概念](concepts-data-in-replication.md)に関する記事を参照してください。


## <a name="stopstart-server-to-optimize-cost"></a>サーバーを停止および開始してコストを最適化する

フレキシブル サーバー サービスを使用すると、サーバーをオンデマンドで停止および開始して、コストを最適化することができます。 コンピューティング層の課金は、サーバーが停止すると直ちに停止されます。 これにより、開発、テスト、期限付きの予測可能な運用ワークロードにおいて、大幅なコスト削減を実現できます。 サーバーは、すぐに再起動しない限り 30 日間は停止状態のままになります。

詳細については、[サーバーの概念](concept-servers.md)に関する記事を参照してください。

## <a name="enterprise-grade-security-compliance-and-privacy"></a>エンタープライズ グレードのセキュリティ、コンプライアンス、プライバシー

フレキシブル サーバー サービスでは、保存データのストレージ暗号化に FIPS 140-2 認証済みの暗号モジュールが使用されます。 データ (バックアップを含む) と、クエリの実行中に作成される一時ファイルは暗号化されます。 このサービスでは、Azure ストレージ暗号化に含まれる AES 256 ビット暗号が使用され、キーはシステムによって管理されます (既定)。

サービスでは、既定で適用されるトランスポート層セキュリティを使用して、動作中のデータが暗号化されます。 フレキシブル サーバーは、トランスポート層セキュリティ (TLS 1.2) を使用した暗号化接続を既定でサポートしており、TLS 1.0 と TLS 1.1 を使用した受信接続はすべて拒否されます。 require_secure_transport サーバー パラメーターを設定することで、SSL 強制を無効にすることができ、また、サーバーの最小 tls_version を設定できます。

詳細については、[フレキシブル サーバーへの暗号化された接続の使用方法](how-to-connect-tls-ssl.md)に関する記事を参照してください。

フレキシブル サーバーを使用すると、[Azure 仮想ネットワーク](../../virtual-network/virtual-networks-overview.md) (VNet 統合) を使用してサーバーに完全にプライベートでアクセスできます。 Azure Virtual Network 内のサーバーには、プライベート IP アドレスを介してのみアクセスおよび接続できます。 VNet 統合では、パブリック アクセスが拒否され、パブリック エンドポイントを使用してサーバーに到達できません。

詳細については、[ネットワークの概念](concepts-networking.md)に関する記事をご覧ください。

## <a name="monitoring-and-alerting"></a>監視とアラート

フレキシブル サーバー サービスには、組み込みのパフォーマンス監視機能とアラート機能が搭載されています。 すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 サービスにより、リソース使用率を監視し低速クエリ ログを構成できるようにするホスト サーバー メトリックが公開されます。 これらのツールを使用すると、ワークロードをすばやく最適化し、最適なパフォーマンスが得られるようにサーバーを構成することができます。 Azure Database for MySQL フレキシブル サーバーでは、Azure Monitor ブックを使用し、遅いクエリと監査ログ データを視覚化できます。 ブックを使用すると、データを分析し、Azure portal 内に豊富な視覚レポートを作成するための柔軟なキャンバスが得られます。 Azure Database for MySQL フレキシブル サーバーでは、Server Overview、[Auditing](tutorial-configure-audit.md)、[Query Performance Insights](tutorial-query-performance-insights.md) という 3 つのブック テンプレートを面倒な設定なしで利用できます。 [Query Performance Insight](tutorial-query-performance-insights.md) ブックは、次のような情報を提供することで、データベースのパフォーマンスのトラブルシューティングに費やす時間を短縮できるように設計されています。

* 実行時間の長いクエリの上位 N 個とその傾向。
* クエリの詳細: クエリ テキストと、クエリ時間の最小、最大、平均、および標準偏差を含む実行履歴の表示。
* リソース使用率 (CPU、メモリ、ストレージ)。

さらに、コミュニティ監視ツール ([MySQL フレキシブル サーバーでの Percona Monitoring and Management](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/monitor-azure-database-for-mysql-using-percona-monitoring-and/ba-p/2568545) など) を使用でき、これらのツールと統合することができます。 

詳細については、[監視の概念](concepts-monitoring.md)に関する記事を参照してください。

## <a name="migration"></a>移行

このサービスでは、MySQL のコミュニティ バージョンが実行されます。 これにより、アプリケーションの完全な互換性が確保され、MySQL エンジン上で開発された既存のアプリケーションをフレキシブル サーバーに移行するために必要なリファクタリング コストが最小限に抑えられます。 フレキシブル サーバーへの移行は、次のオプションを使用して実行できます。

### <a name="offline-migrations"></a>オフライン移行
*   ソースと Azure の間のネットワーク帯域幅が適切な場合 (高速 ExpressRoute など) は、Azure Data Migration Service を使用します。 詳細については、「[DMS を使用して MySQL を Azure Database for MySQL にオフラインで移行する - Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-offline-portal.md)」のステップ バイ ステップの手順を参照してください
*   mydumper/myloader を使用して圧縮設定を利用し、低速ネットワーク (パブリック インターネットなど) 経由で効率的にデータを移動します。 詳細については、[mydumper/myloader を使用した Azure Database for MySQL への大規模なデータベースの移行](../../mysql/concepts-migrate-mydumper-myloader.md)に関する記事のステップ バイ ステップの手順を参照してください

### <a name="online-or-minimal-downtime-migrations"></a>オンラインの移行または最小限のダウンタイムの移行
初期シード処理では、mydumper/myloader の整合性バックアップ/復元でデータイン レプリケーションを使用します。 詳細については、[チュートリアル: 最小限のダウンタイムでの Azure Database for MySQL - シングル サーバーから Azure Database for MySQL – フレキシブル サーバーへの移行](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)に関する記事のステップ バイ ステップの手順を参照してください

5 つの簡単な手順で Azure Database for MySQL - 単一サーバーからフレキシブル サーバーに移行するには、[こちらのブログ](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057)を参照してください。

詳細については、「[Azure Database for MySQL への移行に適切なツールを選択する](../../mysql/how-to-decide-on-right-migration-tools.md)」を参照してください。

## <a name="azure-regions"></a>Azure Azure リージョン

Azure でワークロードを実行する利点の 1 つは、グローバルに展開できることです。 Azure Database for MySQL フレキシブル サーバーは、現在、次の Azure リージョンで提供されています。

| Region | 可用性 | 同一ゾーン HA | ゾーン冗長 HA |
| --- | --- | --- | --- |
| オーストラリア東部 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| オーストラリア南東部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Brazil South | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| カナダ中部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| カナダ東部 | :heavy_check_mark: | :x: | :x: |
| インド中部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 米国中部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 東アジア (香港) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 米国東部 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 米国東部 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| フランス中部 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ドイツ中西部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Japan East | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 西日本 | :heavy_check_mark: | :x: | :x: |
| 韓国中部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 韓国南部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 米国中北部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 北ヨーロッパ | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 米国中北部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| ノルウェー東部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 南アフリカ北部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 米国中南部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Southeast Asia | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| スイス北部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| アラブ首長国連邦北部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 英国南部 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 英国西部 | :heavy_check_mark: | :x: | :x: |
| 米国中西部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 西ヨーロッパ | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 米国西部 | :heavy_check_mark: | :heavy_check_mark: | :x: |
| 米国西部 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| 米国西部 3 | :heavy_check_mark: | :heavy_check_mark: | :x: |

## <a name="contacts"></a>連絡先

Azure Database for MySQL フレキシブル サーバーについてのご質問やご提案については、Azure Database for MySQL チームまでメール ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)) でお送りください。 このメール アドレスはテクニカル サポートのエイリアスではありません。

さらに、適切な連絡先について次の点を考慮してください。

- Azure サポートに問い合わせる場合は、[Azure portal からチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。
- アカウントを使用して問題を修正するには、Azure Portal で[サポート要求](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を提出します。
- フィードバックを提供したり、新しい機能を要求したりするには、<bpt id="p1">[</bpt>UserVoice<ept id="p1">](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)</ept> でエントリを作成します。

## <a name="next-steps"></a>次のステップ

Azure Database for MySQL - シングル サーバー デプロイ モードの概要を確認したので、以下のことを行う準備ができました。

- 最初のサーバーを作成する。
  - [Azure portal を使用して Azure Database for MySQL フレキシブル サーバーを作成する](quickstart-create-server-portal.md)
  - [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーを作成する](quickstart-create-server-cli.md)
  - [Azure CLI を使用して Azure Database for MySQL フレキシブル サーバーを管理する](how-to-manage-server-portal.md)

- 次の任意の言語を使って最初のアプリを作成します。
  - [Python](connect-python.md)
  - [PHP](connect-php.md)
