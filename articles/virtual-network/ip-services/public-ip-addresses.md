---
title: Azure でのパブリック IP アドレス
titleSuffix: Azure Virtual Network
description: Azure でのパブリック IP アドレスについて説明します。
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: conceptual
ms.date: 04/29/2021
ms.author: allensu
ms.openlocfilehash: a57c4ea8d058f0f565b6dbec7fdb74b07ea7564e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471404"
---
# <a name="public-ip-addresses"></a>パブリック IP アドレス

インターネット リソースから Azure リソースに入ってくる通信には、パブリック IP アドレスが使用されます。 パブリック IP アドレスにより、Azure リソースからインターネットへの通信と、公開されている Azure サービスへの通信が可能になります。 このアドレスは、特定のリソース専用に確保され、明示的に割り当てが解除されない限り維持されます。 パブリック IP が割り当てられていないリソースは、送信通信できます。 Azure では、リソース専用ではない使用可能な IP アドレスが動的に割り当てられます。 Azure での送信接続の詳細については、[送信用接続の詳細](../../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページを参照してください。

Azure リソース マネージャーで、 [パブリック IP](virtual-network-public-ip-address.md) アドレスは、独自のプロパティを持つリソースです。 パブリック IP アドレスのリソースは、次のリソースのいずれかと関連付けることができます。

* 仮想マシン ネットワーク インターフェイス
* インターネットに接続するロード バランサー
* Virtual Network ゲートウェイ (VPN/ER)
* NAT Gateway
* アプリケーション ゲートウェイ
* Azure Firewall
* bastion ホスト

Virtual Machine Scale Sets の場合は、[パブリック IP プレフィックス](public-ip-address-prefix.md)を使用します。

## <a name="at-a-glance"></a>早見表

下の表は、パブリック IP をリソースに関連付けることができるプロパティと、割り当て方法を示しています。 現時点では、パブリック IPv6 のサポートはすべてのリソースの種類で使用できるわけではないことに注意してください。

| 最上位リソース | IP アドレスの関連付け | 動的 IPv4 | 静的 IPv4 | 動的 IPv6 | 静的 IPv6 |
| --- | --- | --- | --- | --- | --- |
| 仮想マシン |ネットワーク インターフェイス |はい | はい | はい | はい |
| インターネットに接続するロード バランサー |フロント エンド構成 |はい | はい | はい |はい |
| Virtual Network ゲートウェイ (VPN) |ゲートウェイ IP の構成 |はい (AZ 以外のみ) |はい (AZ のみ) | いいえ |いいえ |
| Virtual Network ゲートウェイ (ER) |ゲートウェイ IP の構成 |はい | いいえ | はい (プレビュー) |いいえ |
| NAT Gateway |ゲートウェイ IP の構成 |いいえ |はい | いいえ |いいえ |
| Application gateway |フロント エンド構成 |はい (V1 のみ) |はい (V2 のみ) | いいえ | いいえ |
| Azure Firewall | フロント エンド構成 | いいえ | はい | いいえ | いいえ |
| bastion ホスト | パブリック IP 構成 | いいえ | はい | いいえ | いいえ |

## <a name="ip-address-version"></a>IP アドレスのバージョン

パブリック IP アドレスは、IPv4 または IPv6 アドレスを使用して作成できます。 IPv4 アドレスと IPv6 アドレスを使用してデュアルスタック デプロイメントを作成するオプションが表示される場合があります。

## <a name="sku"></a>SKU

パブリック IP アドレスは、次の SKU のいずれかを使用して作成されます。

### <a name="standard"></a>Standard

Standard SKU のパブリック IP アドレス:

- 必ず静的割り当て方法を使用してください。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- "既定でのセキュリティ保護" モデルと足並みを揃えるように設計されており、フロントエンドとして使用されるときはインバウンド トラフィックに対して閉じられます。  データ プレーン トラフィックと[ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#network-security-groups) (NSG) を許可リストに載せることが必要になります (たとえば、Standard SKU パブリック IP がアタッチされた仮想マシンの NIC で)。
- ゾーン冗長 (3 つすべてのゾーンからアドバタイズ)、ゾーン ベース (特定の事前に選択された可用性ゾーンで保証)、または "ゾーンなし" (特定の事前に選択された可用性ゾーンに関連付けられていない) にすることができます。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。 **ゾーン冗長 IP は、[3 つの可用性ゾーン](../../availability-zones/az-region.md)が有効になっているリージョンでのみ作成できます。** ゾーンが有効になる前に作成された IP は、ゾーン冗長にはなりません。
- [ルーティング設定](routing-preference-overview.md)を使用して、Azure とインターネット間のトラフィックのルーティング方法をより細かく制御できるようにすることができます。
- [リージョン間ロード バランサー ](../../load-balancer/cross-region-overview.md)(プレビュー機能) のためのエニーキャスト フロントエンド IP として使用できます。

> [!NOTE]
> [インスタンス メタデータ サービス (IMDS)](../../virtual-machines/windows/instance-metadata-service.md) を使用している場合は、Basic SKU のパブリック IP アドレスのみを使用できます。 Standard SKU はサポートされていません。

> [!NOTE]
> Standard SKU のパブリック IP アドレスを使用している場合、診断設定は [リソース] ブレードに表示されません。 Standard パブリック IP アドレス リソースのログ記録を有効にするには、[Azure Monitor] ブレードの [診断設定] に移動し、IP アドレス リソースを選択します。

> [!NOTE]
> [ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md#network-security-groups)を作成して関連付け、目的のインバウンド トラフィックを明示的に許可するまで、Standard SKU リソースとのインバウンド通信は失敗します。

### <a name="basic"></a>基本

Basic SKU のアドレス:

- IPv4 の場合: 動的または静的な割り当て方法を使用して割り当てることができます。  IPv6 の場合: 動的な割り当て方法のみを使用して割り当てることができます。
- インバウンドから発生するフローの調整可能なアイドル タイムアウトとして4分から30 分 (既定値は 4 分) が、またアウトバウンドから発生するフローの固定アイドル タイムアウトとして 4 分が割り当てられます。
- 既定で開いています。  ネットワーク セキュリティ グループは推奨されますが、受信または送信トラフィックを制限する場合は省略可能です。
- 可用性ゾーンのシナリオはサポートしません。 該当するリージョンでの可用性ゾーンのシナリオには、Standard SKU のパブリック IP を使用します。 可用性ゾーンに関する詳細については、[可用性ゾーンの概要](../../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページと「[Standard Load Balancer と可用性ゾーン](../../load-balancer/load-balancer-standard-availability-zones.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」を参照してください。
- [ルーティング設定](routing-preference-overview.md)または[リージョン間ロード バランサー](../../load-balancer/cross-region-overview.md)の機能はサポートされません。

> [!NOTE]
> Basic SKU IPv4 アドレスは、作成後に Standard SKU にアップグレードできます。  SKU のアップグレードについては、[パブリック IP のアップグレード](public-ip-upgrade-portal.md)に関するページをご覧ください。

>[!IMPORTANT]
> ロード バランサー リソースとパブリック IP リソースには一致する SKU を使用する必要があります。 Basic SKU リソースと Standard SKU リソースを組み合わせることはできません。 スタンドアロン仮想マシン、可用性セット リソース内の仮想マシン、または仮想マシン スケール セット リソースを、両方の SKU に同時にアタッチすることはできません。  新しい設計では、Standard SKU リソースの使用を検討する必要があります。  詳しくは、[Standard ロード バランサー](../../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)に関するページをご覧ください。

## <a name="ip-address-assignment"></a>IP アドレスの割り当て

Standard パブリック IPv4、Basic パブリック IPv4、および Standard パブリック IPv6 アドレスのすべてで、**静的** な割り当てがサポートされます。  リソースには、作成時に IP アドレスが割り当てられます。 この IP アドレスは、リソースが削除されたときに解放されます。  

> [!NOTE]
> 割り当て方法を **静的** に設定しても、パブリック IP アドレス リソースに割り当てられる実際の IP アドレスは指定できません。 リソースが作成される Azureの場所で使用可能な IP アドレスのプールから IP アドレスが割り当てられます。
>

次のようなシナリオでは、静的なパブリック IP アドレスが一般的に使用されます。

* Azure リソースと通信するためにファイアウォール規則を更新する必要がある。
* DNS 名の解決で、IP アドレスの変更によりレコードを更新する必要がある。
* Azure のリソースが、IP アドレス ベースのセキュリティ モデルを使用する他のアプリまたはサービスと通信する。
* IP アドレスにリンクされている TLS/SSL 証明書を使用する。

Basic パブリック IPv4 および IPv6 アドレスでは、**動的** な割り当てがサポートされます。  "動的" を選択すると、IP アドレスは作成時にリソースに割り当てられ **ません**。  IP が割り当てられるのは、パブリック IP アドレスをリソースに関連付けるときです。 IP アドレスは、リソースを停止または削除すると解放されます。   たとえば、パブリック IP リソースが **リソース A** という名前のリソースから解放されます。**リソース A** は、パブリック IP リソースが再割り当てされる場合、起動時に別の IP アドレスを受信します。 関連付けられた IP アドレスは、割り当て方法が **静的** から **動的** に変更されると解放されます。 関連付けられた IP アドレスは、割り当て方法が **動的** から **静的** に変更された場合、変更されません。 IP アドレスが変わらないようにするため、割り当て方法を **静的** に設定します。

> [!NOTE]
> Azure では、各 Azure クラウドの各リージョンに一意の範囲からパブリック IP アドレスが割り当てられます。 Azure [Public](https://www.microsoft.com/download/details.aspx?id=56519)、[US Government](https://www.microsoft.com/download/details.aspx?id=57063)、[China](https://www.microsoft.com/download/details.aspx?id=57062)、および [Germany](https://www.microsoft.com/download/details.aspx?id=57064) クラウドの範囲 (プレフィックス) の一覧をダウンロードできます。
>

## <a name="dns-name-label"></a>DNS 名ラベル

このオプションを選択して、パブリック IP リソースの DNS ラベルを指定します。 この機能は、IPv4 アドレス (32 ビット A レコード) と IPv6 アドレス (128 ビット AAAA レコード) の両方で機能します。  この選択により、**domainnamelabel**.**location**.cloudapp.azure.com から Azure で管理される DNS のパブリック IP へのマッピングが作成されます。 

たとえば、次のようなパブリック IP を作成します。

* **domainnamelabel** が **contoso**
* Azure の **location** が **West US**

完全修飾ドメイン名 (FQDN) **contoso.westus.cloudapp.azure.com** は、リソースのパブリック IP アドレスに解決されます。

> [!IMPORTANT]
> 作成された各ドメイン名ラベルは、Azure の location 内で一意である必要があります。  

パブリック IP を使用するサービスにカスタム ドメインが望まれる場合、DNS レコードに [Azure DNS](../../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) または外部 DNS プロバイダーを使用できます。

## <a name="other-public-ip-address-features"></a>パブリック IP アドレスのその他の機能

パブリック IP アドレスに使用できるその他の属性があります。  

* グローバル **階層** では、パブリック IP アドレスをリージョン間ロード バランサーで使用できるようになります。 
* インターネットの **[ルーティングの優先設定]** オプションでは、トラフィックが Microsoft ネットワークに費やす時間を最小限に抑え、エグレス データ転送コストを削減します。

> [!NOTE]
> この時点で、**階層** と **ルーティング設定** の両方の機能は Standard SKU IPv4 アドレスのみで使用できます。  また、同じ IP アドレスで同時に使用することはできません。
>

[!INCLUDE [ephemeral-ip-note.md](../../../includes/ephemeral-ip-note.md)]

## <a name="limits"></a>制限 

IP アドレス指定に対する制限は、Azure の[ネットワークの制限](../../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)の完全なセットに示されています。 この制限は、リージョンとサブスクリプションごとに存在します。 ビジネス ニーズに基づいて既定の制限を超えて増やすには、[サポートにお問い合わせください](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="pricing"></a>価格

パブリック IP アドレスには、わずかですが料金が発生します。 Azure での IP アドレスの料金の詳細については、「[IP アドレスの料金](https://azure.microsoft.com/pricing/details/ip-addresses)」ページをご覧ください。

## <a name="limitations-for-ipv6"></a>IPv6 の制限事項

* VPN ゲートウェイは、IPv6 が有効になっている仮想ネットワークで、直接的にも、"UseRemoteGateway" とのピアリングでも使用できません。
* パブリック IPv6 アドレスは、4 分のアイドル タイムアウトでロックされます。
* Azure では、コンテナーの IPv6 通信はサポートされていません。
* IPv6 専用仮想マシンまたは仮想マシン スケール セットの使用はサポートされていません。 各 NIC には、少なくとも 1 つの IPv4 IP 構成を含める必要があります (デュアルスタック)。
* 既存の IPv4 デプロイに IPv6 を追加する場合、既存のリソース ナビゲーション リンクがある仮想ネットワークに IPv6 範囲を追加することはできません。
* IPv6 の正引き DNS は Azure パブリック DNS でサポートされています。 逆引き DNS はサポートされていません。
* ルーティング設定およびリージョン間負荷分散はサポートされていません。

Azure での IPv6 の詳細については、[こちら](ipv6-overview.md)を参照してください。

## <a name="next-steps"></a>次のステップ
* [Azure でのプライベート IP アドレス](private-ip-addresses.md)について学習する
* [Azure ポータルを使用して静的パブリック IP を持つ VM をデプロイする](./virtual-network-deploy-static-pip-arm-portal.md)