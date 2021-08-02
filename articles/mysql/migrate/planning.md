---
title: 'オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 計画'
description: Azure ランディング ゾーンは、クラウド移行プロジェクトの最終的な環境として定義されたターゲット環境です。
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 05/25/2021
ms.openlocfilehash: 1cd9d78fab41305fc6cf4fc814a39a999502c795
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958608"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-planning"></a>オンプレミスの MySQL から Azure Database for MySQL への移行ガイド: 計画

### <a name="landing-zone"></a>ランディング ゾーン

[Azure ランディング ゾーン](/azure/cloud-adoption-framework/ready/landing-zone/)は、クラウド移行プロジェクトの最終的な環境として定義されたターゲット環境です。 ほとんどのプロジェクトでは、ランディング ゾーンはその初期セットアップのために、ARM テンプレートを使用してスクリプト化される必要があります。 最終的には、PowerShell または Azure portal を使用してこれをカスタマイズし、ワークロードのニーズに合わせる必要があります。

WWI はサンフランシスコを拠点としているため、Azure ランディング ゾーンのすべてのリソースは `US West 2` リージョンに作成されました。 移行をサポートするために、次のリソースが作成されました。

  - [Azure Database for MySQL ](../quickstart-create-mysql-server-database-using-azure-portal.md)

  - [Azure Database Migration Service (DMS) ](../../dms/quickstart-create-data-migration-service-portal.md)

  - [ExpressRoute ](../../expressroute/expressroute-introduction.md)

  - 対応する[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)が確立された[ハブ アンド スポーク設計](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)を備えた [Azure Virtual Network](../../virtual-network/quick-create-portal.md)

  - [App Service ](../../app-service/overview.md)

  - [Application Gateway ](../../load-balancer/quickstart-load-balancer-standard-internal-portal.md?tabs=option-1-create-internal-load-balancer-standard)

  - App Services と MySQL インスタンスの[プライベート エンドポイント](../../private-link/private-endpoint-overview.md)

> [!NOTE]
> このガイドの一部として、MySQL 移行プロジェクトの考えられる Azure ランディング ゾーンをデプロイするために、2 つの ARM テンプレート (プライベート エンドポイントを含むものと含まないもの) が用意されました。 プライベート エンドポイントの ARM テンプレートでは、より安全な、運用環境と同様のシナリオが提供されます。 要件によっては、Azure ランディング ゾーンの追加の手動構成が必要になる場合があります。

### <a name="networking"></a>ネットワーク

迅速かつ最適な方法で移行元システムから Azure Database for MySQL にデータを移行することは、移行プロジェクトで検討すべき重要な要素です。 信頼性の低い小規模な接続では、好結果が得られるまで、管理者は移行を数回やり直すことが必要になる場合があります。 ネットワークの問題が原因で移行をやり直すと、無駄な作業が発生する可能性があります。

移行元、ツール、移行先の環境間のネットワーク接続を把握し、評価するための時間を取ってください。 インターネット接続をアップグレードするか、オンプレミス環境から Azure への ExpressRoute 接続を構成することが適切な場合もあります。 オンプレミスから Azure への接続を作成したら、次に、選択した移行ツールが移行元から移行先に接続できることを検証します。

移行ツールの場所によって、ネットワーク接続の要件が決まります。 次の表に示すように、選択した移行ツールは、オンプレミス マシンと Azure の両方に接続できる必要があります。 移行ツールの場所からのネットワーク トラフィックのみを受け入れるように Azure を構成する必要があります。

| 移行ツール                             | Type              | 場所        | 受信ネットワークの要件                                    | 送信ネットワークの要件                          |
|--------------------------------------------|-------------------|-----------------|-----------------------------------------------------------------|--------------------------------------------------------|
| **Database Migration Service (DMS)**           | オフライン           | Azure           | 外部 IP からの 3306 を許可する                                     | Azure MySQL データベース インスタンスに接続するためのパス |
| **インポート/エクスポート (MySQL Workbench、mysqldump)** | オフライン           | オンプレミス     | 内部 IP からの 3306 を許可する                                     | Azure MySQL データベース インスタンスに接続するためのパス |
| **インポート/エクスポート (MySQL Workbench、mysqldump)** | オフライン           | Azure VM        | 外部 IP からの 3306 を許可する                                     | Azure MySQL データベース インスタンスに接続するためのパス |
| **mydumper/myloader**                          | オフライン           | オンプレミス     | 内部 IP からの 3306 を許可する                                     | Azure MySQL データベース インスタンスに接続するためのパス |
| **mydumper/myloader**                          | オフライン           | Azure VM        | 外部 IP からの 3306 を許可する                                     | Azure MySQL データベース インスタンスに接続するためのパス |
| **binlog**                                     | オフライン           | オンプレミス     | プライベート エンドポイント経由の外部 IP またはプライベート IP からの 3306 を許可する | 各レプリケーション サーバーからマスターへのパス       |

ネットワークに関するその他の考慮事項は次のとおりです。

  - VNET 内にある DMS には、このサービスに対する[動的パブリック IP](../../dms/faq.md#setup) が割り当てられます。 このサービスは、作成時に [ExpressRoute](../../expressroute/expressroute-introduction.md) 経由または[サイト間 VPN](../../vpn-gateway/tutorial-site-to-site-portal.md) 経由で接続されている仮想ネットワーク内に配置できます。

  - Azure 仮想マシンを使用して移行ツールを実行する場合は、それにパブリック IP アドレスを割り当て、オンプレミスの MySQL インスタンスへの接続のみを許可します。

  - 送信ファイアウォールで、Azure Database for MySQL への送信接続を確保する必要があります。 MySQL ゲートウェイの IP アドレスは、「[Azure Database for MySQL の接続アーキテクチャ](../concepts-connectivity-architecture.md#azure-database-for-mysql-gateway-ip-addresses)」で確認できます。

### <a name="ssltls-connectivity"></a>SSL/TLS 接続

SSL ベースの通信への移行によるアプリケーションへの影響に加えて、SSL/TLS 接続の種類も考慮する必要があります。 Azure Database for MySQL データベースの作成後、SSL 設定を確認し、「[Azure Database for MySQL での SSL/TLS 接続](../concepts-ssl-connection-security.md)」を参照して、TLS 設定がセキュリティ態勢に及ぼす可能性のある影響を理解します。

> [!Important]
> そのページの免責事項に注意してください。 TLS バージョンの適用は、既定で有効になっていません。 TLS を有効にしたら、それを無効にする唯一の方法は、SSL を再度有効にすることです。

### <a name="wwi-scenario"></a>WWI のシナリオ

WWI のクラウド チームは、Azure Database for MySQL の特定のリソース グループに、必要な Azure ランディング ゾーン リソースを作成しました。 ランディング ゾーンを作成するために、WWI は ARM テンプレートを使用してセットアップとデプロイをスクリプト化することにしました。 ARM テンプレートを使用することで、必要に応じて環境をすばやく破棄して再設定できるようになります。

ARM テンプレートの一部として、仮想ネットワーク間のすべての接続が、ハブ アンド スポーク アーキテクチャのピアリングで構成されます。 データベースとアプリケーションは、個別の仮想ネットワークに配置されます。 Azure App Gateway がアプリ サービスの前に配置され、アプリ サービスをインターネットから分離できるようにます。 Azure App Service は、プライベート エンドポイントを使用して Azure Database for MySQL に接続します。

当初、WWI はオンライン移行をテストしたいと考えていましたが、DMS がオンプレミス環境に接続するためのネットワーク設定が必要なため、これは実行不可能でした。 WWI は、代わりにオフライン移行を実行することにしました。 MySQL Workbench ツールを使用して、オンプレミス データをエクスポートし、そのデータを Azure Database for MySQL インスタンスにインポートしました。

### <a name="planning-checklist"></a>チェックリストの立案

  - Azure ランディング ゾーンを準備します。 環境を迅速に破棄して再構築する必要がある場合に備えて、ARM テンプレートのデプロイを使用することを検討します。

  - ネットワーク設定を確認します。 接続、帯域幅、待ち時間、ファイアウォールの構成を確認する必要があります。

  - オンラインとオフラインのどちらのデータ移行戦略を使用するかを決定します。

  - SSL 証明書戦略を決定します。  


> [!div class="nextstepaction"]
> [移行方法](./migration-methods.md)