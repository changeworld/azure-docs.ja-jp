---
title: Azure Load Balancer Standard の概要 | Microsoft Docs
description: Azure Load Balancer Standard の機能の概要
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2018
ms.author: kumud
ms.openlocfilehash: 7e4fa38145ac21aaca8efa37250cc32ef1b1655c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2018
---
# <a name="azure-load-balancer-standard-overview-preview"></a>Azure Load Balancer Standard の概要 (プレビュー)

Azure Load Balancer Standard SKU とパブリック IP Standard SKU を組み合わせて使用すると、拡張性と信頼性に優れたアーキテクチャを構築できます。 Load Balancer Standard を使用するアプリケーションでは、新しい機能を利用できます。 すべての TCP および UDP アプリケーションの数百万のフローに対応する、低転送待機時間、高スループット、およびスケールを利用できます。

>[!NOTE]
> Load Balancer Standard SKU は現在プレビュー段階です。 プレビュー期間は、一般公開リリースの機能と同じレベルの可用性と信頼性がない場合があります。 詳細については、[Microsoft Azure プレビューのMicrosoft Azure 追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 運用サービスには、一般公開されている [Load Balancer Basic SKU](load-balancer-overview.md) を使用してください。 このプレビューで[可用性ゾーン (プレビュー)](https://aka.ms/availabilityzones) を使用するには、Load Balancer [標準プレビュー](#preview-sign-up)へのサインアップに加え、[別のサインアップ](https://aka.ms/availabilityzones)が必要になります。

## <a name="why-use-load-balancer-standard"></a>Load Balancer Standard を使用する理由

Load Balancer Standard は、さまざまな仮想データ センターで使用できます。 小規模のデプロイから大規模で複雑なマルチゾーン アーキテクチャまで、Load Balancer Standard を使用すると、次の機能を利用できます。

- Load Balancer Standard では、[エンタープライズ スケール](#enterprisescale)を実現できます。 この機能は、仮想ネットワーク内の仮想マシン (VM) インスタンス (最大 1000 個の VM インスタンス) でご利用いただけます。

- [新しい診断インサイト](#diagnosticinsights)を使用すると、仮想データ センターのこの重要なコンポーネントについて理解し、コンポーネントの管理やトラブルシューティングを行うことができます。 Azure Monitor (プレビュー) を使用して、連続するデータ パスの正常性を計測するために、新しい多次元メトリックを表示、フィルター、およびグループ化することができます。 フロント エンドから VM、エンドポイントの正常性プローブまで、および TCP 接続の試行用に発信接続までデータを監視します。

- Load Balancer Standard SKU またはパブリック IP の Standard SKU に関連付けられている VM インスタンスで[ネットワーク セキュリティ グループ](#nsg)が必須になりました。 ネットワーク セキュリティ グループ (NSG) により、シナリオのセキュリティが強化されます。

- ネットワーク仮想アプライアンス (NVA) や他のアプリケーション シナリオで、[高可用性 (HA) ポートは高信頼性とスケールを提供](#highreliability)します。 HA ポートは、Azure Internal Load Balancer (ILB) フロントエンドのすべてのポートを VM インスタンスのプールに負荷分散します。

- 優れた回復性とスケールを実現する新しい Source Network Address Translation (SNAT) ポート割り当てモデルが[送信接続](#outboundconnections)で使用されるようになりました。

- [可用性ゾーンを使用する Load Balancer Standard](#availabilityzones) を使用して、ゾーン冗長アーキテクチャとゾーン ベース アーキテクチャを構築できます。 これらのアーキテクチャは、どちらにもクロスゾーン負荷分散を含めることができます。 DNS レコードに依存せずに、ゾーン冗長を実現できます。 既定では、単一 IP アドレスがゾーン冗長です。  単一 IP アドレスは、すべての可用性ゾーンにまたがるリージョン内の仮想ネットワークにあるすべての仮想マシンに到達できます。


Load Balancer Standard をパブリック構成または内部構成で使用して、次の基本的なシナリオをサポートできます。

- 受信トラフィックを正常なバックエンド インスタンスに負荷分散する。
- 受信トラフィックを 1 つのバックエンド インスタンスにポート転送する。
- 送信トラフィックを、仮想ネットワーク内のプライベート IP アドレスからパブリック IP アドレスに変換する。

### <a name = "enterprisescale"></a>エンタープライズ スケール

 Load Balancer Standard を使用して、高パフォーマンスの仮想データ センターを設計し、任意の TCP または UDP アプリケーションをサポートします。 スタンドアロン VM インスタンス、または仮想マシン スケール セットの最大 1,000 インスタンスをバックエンド プールで使用します。 低転送待機時間、高スループット パフォーマンス、完全に管理された Azure サービスの数百万のフローに対応するスケールを引き続き利用できます。
 
Load Balancer Standard は、リージョンの仮想ネットワーク内の任意の VM インスタンスにトラフィックを転送できます。 バックエンド プールのサイズは、次の VM シナリオの組み合わせで最大 1000 インスタンスまで可能です。

- 可用性セットに含まれていないスタンドアロン VM
- 可用性セットに含まれているスタンドアロン VM
- 仮想マシン スケール セット (最大 1,000 インスタンス)
- 複数の仮想マシン スケール セット
- VM と仮想マシン スケール セットの混合

可用性セットの要件はなくなりました。 提供されるその他の利点を考慮して、可用性セットの使用を選択することができます。

### <a name = "diagnosticinsights"></a>診断インサイト

Load Balancer Standard には、パブリックおよび内部ロード バランサー構成の新しい多次元診断機能が用意されています。 Azure Monitor (プレビュー) によって次の新しいメトリックが提供されます。これらのメトリックでは、ダウンストリーム コンシューマーと統合するための機能など、関連するすべての機能を利用します。

| メトリック | [説明] |
| --- | --- |
| VIP 可用性 | Load Balancer Standard は、リージョン内から Load Balancer フロントエンドを経て、VM をサポートする SDN スタックに至るまでのデータ パスを継続的に学習します。 正常なインスタンスが保持されていれば、測定ではアプリケーションの負荷分散されたトラフィックと同じパスに従います。 顧客が使用しているデータ パスも検証されます。 測定はアプリケーションには見えないので、他の操作と干渉することはありません。|
| DIP 可用性 | Load Balancer Standard では、構成設定に従ってアプリケーション エンドポイントの正常性を監視する、分散型の正常性プローブ サービスを使用します。 このメトリックは、Load Balancer プールの個々のインスタンス エンドポイントの集計ビューまたはエンドポイントごとのフィルター ビューを提供します。  正常性プローブ構成で示されているアプリケーションの正常性を、Load Balancer がどのように表示するのかを確認できます。
| SYN パケット | Load Balancer Standard が TCP 接続を終了したり、TCP または UDP パケット フローと対話したりすることはありません。 フローとハンドシェイクは、常にソースと VM インスタンスの間で発生します。 TCP プロトコルのシナリオのトラブルシューティングを適切に行うために、SYN パケット カウンターを使用して TCP 接続試行の数を把握できます。 このメトリックは、受信済みの TCP SYN パケットの数を報告します。|
| SNAT 接続 | Load Balancer Standard は、パブリック IP アドレス フロントエンドにマスカレードされた送信フローの数を報告します。 SNAT ポートは有限のリソースです。 このメトリックはアプリケーションが送信フローで SNAT にどれくらい依存しているかを示すことができます。  成功した送信 SNAT フローと失敗した送信 SNAT フローのカウンターがレポートされるので、送信フローの正常性について、トラブルシューティングしたり、理解したりするのに役立てることができます。|
| バイト カウンター | Load Balancer Standard は、フロントエンドごとに処理されたデータを報告します。|
| パケット カウンター | Load Balancer Standard は、フロントエンドごとに処理されたパケットを報告します。|

### <a name = "highreliability"></a>高信頼性

アプリケーションをスケールし、高信頼性を維持するために、負荷分散規則を構成します。 個々のポートの規則を構成できます。また、HA ポートを使用して、TCP または UDP ポート番号に関係なく、すべてのトラフィックを分散させることもできます。  

新しい HA ポート機能を使用すると、内部の NVA の高可用性とスケールなどの各種シナリオにも対応できるようになります。 また、個々のポートを指定するのは実用的ではなかったり、望ましくなかったりするその他のシナリオにも対応できるようになります。 HA ポートは、必要な数だけインスタンスを使用できるようにすることで、冗長性とスケールを提供します。 構成が、アクティブ/パッシブ シナリオに制限されることはなくなりました。 正常性プローブ構成により、正常なインスタンスにのみトラフィックを転送することでサービスが保護されます。

NVA のベンダーは、ベンダーが完全にサポートする回復性の高いシナリオを顧客に提供できます。 顧客の単一障害点を排除し、スケールに合わせて複数のアクティブ インスタンスを使用することができます。 アプライアンスで許可されていれば、複数のインタンスにスケールできます。 これらのシナリオの詳細なガイダンスについては、NVA のベンダーにお問い合わせください。

### <a name = "availabilityzones"></a>可用性ゾーン

[!INCLUDE [availability-zones-preview-statement](../../includes/availability-zones-preview-statement.md)]

サポートされているリージョンで可用性ゾーンを使用して、アプリケーションの回復性を高めます。 可用性ゾーンは特定のリージョンで現在プレビュー段階であり、追加のオプトインが必要となります。

### <a name="automatic-zone-redundancy"></a>自動ゾーン冗長性

Load Balancer でアプリケーションごとにゾーン冗長またはゾーン フロントエンドを提供するかどうかを選択できます。 Load Balancer Standard では、ゾーン冗長性を簡単に作成できます。 単一のフロントエンド IP アドレスが自動的にゾーン冗長になります。 ゾーン冗長フロントエンドは、リージョン内のすべての可用性ゾーンによって同時に提供されます。 受信接続と送信接続のゾーン冗長データ パスが作成されます。 Azure のゾーン冗長性には、複数の IP アドレスや DNS レコードは不要です。 

ゾーン冗長性は、パブリックまたは内部フロントエンドで提供されます。 パブリック IP アドレスと内部ロード バランサー フロントエンドのプライベート IP をゾーン冗長にすることができます。

内部ロード バランサーに対してゾーン冗長のパブリック IP アドレスを作成するには、次のスクリプトを使用します。 構成で既存の Resource Manager テンプレートを使用している場合は、これらのテンプレートに **sku** セクションを追加してください。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

内部ロード バランサーに対してゾーン冗長のフロントエンド IP を作成するには、次のスクリプトを使用します。 構成で既存の Resource Manager テンプレートを使用している場合は、これらのテンプレートに **sku** セクションを追加してください。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zone_redundant_frontend",
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

パブリック IP フロントエンドがゾーン冗長の場合、VM インスタンスからの送信接続が自動的にゾーン冗長になります。 フロントエンドは、ゾーンの障害から保護されます。 ゾーンの障害が発生しても、SNAT ポートの割り当ては保持されます。

#### <a name="cross-zone-load-balancing"></a>クロスゾーン負荷分散

クロスゾーン負荷分散は、リージョン内のバックエンド プールで利用できるため、VM インスタンスの柔軟性を最大限に高めることができます。 フロントエンドは、VM インスタンスの可用性ゾーンに関係なく、仮想ネットワーク内の任意の VM へのフローを提供します。

さらに、フロントエンド インスタンスとバックエンド インスタンスに特定のゾーンを指定して、特定のゾーンに合わせてデータ パスとリソースを調整することもできます。

仮想ネットワークとサブネットには、ゾーンの制約がありません。 必要な VM インスタンスを含むバックエンド プールを定義するだけで構成が完了します。

#### <a name="zonal-deployments"></a>ゾーン展開

必要に応じて、ゾーン フロントエンドを定義して、ロード バランサー フロントエンドを特定のゾーンに配置することもできます。 ゾーン フロントエンドは、指定された 1 つの可用性ゾーンでのみ提供されます。 ゾーンの VM インスタンスと組み合わせると、リソースを特定のゾーンに配置できます。

特定のゾーンに作成されたパブリック IP アドレスは、常にそのゾーンにのみ存在します。 パブリック IP アドレスのゾーンを変更することはできません。 複数のゾーンのリソースにアタッチできるパブリック IP アドレスの場合は、代わりにゾーン冗長のパブリック IP を作成します。

可用性ゾーン 1 にパブリック IP アドレスを作成するには、次のスクリプトを使用します。 構成で既存の Resource Manager テンプレートを使用している場合は、これらのテンプレートに **sku** セクションを追加してください。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "zones": [ "1" ],
            "sku":
            {
                "name": "Standard"
            },
```

内部ロード バランサー フロントエンドを可用性ゾーン 1 に作成するには、次のスクリプトを使用します。

構成で既存の Resource Manager テンプレートを使用している場合は、これらのテンプレートに **sku** セクションを追加してください。 また、**zones** プロパティをフロントエンド IP 構成の子リソースに定義します。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "zonal_frontend_in_az1",
                        "zones": [ "1" ],
                        "properties": {
                            "subnet": {
                                "Id": "[variables('subnetRef')]"
                            },
                            "privateIPAddress": "10.0.0.6",
                            "privateIPAllocationMethod": "Static"
                        }
                    },
                ],
```

仮想ネットワーク内の VM インスタンスをバックエンド プールに配置することで、バックエンド プールにクロスゾーン負荷分散を追加します。

可用性ゾーンがサポートされている場合、Load Balancer Standard リソースは常にリージョン ベースであり、ゾーン冗長です。 ゾーンが割り当てられていないパブリック IP アドレスまたは内部ロード バランサー フロントエンドは、どのリージョンにでもデプロイできます。 可用性ゾーンのサポートは、デプロイ機能には影響しません。 後日、リージョンで可用性ゾーンが使用できるようになった場合、既にデプロイされているパブリック IP または内部ロード バランサー フロントエンドは自動的にゾーン冗長になります。 ゾーン冗長データ パスは、パケット損失が 0% であることを意味するわけではありません。

### <a name = "nsg"></a>ネットワーク セキュリティ グループ

仮想ネットワークへの Load Balancer Standard とパブリック IP Standard の完全なオンボードには、ネットワーク セキュリティ グループ (NSG) が必要です。 NSG により、トラフィック フローのホワイトリスト登録が可能になります。 NSG により、デプロイへのトラフィックを完全に制御することができます。 他のトラフィック フローが完了するまで待機する必要がなくなりました。

NSG をサブネットまたはバックエンド プールの VM インスタンスのネットワーク インターフェイス (NIC) に関連付けます。 この構成は、Load Balancer Standard と、インスタンス レベルのパブリック IP として使用されているパブリック IP Standard で使用します。 トラフィックが流れるようにするには、NSG で許可するトラフィックを明示的にホワイトリストに登録する必要があります。

NSG と、ネットワーク セキュリティ グループをシナリオに適用する方法の詳細については、[ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)に関する記事をご覧ください。

### <a name ="outboundconnections"></a>送信接続

ロード バランサーがポート マスカレード SNAT を使用する場合、Load Balancer Standard は VNet 内の VM に送信接続を提供します。 ポート マスカレード SNAT アルゴリズムにより、堅牢性とスケールが強化されます。

パブリック ロード バランサー リソースが VM インスタンスに関連付けられている場合、各送信接続のソースは書き換えられます。 ソースは、仮想ネットワークのプライベート IP アドレス空間からロード バランサー フロントエンドのパブリック IP アドレスに書き換えられます。

ゾーン冗長フロントエンドと共に使用すると、送信接続もゾーン冗長になり、ゾーンの障害が発生しても、SNAT ポートの割り当ては保持されます。

Load Balancer Standard の新しいアルゴリズムでは、各 VM の NIC に SNAT ポートを事前に割り当てます。 NIC がプールに追加されると、プール サイズに基づいて SNAT ポートが事前に割り当てられます。 次の表は、6 レベルのバックエンド プール サイズに対するポートの事前割り当てを示しています。

| プール サイズ (VM インスタンス) | 事前に割り当てられている SNAT ポート数 |
| --- | --- |
| 1 - 50 | 1024 |
| 51 - 100 | 512 |
| 101 - 200 | 256 |
| 201 - 400 | 128 |
| 401 - 800 | 64 |
| 801 - 1,000 | 32 |

SNAT ポートは発信接続数に直接変換されるわけではありません。 複数の一意の送信先に SNAT ポートを再利用できます。 詳細については、[送信接続](load-balancer-outbound-connections.md)に関する記事をご覧ください。

バックエンド プールのサイズが増加し、1 つ大きいレベルに移行すると、割り当て済みポートの半分が解放されます。 解放されたポートに関連付けられている接続はタイムアウトになるので、再確立する必要があります。 新しい接続試行はすぐに成功します。 バックエンド プールのサイズが減少し、1 つ小さいレベルに移行すると、使用できる SNAT ポートが増えます。 この場合、既存の接続には影響しません。

Load Balancer Standard には、規則ごとに使用できる追加構成オプションもあります。 このオプションでは、複数のフロントエンドが使用可能な場合に、ポート マスカレード SNAT に使用するフロントエンドを制御できます。

Load Balancer Standard だけが VM インスタンスを提供している場合、SNAT 送信接続は使用できません。 VM インスタンスをパブリック ロード バランサーにも割り当てることで、この機能を明示的に復元できます。 さらに、パブリック IP をインスタンス レベルのパブリック IP として各 VM インスタンスに直接割り当てることもできます。 この構成オプションは、オペレーティング システムとアプリケーションの一部のシナリオで必要になる場合があります。 

### <a name="port-forwarding"></a>ポート フォワーディング

Basic と Standard の Load Balancer では、フロントエンド ポートを個々のバックエンド インスタンスにマップする受信 NAT 規則を構成できます。 これらの規則を構成することにより、リモート デスクトップ プロトコル エンドポイントおよび SSH エンドポイントを公開したり、他のさまざまなアプリケーション シナリオを実行したりできます。

Load Balancer Standard では、受信 NAT 規則を通じてポート フォワーディング機能を引き続き提供します。 ゾーン冗長フロントエンドと共に使用すると、受信 NAT 規則はゾーン冗長になり、ゾーンの障害が発生しても保持されます。

### <a name="multiple-front-ends"></a>複数のフロントエンド

アプリケーションが複数の個別の IP アドレスを公開する必要がある場合は (TLS Web サイトや SQL AlwaysOn 可用性グループのエンドポイントなど) 、設計の柔軟性を確保するために複数のフロントエンドを構成します。 

Load Balancer Standard では、一意の IP アドレスで特定のアプリケーション エンドポイントを公開する必要がある場合に、複数のフロントエンドを引き続き提供します。

複数のフロントエンドを構成する方法の詳細については、「[複数の IP 構成](load-balancer-multivip-overview.md)」を参照してください。

## <a name = "sku"></a>SKU について

SKU は、Azure Resource Manager デプロイメント モデルでのみ利用できます。 このプレビューでは、Load Balancer リソースとパブリック IP リソースの 2 つの SKU (Basic と Standard) が導入されています。 これらの SKU は、機能、パフォーマンス特性、制限事項、一部の組み込み動作が異なります。 仮想マシンはどちらの SKU でもご利用いただけます。 Load Balancer リソースとパブリック IP リソースのどちらについても、SKU は引き続き省略可能な属性です。 SKU がシナリオ定義で省略されている場合、既定の構成は Basic SKU になります。

>[!IMPORTANT]
>リソースの SKU は変更不可です。 既存のリソースの SKU を変更することはできません。  

### <a name="load-balancer"></a>Load Balancer

[既存の Load Balancer リソース](load-balancer-overview.md)は Basic SKU になりますが、一般公開されている状態に変わりはありません。

Load Balancer Standard SKU は新機能であり、現在プレビュー段階です。 Microsoft.Network/loadBalancers の 2017-08-01 API バージョンで、リソース定義に **sku** プロパティが導入されています。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/loadBalancers",
            "name": "load_balancer_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```
Load Balancer Standard は、可用性ゾーンを提供するリージョンで自動的にゾーン回復性を持ちます。 Load Balancer Standard がゾーンとして宣言されている場合は、自動的にゾーン回復性を持ちません。

### <a name="public-ip"></a>パブリック IP

[既存のパブリック IP リソース](../virtual-network/virtual-network-ip-addresses-overview-arm.md)は Basic SKU になり、Basic の機能、パフォーマンス特性、制限事項が適用されて引き続き一般公開されます。

パブリック IP Standard SKU は新機能であり、現在プレビュー段階です。 Microsoft.Network/publicIPAddresses の 2017-08-01 API バージョンで、リソース定義に **sku** プロパティが導入されています。

```json
            "apiVersion": "2017-08-01",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "public_ip_standard",
            "location": "region",
            "sku":
            {
                "name": "Standard"
            },
```

複数の割り当て方法を提供するパブリック IP Basic とは異なり、パブリック IP Standard は常に静的割り当てを使用します。

パブリック IP Standard は、可用性ゾーンを提供するリージョンで自動的にゾーン回復性を持ちます。 パブリック IP がゾーンとして宣言されている場合は、自動的にゾーン回復性を持ちません。 ゾーンのパブリック IP は、別のゾーンに変更できません。

## <a name="migration-between-skus"></a>SKU 間での移行

SKU は変更不可です。 一方の SKU からもう一方の SKU に移行する場合は、このセクションの手順に従います。

### <a name="migrate-from-basic-to-standard-sku"></a>Basic SKU から Standard SKU への移行

1. 新しい Standard リソース (必要に応じて、Load Balancer とパブリック IP) を作成します。 規則とプローブ定義を再作成します。

2. NIC またはサブネットで新規 NSGを作成するか、既存の NSG を更新して、負荷分散されたトラフィック、プローブ、その他の許可するすべてのトラフィックをホワイトリストに登録します。

3. すべての VM インスタンスから Basic SKU リソース (必要に応じて、Load Balancer とパブリック IP) を削除します。 可用性セットのすべての VM インスタンスも削除してください。

4. すべての VM インスタンスを新しい Standard SKU リソースに接続します。

### <a name="migrate-from-standard-to-basic-sku"></a>Standard SKU から Basic SKU への移行

1. 新しい Basic リソース (必要に応じて、Load Balancer とパブリック IP) を作成します。 規則とプローブ定義を再作成します。 

2. すべての VM インスタンスから Standard SKU リソース (必要に応じて、Load Balancer とパブリック IP) を削除します。 可用性セットのすべての VM インスタンスも削除してください。

3. すべての VM インスタンスを新しい Basic SKU リソースに接続します。

>[!IMPORTANT]
>
>Basic SKU と Standard SKU の使用には制限事項があります。
>
>Standard SKU の HA ポート診断機能は、Standard SKU でのみ提供されます。 Standard SKU から Basic SKU に移行し、この機能を保持することはできません。
>
>Load Balancer リソースとパブリック IP リソースには一致する SKU を使用する必要があります。 Basic SKU リソースと Standard SKU リソースを組み合わせることはできません。 VM、可用性セットの VM、または仮想マシン スケール セットを両方の SKU に同時に接続したりすることはできません。
>

## <a name="region-availability"></a>利用可能なリージョン

現在、Load Balancer Standard は、米国西部以外のすべてのパブリック クラウド リージョンで利用できます。

>[!IMPORTANT]
> 初期起動リージョン (米国東部 2、米国中部、北ヨーロッパ、米国中西部、西ヨーロッパ、東南アジア) の外部のリージョンに短期間アクセスするには、追加のサブスクリプション機能 (AllowLBPreviewWave2 と AllowLBPreviewWave3) の登録が必要です。  [こちらに記した手順に従ってください](#additionalpreviewregions)。 以前に AllowLBPreview にサインアップしたことがある場合でも、これらの手順をすべて実行してください。
> この要件は今後数週間で削除される予定です。

## <a name="sku-service-limits-and-abilities"></a>SKU のサービスの制限と機能

[Azure サービスのネットワークの制限](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)は、各サブスクリプションのリージョンごとに適用されます。 

次の表では、Load Balancer の Basic SKU と Standard SKU の制限と機能を比較しています。

| Load Balancer | Basic | 標準 |
| --- | --- | --- |
| バックエンド プールのサイズ | 最大 100 | 最大 1,000 |
| バックエンド プールの境界 | 可用性セット | 仮想ネットワーク、リージョン |
| バックエンド プールの設計 | 可用性セットの VM、可用性セットの仮想マシン スケール セット | 仮想ネットワーク内の任意の VM インスタンス |
| HA ポート | サポートされていません | 使用可能 |
| 診断 | 制限付き、パブリックのみ | 使用可能 |
| VIP 可用性  | サポートされていません | 使用可能 |
| 高速 IP モビリティ | サポートされていません | 使用可能 |
|可用性ゾーンのシナリオ | ゾーン ベースのみ | ゾーン ベース、ゾーン冗長、クロスゾーン負荷分散 |
| 送信 SNAT アルゴリズム | オンデマンド | 事前割り当て |
| 送信 SNAT フロントエンドの選択 | 構成不可、複数の候補 | 候補を減らすオプション構成 |
| ネットワーク セキュリティ グループ | NIC/サブネットでは省略可能 | 必須 |

次の表では、パブリック IP の Basic SKU と Standard SKU の制限と機能を比較しています。

| パブリック IP | Basic | 標準 |
| --- | --- | --- |
| 可用性ゾーンのシナリオ | ゾーン ベースのみ | ゾーン冗長 (既定)、ゾーン ベース (オプション) | 
| 高速 IP モビリティ | サポートされていません | 使用可能 |
| VIP 可用性 | サポートされていません | 使用可能 |
| Counters | サポートされていません | 使用可能 |
| ネットワーク セキュリティ グループ | NIC では省略可能 | 必須 |


## <a name="preview-sign-up"></a>プレビューのサインアップ

Load Balancer Standard SKU とそのコンパニオンのパブリック IP Standard SKU のプレビューに参加するには、サブスクリプションを登録します。  サブスクリプションを登録すると、PowerShell または Azure CLI 2.0 からのアクセス権を取得できます。 登録するには、次の手順に従います。

>[!NOTE]
>Load Balancer Standard の機能の登録には、グローバルに有効になるまで 1 時間ほどかかる場合があります。 Load Balancer Standard を[可用性ゾーン](https://aka.ms/availabilityzones)で使用する場合は、AZ プレビュー用に[別のサインアップ](https://aka.ms/availabilityzones)が必要です。

<a name="additionalpreviewregions"></a>
>[!IMPORTANT]
> 初期起動リージョン (米国東部 2、米国中部、北ヨーロッパ、米国中西部、西ヨーロッパ、東南アジア) の外部のリージョンに短期間アクセスするには、追加のサブスクリプション機能 (AllowLBPreviewWave2 と AllowLBPreviewWave3) の登録が必要です。  以下の手順は、追加のサブスクリプション機能を有効にするために変更されています。 以前に AllowLBPreview にサインアップしたことがある場合でも、これらの手順をすべて実行してください。 この要件は今後数週間で削除される予定です。


### <a name="sign-up-by-using-azure-cli-20"></a>Azure CLI 2.0 を使用したサインアップ

1. 機能を次のようにプロバイダーに登録します。

    ```cli
    az feature register --name AllowLBPreview --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave2 --namespace Microsoft.Network
    az feature register --name AllowLBPreviewWave3 --namespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。 操作の状態は、次のコマンドで確認できます。

    ```cli
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreview']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave2']" --output json
    az feature list --query "[?name=='Microsoft.Network/AllowLBPreviewWave3']" --output json
    ```
    
    上記の各サブスクリプション機能について、機能の登録状態が "Registered" になったら、次の手順に進んでください。 例:
   
    ```json
    {
       "id": "/subscriptions/foo/providers/Microsoft.Features/providers/Microsoft.Network/features/AllowLBPreview",
       "name": "Microsoft.Network/AllowLBPreview",
       "properties": {
          "state": "Registered"
       },
       "type": "Microsoft.Features/providers/features"
    }
    ```
    
4. サブスクリプションをリソース プロバイダーに再登録して、プレビューのサインアップを完了してください。

    ```cli
    az provider register --namespace Microsoft.Network
    ```
    

### <a name="sign-up-by-using-powershell"></a>PowerShell を使用したサインアップ

1. 機能を次のようにプロバイダーに登録します。

    ```powershell
    Register-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Register-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```
    
2. この操作は、完了するまで最大 10 分かかることがあります。 操作の状態は、次のコマンドで確認できます。

    ```powershell
    Get-AzureRmProviderFeature -FeatureName AllowLBPreview -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave2 -ProviderNamespace Microsoft.Network
    Get-AzureRmProviderFeature -FeatureName AllowLBPreviewWave3 -ProviderNamespace Microsoft.Network
    ```

  上記の各サブスクリプション機能について、機能の登録状態が "Registered" になったら、次の手順に進んでください。 例:

    ```
    FeatureName      ProviderName        RegistrationState
    -----------      ------------        -----------------
    AllowLBPreview   Microsoft.Network   Registered
    ```
    
3. サブスクリプションをリソース プロバイダーに再登録して、プレビューのサインアップを完了してください。

    ```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
    ```
 
## <a name="pricing"></a>価格

Load Balancer Standard SKU は、構成済みの規則と処理されたデータに基づいて課金されます。 プレビュー期間中は、料金は発生しません。 詳細については、[Load Balancer](https://aka.ms/lbpreviewpricing) と[パブリック IP](https://aka.ms/lbpreviewpippricing) の価格のページをご覧ください。

Load Balancer Basic SKU は、引き続き無料でご利用いただけます。

## <a name="limitations"></a>制限事項

プレビュー時点で次の制限事項が適用されます。これらの制限事項は変更されることがあります。

- 現時点では、Load Balancer のバックエンド インスタンスをピアリングされた仮想ネットワークに配置することはできません。 バックエンド インスタンスはすべて同じリージョンに存在する必要があります。
- SKU は変更不可です。 既存のリソースの SKU を変更することはできません。
- スタンドアロン VM、可用性セットのすべての VM インスタンス、または仮想マシン スケール セットを両方の SKU で使用することができます。 VM の組み合わせを両方の SKU で同時に使用することはできません。 SKU の組み合わせが含まれる構成は許可されていません。
- VM インスタンス (または可用性セットの VM インスタンス) で内部 Load Balancer Standard を使用すると、[既定の SNAT 送信接続](load-balancer-outbound-connections.md)が無効になります。 この機能をスタンドアロン VM、可用性セットのすべての VM インスタンス、または仮想マシン スケール セットに復元することができます。 発信接続を行う機能を復元することもできます。 これらの機能を復元するには、パブリック Load Balancer Standard またはインスタンス レベルのパブリック IP としてのパブリック IP Standard を同じ VM インスタンスに同時に割り当てます。 割り当てが完了すると、パブリック IP アドレスに対するポート マスカレード SNAT が再び提供されます。
- フル スケースのバックエンド プールを実現するために、VM インスタンスを可用性セットにグループ化することが必要な場合があります。 最大 150 個の可用性セットとスタンドアロン VM を 1 つのバックエンド プールに配置できます。
- IPv6 はサポートされていません。
- 可用性ゾーンのコンテキストでは、フロントエンドをゾーン ベースからゾーン冗長に変更したり、ゾーン冗長からゾーン ベースに変更したりすることはできません。 フロント エンドは、ゾーン冗長として作成した後は常にゾーン冗長のままです。 フロント エンドは、ゾーン ベースとして作成した後は常にゾーン ベースのままです。
- 可用性ゾーンのコンテキストでは、ゾーンのパブリック IP アドレスをゾーン間で移動することはできません。
- [Azure Monitor アラート](../monitoring-and-diagnostics/monitoring-overview-alerts.md)は、現時点ではサポートされていません。
- ポータルでは、拡張されたプレビュー リージョンがまだサポートされていません。  回避策として、テンプレート、Azure CLI 2.0、PowerShell などのクライアント ツールを使用してください。
- サブスクリプションの移動操作はサポートされていません。
- 米国西部では使用できません。


## <a name="next-steps"></a>次の手順

- [Load Balancer Basic](load-balancer-overview.md) の詳細を確認する。
- [可用性ゾーン](../availability-zones/az-overview.md)の詳細を学習する。
- [ネットワーク セキュリティ グループ](../virtual-network/virtual-networks-nsg.md)の詳細を確認する。
- Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
- [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) で[公開されたメトリック](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkloadbalancers)を確認する。
