---
title: Azure Application Gateway に関してよく寄せられる質問
description: このページでは、Azure Application Gateway に関してよく寄せられる質問の回答を紹介します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/6/2018
ms.author: victorh
ms.openlocfilehash: 9cb14e5076379e5095ca88dc749a954e9e5d5aa4
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53994860"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Application Gateway に関してよく寄せられる質問

## <a name="general"></a>全般

### <a name="what-is-application-gateway"></a>Application Gateway とは

Azure Application Gateway は、アプリケーション配信コントローラー (ADC) をサービスとして提供することで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。 また、Azure で完全に管理されるスケーラブルな高可用性サービスを提供します。

### <a name="what-features-does-application-gateway-support"></a>Application Gateway はどのような機能をサポートしますか?

Application Gateway は、自動スケーリング、SSL オフロードとエンド ツー エンド SSL、Web アプリケーション ファイアウォール、Cookie ベースのセッション アフィニティ、URL パス ベースのルーティング、複数サイトのホスティングなどをサポートします。 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」をご覧ください。

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Application Gateway と Azure Load Balancer の違いは何ですか?

Application Gateway はレイヤー 7 のロード バランサーです。つまり、Web トラフィックのみ (HTTP/HTTPS/WebSocket) で機能します。 また、SSL ターミネーション、Cookie ベースのセッション アフィニティ、ラウンド ロビンによるトラフィックの負荷分散などの機能をサポートします。 Load Balancer は、レイヤー 4 (TCP/UDP) でトラフィックを負荷分散します。

### <a name="what-protocols-does-application-gateway-support"></a>Application Gateway はどのようなプロトコルをサポートしますか?

Application Gateway は、HTTP、HTTPS、HTTP/2、WebSocket をサポートします。

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway は HTTP/2 をどのようにサポートしますか?

HTTP/2 プロトコルのサポートを利用できるのは、アプリケーション ゲートウェイ リスナーに接続しているクライアントだけです。 バックエンド サーバー プールへの通信は、HTTP/1.1 で行われます。 

既定では、HTTP/2 のサポートは無効になっています。 これを有効にする Azure PowerShell コード スニペットの例を次に示します。

```
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm
$gw.EnableHttp2 = $true
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>現在、バックエンド プールの一部としてどのようなリソースがサポートされますか?

バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 Application Gateway のバックエンド プールのメンバーは、可用性セットに関連付けられていません。 バックエンド プールのメンバーは、IP 接続されている限り、クラスターおよびデータ センター間、または Azure の外部に配置できます。

### <a name="what-regions-is-the-service-available-in"></a>このサービスは、どのリージョンで利用できますか?

Application Gateway は、グローバル Azure のすべてのリージョンで利用できます。 また、[Azure China](https://www.azure.cn/) と [Azure Government](https://azure.microsoft.com/overview/clouds/government/) で利用することもできます。

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>Application Gateway はサブスクリプション専用のデプロイメントですか? または、複数の顧客と共有されますか?

Application Gateway は、お客様の仮想ネットワーク専用のデプロイメントです。

### <a name="is-http-https-redirection-supported"></a>HTTP から HTTPS へのリダイレクトはサポートされていますか?

リダイレクトはサポートされます。 詳しくは、「[Application Gateway のリダイレクトの概要](application-gateway-redirect-overview.md)」をご覧ください。

### <a name="in-what-order-are-listeners-processed"></a>リスナーはどのような順序で処理されますか?

リスナーは、表示される順序で処理されます。 そのため、基本リスナーが着信要求と一致する場合は、基本リスナーが要求を最初に処理します。  トラフィックが正しいバックエンドにルーティングされるようにするには、基本リスナーの前にマルチサイト リスナーを構成する必要があります。

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Application Gateway の IP と DNS はどこで確認できますか?

パブリック IP アドレスをエンドポイントとして使用する場合は、パブリック IP アドレス リソースまたはポータルにあるアプリケーション ゲートウェイの[概要] ページでこの情報を確認できます。 内部 IP アドレスの場合は、[概要] ページで確認できます。

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>IP または DNS 名は Application Gateway の有効期間内に変更されますか?

アプリケーション ゲートウェイが停止され起動されると、VIP が変更される可能性があります。 アプリケーション ゲートウェイに関連付けられた DNS は、そのゲートウェイのライフサイクル全体を通して変更されません。 そのため、CNAME エイリアスを使用してアプリケーション ゲートウェイの DNS アドレスを参照することをお勧めします。

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway は静的 IP をサポートしますか?

はい、Application Gateway v2 SKU では、静的パブリック IP アドレスがサポートされます。 v1 SKU では、静的内部 IP アドレスがサポートされます。

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway は複数のパブリック IP をサポートしますか?

アプリケーション ゲートウェイでサポートされるパブリック IP アドレスは 1 つだけです。

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Application Gateway のサブネットはどのくらい大きくする必要がありますか?

Application Gateway は、インスタンスごとに 1 つのプライベート IP アドレスを使用します。さらに、プライベート フロントエンド IP 構成を使用している場合は、もう 1 つのプライベート IP アドレスを使用します。 また、Azure は、内部使用のために、各サブネットの最初の 4 個の IP アドレスと最後の IP アドレスを予約しています。
たとえば、アプリケーション ゲートウェイが 3 つのインスタンスに設定され、プライベート フロントエンド IP が設定されていない場合は、/29 サブネット サイズ以上が必要です。 この場合、アプリケーション ゲートウェイは 3 つの IP アドレスを使用します。 プライベート フロントエンド IP 構成用に 3 つのインスタンスと 1 つの IP アドレスがある場合は、4 つの IP アドレスが必要なため、/28 サブネット以上のサイズが必要です。

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Q. 1 つのサブネットに複数の Application Gateway リソースをデプロイできますか?

はい、特定の Application Gateway の複数のインスタンスのデプロイに加え、別の Application Gateway リソースを含む既存のサブネットに別の一意の Application Gateway リソースをプロビジョニングできます。

同じサブネット上の Standard_v2 と Standard Application Gateway の混在はサポートされていません。 さらに、自動スケールを有効にすると、1 つのサブネットには 1 つのアプリケーション ゲートウェイだけが存在できます。

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway は x-forwarded-for ヘッダーをサポートしますか?

はい。Application Gateway は、バックエンドに転送される要求に x-forwarded-for、x-forwarded-proto、および x-forwarded-port ヘッダーを挿入します。 x-forwarded-for ヘッダーの形式は、"IP:ポート" のコンマ区切りリストです。 x-forwarded-proto の有効な値は http または https です。 x-forwarded-port は、要求がアプリケーション ゲートウェイに到達するポートを指定します。

Application Gateway は、要求が到達した元の Host ヘッダーを含む X-Original-Host ヘッダーも挿入します。 このヘッダーは、トラフィックがバックエンドにルーティングされる前に受信ホスト ヘッダーが変更される、Azure Web サイト統合などのシナリオで役立ちます。

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Application Gateway のデプロイにはどのくらい時間がかかりますか? 更新中にも Application Gateway は動作しますか?

新しい Application Gateway v1 SKU のデプロイでは、プロビジョニングに最大 20 分かかります。 インスタンス サイズ/数の変更は中断を伴わず、ゲートウェイはこの時間にはアクティブなままです。

V2 SKU のデプロイは、プロビジョニングに約 5 分から 6 分かかります。

Application Gateway は x-forwarded-for ヘッダーをサポートしますか?

## <a name="configuration"></a>構成

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway は常に仮想ネットワークにデプロイされますか?

はい。Application Gateway は常に仮想ネットワーク サブネットにデプロイされます。 このサブネットには Application Gateway のみを含めることができます。

### <a name="can-application-gateway-communicate-with-instances-outside-its-virtual-network"></a>Application Gateway はその仮想ネットワークの外部にあるインスタンスと通信できますか?

Application Gateway は IP 接続がある限り、仮想ネットワークの外部にあるインスタンスと通信できます。 内部 IP をバックエンド プールのメンバーとして使用する場合は、[VNET ピアリング](../virtual-network/virtual-network-peering-overview.md)または [VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) が必要です。

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>アプリケーション ゲートウェイ サブネット内に何か他にデプロイできるものはありますか?

いいえ。ただし、サブネット内に他のアプリケーション ゲートウェイをデプロイすることはできます。

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>ネットワーク セキュリティ グループはアプリケーション ゲートウェイ サブネットでサポートされますか?

ネットワーク セキュリティ グループ (NSG) はアプリケーション ゲートウェイ サブネットでサポートされますが、次の制約があります。

* Application Gateway v1 SKU ではポート 65503 から 65534 の、v2 SKU ではポート 65200 から 65535 の受信トラフィックを例外にする必要があります。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに対する変更を開始することはできません。

* 送信インターネット接続はブロックできません。

* AzureLoadBalancer タグからのトラフィックを許可する必要があります。

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>ユーザー定義ルートは Application Gateway サブネットでサポートされますか?

ユーザー定義ルート (UDR) は、エンドツーエンドの要求/応答の通信を変えないかぎり、Application Gateway サブネットでサポートされます。

たとえば、パケットの検査のためにファイアウォール アプリケーションを指す Application Gateway サブネットに UDR をセットアップできますが、パケットが意図した宛先の後検査にリーチできることを確認する必要があります。 これに失敗すると、不適切な正常性プローブやトラフィック ルーティング動作が発生する場合があります。 これには仮想ネットワークの ExpressRoute や VPN Gateway によってプロパゲートされる学習済みのルートまたは既定の 0.0.0.0/0 ルートが含まれます。

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Application Gateway にはどのような制限がありますか? これらの制限値を引き上げることはできますか?

制限については、「[Application Gateway の制限](../azure-subscription-service-limits.md#application-gateway-limits)」をご覧ください。

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Application Gateway を外部と内部の両方のトラフィックに同時に使用できますか?

はい。Application Gateway では、アプリケーション ゲートウェイごとに 1 つの内部 IP と 1 つの外部 IP をサポートできます。

### <a name="is-vnet-peering-supported"></a>VNET ピアリングはサポートされていますか?

はい。VNET ピアリングはサポート対象であり、他の仮想ネットワークにおけるトラフィックの負荷分散に適しています。

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>ExpressRoute または VPN トンネルによって接続されているオンプレミスのサーバーと通信できますか?

はい。トラフィックが許可されている限り、通信可能です。

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>1 つのバックエンド プールが複数のポートで多数のアプリケーションを提供することはできますか?

マイクロ サービス アーキテクチャがサポートされています。 複数のポートでプローブを実行するには、複数の HTTP 設定を構成する必要があります。

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>カスタム プローブは応答データでワイルドカード/正規表現をサポートしますか?

カスタム プローブは応答データでワイルドカードまたは正規表現をサポートしません。

### <a name="how-are-rules-processed"></a>ルールはどのように処理されますか?

ルールは、構成されている順序で処理されます。 マルチサイト ルールが評価される前にポートに基づいて基本ルールがトラフィックと一致することでトラフィックが不適切なバックエンドにルーティングされる可能性を下げるため、基本ルールの前にマルチサイト ルールを構成することをお勧めします。

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>カスタム プローブの [ホスト] フィールドは何を表しますか?

[ホスト] フィールドは、プローブの送信先の名前を指定します。 Application Gateway でマルチサイトが構成されている場合にのみ適用されます。それ以外の場合は、"127.0.0.1" を使用します。 この値は VM ホスト名とは異なり、\<プロトコル\>://\<ホスト\>:\<ポート\>\<パス\> という形式になります。

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Application Gateway アクセスを少数のソース IP に限定できますか?

このシナリオは、アプリケーション ゲートウェイ サブネットの NSG を使用して行うことができます。 次の制約を次の優先順位でサブネットに適用する必要があります。

* ソース IP と IP 範囲からの着信トラフィックを許可します。

* [バックエンド ヘルス通信](application-gateway-diagnostics.md)用にポート 65503 ~ 65534 のすべてのソースからの着信要求を許可します。 このポート範囲は、Azure インフラストラクチャの通信に必要です。 これらのポートは、Azure の証明書によって保護 (ロックダウン) されます。 対象のゲートウェイの顧客を含め、適切な証明書を持たない外部エンティティは、これらのエンドポイントに対する変更を開始することはできません。

* [NSG](../virtual-network/security-overview.md)で着信 Azure Load Balancer プローブ (AzureLoadBalancer タグ) と受信仮想ネットワーク トラフィック (VirtualNetwork タグ) を許可します。

* すべて拒否ルールを使用して、その他すべての着信トラフィックをブロックします。

* インターネットのすべての宛先への送信トラフィックを許可します。

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>パブリック側のリスナーとプライベート側のリスナーの両方に同じポートを使用できますか?

いいえ、これはサポートされていません。

## <a name="performance"></a>パフォーマンス

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Application Gateway は高可用性とスケーラビリティをどのようにサポートしますか?

2 つ以上のインスタンスをデプロイすると、Application Gateway v1 SKU は高可用性のシナリオをサポートします。 Azure は、これらのインスタンスを更新ドメインと障害ドメインに分散して、すべてのインスタンスで同時に障害が発生しないようにします。 v1 SKU では、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによってスケーラビリティをサポートします。

v2 SKU では、新しいインスタンスが障害ドメインと更新ドメインに自動的に分散されるようにします。 ゾーンの冗長性が選択されている場合は、ゾーン障害回復性を提供するために、最新のインスタンスが可用性ゾーンにも分散されます。

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Application Gateway を使用して複数のデータ センター間で障害復旧のシナリオを実現するにはどうすればよいですか?

お客様は、Traffic Manager を使用して、異なるデータ センターにある複数の Application Gateway にトラフィックを分散できます。

### <a name="is-autoscaling-supported"></a>自動スケールはサポートされていますか?

はい、Application Gateway v2 SKU では、自動スケールをサポートします。 詳細については、「[自動スケールとゾーン冗長 Application Gateway (パブリック プレビュー)](application-gateway-autoscaling-zone-redundant.md)」を参照してください。

### <a name="does-manual-scale-updown-cause-downtime"></a>手動でのスケールアップまたはスケールダウンによってダウンタイムが発生しますか?

ダウンタイムは発生しません。アップグレード ドメインと障害ドメインにインスタンスが分散されます。

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway は接続のドレインに対応していますか?

はい。 中断することなくバックエンド プール内のメンバーを変更するように接続のドレインを構成できます。 そのため、既存の接続がある場合、その接続が閉じられるか、構成可能なタイムアウトに達するまで、以前の宛先に送信し続けることができます。 接続のドレインは、現在処理中の接続が完了するまで待機するだけです。 Application Gateway は、アプリケーションのセッション状態を認識しません。

### <a name="what-are-application-gateway-sizes"></a>Application Gateway のサイズは何ですか?

現在、Application Gateway は、**Small**、**Medium**、**Large** の 3 つのサイズで提供されています。 Small サイズのインスタンスは、開発用およびシナリオのテスト用です。

アプリケーション ゲートウェイの制限の詳細な一覧については、[Application Gateway サービスの制限](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits)に関するページをご覧ください。

次の表では、SSL オフロードが有効になっているアプリケーション ゲートウェイ インスタンスごとにパフォーマンス スループットの平均値を示します。

| バックエンド ページの平均応答サイズ | Small | Medium | Large |
| --- | --- | --- | --- |
| 6 KB |7.5 Mbps |13 Mbps |50 Mbps |
| 100 KB |35 Mbps |100 Mbps |200 Mbps |

> [!NOTE]
> これらの値は、アプリケーション ゲートウェイ スループットのおおよその値です。 実際のスループットは、平均ページ サイズ、バックエンド インスタンスの場所、ページの処理時間など、さまざまな環境の違いによって異なります。 パフォーマンス面の正確な数値は、ご自身でテストを実施のうえご確認ください。 ここに挙げた数値は、容量計画の参考とすることを目的として記載したものにすぎません。

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>インスタンスを中断せずにサイズを中から大に変更できますか?

はい。Azure は、インスタンスを更新ドメインと障害ドメインに分散して、すべてのインスタンスで同時に障害が発生しないようにします。 Application Gateway は、同じゲートウェイの複数のインスタンスを追加して負荷を共有することによるスケーリングをサポートします。

## <a name="ssl-configuration"></a>SSL の構成

### <a name="what-certificates-are-supported-on-application-gateway"></a>Application Gateway ではどの証明書がサポートされますか?

自己署名証明書、CA 証明書、およびワイルドカード証明書がサポートされています。 EV 証明書はサポートされていません。

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Application Gateway でサポートされている最新の暗号スイートはどれですか?

Application Gateway でサポートされている最新の暗号スイートは次のとおりです。 SSL オプションのカスタマイズ方法については、「[Configure SSL policy versions and cipher suites on Application Gateway](application-gateway-configure-ssl-policy-powershell.md)」(Application Gateway で SSL ポリシーのバージョンと暗号スイートを構成する) をご覧ください。

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Application Gateway ではバックエンドへのトラフィックの再暗号化もサポートされていますか?

はい。Application Gateway は SSL オフロードとエンド ツー エンド SSL をサポートしており、バックエンドへのトラフィックが再暗号化されます。

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>SSL プロトコルのバージョンを管理する SSL ポリシーを構成できますか?

はい。TLS1.0、TLS1.1、および TLS1.2 を拒否するように Application Gateway を構成できます。 SSL 2.0 および 3.0 は既定で無効になっているため、構成できません。

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>暗号スイートおよびポリシーの順序は構成できますか?

はい、[暗号スイートの構成](application-gateway-ssl-policy-overview.md)はサポートされています。 カスタム ポリシーを定義する際、次の暗号スイートを 1 つ以上有効にする必要があります。 アプリケーション ゲートウェイでは、バックエンドの管理に SHA256 を使用します。

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>いくつの SSL 証明書がサポートされていますか?

サポートされている SSL 証明書は最大 20 個です。

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>バックエンドの再暗号化用として、いくつの認証証明書がサポートされていますか?

サポートされている認証証明書は最大 10 個 (既定値は 5 個) です。

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Application Gateway は Azure Key Vault とネイティブに統合されますか?

いいえ。Azure Key Vault とは統合されません。

## <a name="web-application-firewall-waf-configuration"></a>Web Application Firewall (WAF) の構成

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>WAF SKU は、Standard SKU で使用可能なすべての機能を提供しますか?

はい。WAF は標準 SKU に含まれるすべての機能をサポートします。

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Application Gateway がサポートするのはどの CRS バージョンですか?

Application Gateway は CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) と CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) をサポートします。

### <a name="how-do-i-monitor-waf"></a>WAF を監視するにはどうすればよいですか?

WAF は診断ログを通じて監視されます。診断ログについて詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

### <a name="does-detection-mode-block-traffic"></a>検出モードではトラフィックがブロックされますか?

いいえ。検出モードは、WAF ルールをトリガーしたトラフィックをログに記録するだけです。

### <a name="can-i-customize-waf-rules"></a>WAF ルールはカスタマイズできますか?

はい、WAF ルールはカスタマイズできます。 詳しくは、[WAF ルール グループとルールのカスタマイズ](application-gateway-customize-waf-rules-portal.md)に関するページをご覧ください

### <a name="what-rules-are-currently-available"></a>現在使用できるのはどのようなルールですか?

現在、WAF は CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) と CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30) をサポートしています。CRS 2.2.9 と CRS 3.0 は、Open Web Application Security Project (OWASP) が特定した 10 の脆弱性 ([OWASP Top 10 の脆弱性](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013)に関するページをご覧ください) のほとんどに対するベースライン セキュリティを提供します。

* SQL インジェクションからの保護

* クロス サイト スクリプティングからの保護

* 一般的な Web 攻撃からの保護 (コマンド インジェクション、HTTP 要求スマグリング、HTTP レスポンス スプリッティング、リモート ファイル インクルード攻撃など)

* HTTP プロトコル違反に対する保護

* HTTP プロトコル異常に対する保護 (ホスト ユーザー エージェントと承認ヘッダーが見つからない場合など)

* ボット、クローラー、スキャナーの防止

* 一般的なアプリケーション構成ミスの検出 (Apache、IIS など)

### <a name="does-waf-also-support-ddos-prevention"></a>WAF は DDoS 防止もサポートしますか?

はい。 アプリケーション ゲートウェイがデプロイされている VNet 上で DDos Protection を有効にすることができます。 これにより、Azure DDos Protection サービスを使用してアプリケーション ゲートウェイ VIP も確実に保護されます。

## <a name="diagnostics-and-logging"></a>診断とログ

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Application Gateway ではどのような種類のログを使用できますか?

Application Gateway で使用できるログは 3 つあります。 これらのログとその他の診断機能について詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

* **ApplicationGatewayAccessLog** - アクセス ログには、アプリケーション ゲートウェイ フロントエンドに送信された各要求が格納されます。 このデータには、呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。アクセス ログは 300 秒ごとに収集されます。 このログには、アプリケーション ゲートウェイのインスタンスごとに 1 つのレコードが含まれます。
* **ApplicationGatewayPerformanceLog** - パフォーマンス ログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。
* **ApplicationGatewayFirewallLog** - ファイアウォール ログには、Web アプリケーション ファイアウォールが構成されたアプリケーション ゲートウェイの、検出モードまたは防止モードでログに記録された要求が含まれます。

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>バックエンド プールのメンバーが正常かどうかを確認するにはどうすればよいですか?

PowerShell コマンドレット `Get-AzureRmApplicationGatewayBackendHealth` を使用するか、またはポータルから正常性を確認できます (「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください)。

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>診断ログにおける保持ポリシーとは何ですか?

診断ログはお客様のストレージ アカウントに送信され、お客様はログの優先順位に基づいて保持ポリシーを設定できます。 診断ログをイベント ハブまたは Log Analytics に送信することもできます。 詳しくは、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事をご覧ください。

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Application Gateway の監査ログを取得するにはどうすればよいですか?

Application Gateway では監査ログを使用できます。 ポータルで、アプリケーション ゲートウェイのメニュー ブレードの **[アクティビティ ログ]** をクリックして監査ログにアクセスします。 

### <a name="can-i-set-alerts-with-application-gateway"></a>Application Gateway でアラートを設定できますか?

はい、Application Gateway では、アラートをサポートしています。 メトリックに関するアラートが構成されます。 Application Gateway のメトリックの詳細については、[Application Gateway のメトリック](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics)に関する記事を参照してください。 アラートについて詳しくは、「[アラート通知の受信](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)」をご覧ください。

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Application Gateway のトラフィック統計情報を分析するにはどうすればよいですか?

Azure Log Analytics、Excel、Power BI などのさまざまなメカニズムを使用して、アクセス ログを表示および分析することができます。

Microsoft は、一般的な [GoAccess](https://goaccess.io/) ログ アナライザーをインストールし、Application Gateway アクセス ログに対して実行する、Resource Manager テンプレートも発行しています。 GoAccess では、ユニーク ビジター、要求されたファイル、ホスト、オペレーティング システム、ブラウザー、HTTP 状態コードなど、重要な HTTP トラフィック統計情報が提供されます。 詳細については、[GitHub の Resource Manager テンプレート フォルダーにある Readme ファイル](https://aka.ms/appgwgoaccessreadme)を参照してください。

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>バックエンドの正常性から不明な状態が返されるのですが、この状態はどのような原因が考えられますか?

最も一般的な理由は、バックエンドへのアクセスが NSG またはカスタム DNS によってブロックされていることです。 詳しくは、「[Application Gateway のバックエンドの正常性、診断ログ、およびメトリック](application-gateway-diagnostics.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

Application Gateway の詳細については、「[Azure Application Gateway とは](overview.md)」を参照してください。
