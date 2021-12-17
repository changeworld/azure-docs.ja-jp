---
title: Private Link のセットアップ設計
description: Private Link のセットアップ設計
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: 6bc664d5f7dab7d000f1ea540db56e240c307e15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006660"
---
# <a name="design-your-private-link-setup"></a>Private Link のセットアップ設計

Azure Monitor Private Link を設定する前に、ネットワーク トポロジ、特に DNS ルーティング トポロジを検討してください。
「[しくみ](./private-link-security.md#how-it-works)」で説明されているように、Private Link の設定はすべての Azure Monitor リソースへのトラフィックに影響します。 これは特に、Application Insights リソースに当てはまります。 この設定は、プライベート エンドポイントに接続されているネットワークだけでなく、同じ DNS を共有している他のすべてのネットワークにも影響します。

最もシンプルで最も安全なのは次のアプローチです。
1. 単一のプライベート エンドポイントと単一の AMPLS を指定して、単一の Private Link 接続を作成します。 ネットワークがピアリングされている場合は、共有 (またはハブ) VNet 上に Private Link 接続を作成します。
2. "*すべての*" Azure Monitor リソース (Application Insights コンポーネントおよび Log Analytics ワークスペース) をその AMPLS に追加します。
3. ネットワーク エグレス トラフィックを可能な限りブロックします。


## <a name="plan-by-network-topology"></a>ネットワーク トポロジ別の計画

### <a name="guiding-principle-avoid-dns-overrides-by-using-a-single-ampls"></a>基本原則: 単一の AMPLS を使用して DNS のオーバーライドを回避する
一部のネットワークは、複数の VNet または接続された他のネットワークで構成されます。 これらのネットワークで同じ DNS を共有している場合は、これらのいずれかで Private Link を設定すると、DNS が更新され、すべてのネットワークのトラフィックに影響します。

次の図では、VNet 10.0.1.x が最初に AMPLS1 に接続され、Azure Monitor のグローバル エンドポイントがその範囲の IP にマップされます。 その後、VNet 10.0.2.x が AMPLS2 に接続され、"**同じグローバル エンドポイント**" の DNS マッピングがその範囲の IP でオーバーライドされます。 これらの VNet はピアリングされていないため、最初の VNet はこれらのエンドポイントに接続できません。

この競合を回避するには、DNS ごとに AMPLS オブジェクトを 1 つだけ作成します。

![複数の VNet での DNS オーバーライドの図](./media/private-link-security/dns-overrides-multiple-vnets.png)


### <a name="hub-and-spoke-networks"></a>ハブアンドスポーク ネットワーク
ハブアンドスポーク ネットワークでは、各スポーク VNet 上ではなくハブ (メイン) ネットワーク上に設定された単一の Private Link 接続を使用する必要があります。 

![ハブアンドスポークのシングル PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> たとえば、各 VNet が限られた一連の監視リソースにアクセスできるようにするために、スポーク VNet 用に個別の Private Link を作成することをお勧めします。 このような場合は、VNet ごとに専用のプライベート エンドポイントと AMPLS を作成できますが、**DNS がオーバーライドされないように、同じ DNS ゾーンが共有されていないことも確認する必要があります**。

### <a name="peered-networks"></a>ピアリングされたネットワーク
ネットワーク ピアリングは、ハブスポーク以外のさまざまなトポロジで使用されます。 このようなネットワークは、互いの IP アドレスに到達でき、多くの場合は同一の DNS を共有しています。 このような場合は、もう一度、他のネットワークからアクセス可能なネットワーク上に単一のプライベート リンクを作成することをお勧めします。 複数のプライベート エンドポイントや AMPLS オブジェクトを作成することは避けてください。これは、最終的には DNS に含まれる最後のセットのみが適用されるためです。

### <a name="isolated-networks"></a>分離ネットワーク
ネットワークがピアリングされていない場合は、**プライベート リンクを使用するために DNS も分離する必要があります**。 その後、ネットワークごとに個別のプライベート エンドポイントと、個別の AMPLS オブジェクトを作成します。 AMPLS オブジェクトは、同じワークスペースやコンポーネントにリンクすることも、異なるワークスペースやコンポーネントにリンクすることもできます。

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>ローカルでのテスト: DNS ではなくマシンのホスト ファイルを編集する 
ネットワーク上の他のクライアントに影響を与えずにプライベート リンクをローカルでテストするには、プライベート エンドポイントを作成するときに DNS を更新しないようにしてください。 代わりに、お使いのマシン上の hosts ファイルを編集して、Private Link エンドポイントに要求が送信されるようにします。
* Private Link を設定しますが、プライベート エンドポイントに接続する際は、DNS と自動統合 **しない** ことを選択します (手順 5b)。
* マシンのホスト ファイルで関連するエンドポイントを構成します。 マッピングが必要な Azure Monitor エンドポイントを確認するには、「[エンドポイントの DNS 設定の確認](./private-link-configure.md#reviewing-your-endpoints-dns-settings)」を参照してください。

このアプローチは、運用環境にはお勧めしません。

## <a name="control-how-private-links-apply-to-your-networks"></a>プライベート リンクをネットワークに適用する方法を制御する
(2021 年 9 月に導入された) Private Link アクセス モードを使用すると、プライベート リンクがネットワーク トラフィックに与える影響を制御できます。 これらの設定は、AMPLS オブジェクト (接続されているすべてのネットワークに影響が及びます)、またはそこに接続されている特定のネットワークに適用できます。

適切なアクセス モードを選択すると、ネットワーク トラフィックに悪影響が及びます。 これらの各モードは、インジェストとクエリに対して個別に設定できます。

* プライベートのみ - VNet は、Private Link リソース (AMPLS 内のリソース) のみに到達できます。 これは最も安全な作業モードであり、データ流出を防ぎます。 これを実現するために、AMPLS の外部にある Azure Monitor リソースへのトラフィックはブロックされます。
![AMPLS の "プライベートのみ" アクセス モードの図](./media/private-link-security/ampls-private-only-access-mode.png)
* オープン - VNet は、Private Link リソースと、AMPLS にないリソースの両方に到達できます (それらが[公衆ネットワークからのトラフィックを受け入れる場合](./private-link-design.md#control-network-access-to-your-resources))。 "オープン" アクセス モードでは、データ流出を防ぐことはできませんが、Private Link の他の恩恵が得られます。つまり、Private Link リソースへのトラフィックは、プライベート エンドポイントを介して送信され、検証され、Microsoft バックボーン経由で送信されます。 "オープン" モードは、混合モードの作業 (一部のリソースにパブリックにアクセスし、他のリソースにプライベート リンクを介してアクセスする)、または段階的なオンボーディング プロセス中に役立ちます。
![AMPLS の "オープン" アクセス モードの図](./media/private-link-security/ampls-open-access-mode.png) アクセス モードは、インジェストとクエリに対して個別に設定されます。 たとえば、インジェストには "プライベートのみ" モードを設定しつつ、クエリには "オープン" モードを設定することができます。

アクセス モードは慎重に選択してください。 プライベート専用アクセス モードを使用すると、サブスクリプションまたはテナントに関係なく、同じ DNS を共有しているすべてのネットワークで AMPLS 内ではないリソースへのトラフィックがブロックされます (以下で説明する Log Analytics インジェスト要求を除く)。 すべての Azure Monitor リソースを AMPLS に追加できない場合、まず、一部のリソースを追加し、オープン アクセス モードを適用します。 "*すべて*" の Azure Monitor リソースを AMPLS に追加した後で "プライベートのみ" モードに切り替えると、最大のセキュリティを確保できます。

構成の詳細と例については、「[API とコマンド ラインの使用](./private-link-configure.md#use-apis-and-command-line)」を参照してください。

> [!NOTE]
> Log Analytics インジェストではリソース固有のエンドポイントが使用されます。 そのため、AMPLS アクセス モードに準拠しません。 **Log Analytics インジェスト要求で AMPLS の外部にあるワークスペースにアクセスできないようにするには、AMPLS アクセス モードには関係なく、パブリック エンドポイントへのトラフィックをブロックするようにネットワーク ファイアウォールを設定します**。

### <a name="setting-access-modes-for-specific-networks"></a>特定のネットワークのアクセス モードの設定
AMPLS リソースに設定されたアクセス モードは、すべてのネットワークに影響します。ただし、特定のネットワークに対してこれらの設定をオーバーライドすることができます。

次の図では、VNet1 は "オープン" モードを使用し、VNet2 は "プライベートのみ" モードを使用しています。 その結果、VNet1 からの要求は、Workspace1 と Component2 にはプライベート リンク経由で到達でき、Component3 にはプライベート リンクを経由せずに到達できます (それが[公衆ネットワークからのトラフィックを受け入れる場合](./private-link-design.md#control-network-access-to-your-resources))。 これに対して、VNet2 の要求は Component3 に到達できません。 
![混合アクセス モードの図](./media/private-link-security/ampls-mixed-access-modes.png)


## <a name="consider-ampls-limits"></a>AMPLS の制限を考慮する
AMPLS オブジェクトには次の制限があります。
* 1 つの VNet は、**1 つ** の AMPLS オブジェクトにのみ接続できます。 これは、その AMPLS オブジェクトで、VNet でアクセスできる必要のあるすべての Azure Monitor リソースへのアクセスを提供する必要があることを意味します。
* 1 つの AMPLS オブジェクトは、最大 50 個の Azure Monitor リソースに接続できます。
* 1 つの Azure Monitor リソース (ワークスペースまたは Application Insights コンポーネント) は、最大 5 つの AMPLS に接続できます。
* 1 つの AMPLS オブジェクトは、最大 10 個のプライベート エンドポイントに接続できます。

次の図では、
* 各 VNet は **1 つ** の AMPLS オブジェクトにのみ接続します。
* AMPLS A は、使用可能な Azure Monitor リソース接続 50 個のうち 3 個を使用して、2 つのワークスペースと 1 つの Application Insights コンポーネントに接続します。
* ワークスペース 2 は、使用可能な 5 個の AMPLS 接続のうち 2 個を使用して、AMPLS A と AMPLS B に接続します。
* AMPLS B は、使用可能な 10 個のプライベート エンドポイント接続のうち 2 個を使用して、2 つの VNet (VNet2 と VNet3) のプライベート エンドポイントに接続します。

![AMPLS の制限に関する図](./media/private-link-security/ampls-limits.png)


## <a name="control-network-access-to-your-resources"></a>リソースへのネットワーク アクセスを制御する
Log Analytics ワークスペースまたは Application Insights コンポーネントは、次のように設定できます。
* 公衆ネットワーク (リソースの AMPLS に接続されていないネットワーク) からのインジェストを受け入れるかブロックする。
* 公衆ネットワーク (リソースの AMPLS に接続されていないネットワーク) からのクエリを受け入れるかブロックする。

この細分性により、ワークスペースごとにニーズに応じてアクセスを設定できます。 たとえば、インジェストは Private Link で接続されたネットワーク (つまり、特定の VNet) 経由でのみ受け入れつつ、クエリは公衆であれプライベートであれあらゆるネットワークから受け入れるように選択することができます。 

公衆ネットワークからのクエリをブロックすると、接続されている AMPLS の外部のクライアント (マシン、SDK など) はそのリソース内のデータに対してクエリを実行できなくなります。 このデータには、ログ、メトリック、およびライブ メトリック ストリームが含まれます。 公衆ネットワークからのクエリをブロックすると、これらのクエリを実行するすべてのエクスペリエンス (ブック、ダッシュボード、Azure portal 内の分析情報、Azure portal の外部から実行されるクエリなど) に影響が及びます。

構成の詳細については、「[リソース アクセス フラグを設定する](./private-link-configure.md#set-resource-access-flags)」を参照してください。

### <a name="exceptions"></a>例外

#### <a name="diagnostic-logs"></a>診断ログ
[[診断設定]](../essentials/diagnostic-settings.md) を使用してワークスペースにアップロードされたログとメトリックは、セキュリティで保護されたプライベート Microsoft チャネルを経由し、これらの設定によって制御されることはありません。

#### <a name="azure-resource-manager"></a>Azure Resource Manager
前に説明したようなアクセスの制限は、リソースのデータに適用されます。 ただし、これらのアクセス設定をオンまたはオフにするなどの構成の変更は、Azure Resource Manager で管理されます。 これらの設定を制御するには、適切なロール、アクセス許可、ネットワーク制御、監査を使用して、リソースへのアクセスを制限する必要があります。 詳細については、[Azure Monitor のロール、アクセス許可、およびセキュリティ](../roles-permissions-security.md)に関するページを参照してください

> [!NOTE]
> Azure Resource Management (ARM) API を介して送信されたクエリでは、Azure Monitor プライベート リンクを使用できません。 これらのクエリは、ターゲット リソースが公衆ネットワークからのクエリを許可している ([ネットワークの分離] ブレードまたは [CLI を使用](./private-link-configure.md#set-resource-access-flags)して設定します) 場合にのみ実行できます。
>
> 次のエクスペリエンスについては、ARM API を介してクエリが実行されることが確認されています。
> * Sentinel
> * LogicApp コネクタ
> * Update Management ソリューション
> * ソリューションの変更の追跡
> * VM Insights
> * Container Insights
> * Log Analytics の [ワークスペースの概要] ブレード (ソリューション ダッシュボードを表示)

## <a name="application-insights-considerations"></a>Application Insights の考慮事項
* 監視対象のワークロードをホストしているリソースをプライベート リンクに追加する必要があります。 例については、「[Azure Web アプリでプライベート エンドポイントを使用する](../../app-service/networking/private-endpoint.md)」を参照してください。
* ポータル以外の消費エクスペリエンスも、監視対象のワークロードを含むプライベート リンク VNET 内で実行する必要があります。
* Profiler と Debugger のプライベート リンクをサポートするには、[独自のストレージ アカウントを指定](../app/profiler-bring-your-own-storage.md)する必要があります。 

> [!NOTE]
> ワークスペース ベースの Application Insights を完全に保護するには、基になる Log Analytics ワークスペースと、Application Insights リソースへの両方のアクセスをロックダウンする必要があります。

## <a name="log-analytics-considerations"></a>Log Analytics の考慮事項

### <a name="log-analytics-solution-packs-download"></a>Log Analytics ソリューション パックのダウンロード
ソリューション パックをダウンロードするには、Log Analytics エージェントがグローバル ストレージ アカウントにアクセスする必要があります。 2021 年 4 月 19 日以降 (または、Azure ソブリン クラウドでは、2021 年 6 月から) に作成された Private Link セットアップでは、プライベート リンクを介して、エージェントのソリューション パックのストレージに接続できます。 この機能は、'blob.core.windows.net' 用に作成された DNS ゾーンによって可能になります。

2021 年 4 月 19 日より前に作成された Private Link セットアップでは、プライベート リンクを介してソリューション パックのストレージには接続できません。 これに対処するには、次のいずれかを行います。
* AMPLS とそれに接続されているプライベート エンドポイントを再作成します。
* ファイアウォールの許可リストに次のルールを追加することにより、エージェントがパブリック エンドポイントを介してストレージ アカウントに接続できるようにします。

    | クラウド環境 | エージェントのリソース | Port | Direction |
    |:--|:--|:--|:--|
    |Azure Public     | scadvisorcontent.blob.core.windows.net         | 443 | 送信
    |Azure Government | usbn1oicore.blob.core.usgovcloudapi.net | 443 |  送信
    |Azure China 21Vianet      | mceast2oicore.blob.core.chinacloudapi.cn| 443 | 送信

### <a name="collecting-custom-logs-and-iis-log-over-private-link"></a>Private Link 経由でのカスタム ログと IIS ログの収集
ストレージ アカウントは、カスタム ログの取り込みプロセスで使用されます。 既定では、サービスで管理されるストレージ アカウントが使用されます。 ただし、プライベート リンクにカスタム ログを取り込むには、独自のストレージ アカウントを使用して、それらを Log Analytics ワークスペースに関連付ける必要があります。

独自のストレージ アカウントを接続する方法の詳細については、「[ログ取り込み用の顧客所有のストレージ アカウント](private-storage.md)」および特に「[プライベート リンクの使用](private-storage.md#using-private-links)」と「[ストレージ アカウントを Log Analytics ワークスペースにリンクする](private-storage.md#link-storage-accounts-to-your-log-analytics-workspace)」を参照してください。

### <a name="automation"></a>Automation
Automation アカウント (Update Management、Change Tracking、Inventory など) を必要とする Log Analytics ソリューションを使用する場合は、Automation アカウント用にプライベート リンクを作成する必要もあります。 詳細は、「[Azure Private Link を使用して、ネットワークを Azure Automation に安全に接続する](../../automation/how-to/private-link-security.md)」を参照してください。

> [!NOTE]
> 一部の製品や Azure portal エクスペリエンスでは Azure Resource Manager を介してデータに対してクエリが実行されるため、Private Link 設定がその Resource Manager にも適用されていない限り、Private Link 経由ではデータに対してクエリを実行できません。 これを回避するには、「[リソースへのネットワーク アクセスの制御](./private-link-design.md#control-network-access-to-your-resources)」の説明に従い、公衆ネットワークからのクエリを受け入れるようにリソースを構成できます (インジェストは Private Link ネットワークのみに制限しておくことができます)。
次の製品およびエクスペリエンスが Azure Resource Manager 経由でワークスペースに対してクエリを実行していることが確認されています。
> * LogicApp コネクタ
> * Update Management ソリューション
> * ソリューションの変更の追跡
> * ポータルの [ワークスペースの概要] ブレード (ソリューション ダッシュボードを表示)
> * VM Insights
> * Container Insights

## <a name="requirements"></a>要件

### <a name="network-subnet-size"></a>ネットワーク サブネットのサイズ
サポートされる最小の IPv4 サブネットは /27 です (CIDR サブネット定義を使用)。 Azure VNets は [/29 という小さいサイズにすることもできます](../../virtual-network/virtual-networks-faq.md#how-small-and-how-large-can-vnets-and-subnets-be)が、Azure によって [5 つの IP アドレスが予約されています](../../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)。また、1 つのワークスペースに接続する場合でも、Azure Monitor Private Link の設定には最低でも 11 個の追加の IP アドレスが必要です。 Azure Monitor Private Link エンドポイントの詳細な一覧については、[エンドポイントの DNS 設定を確認してください](./private-link-configure.md#reviewing-your-endpoints-dns-settings)。


### <a name="agents"></a>エージェント
Log Analytics ワークスペースへのセキュリティで保護された取り込みをサポートするには、最新バージョンの Windows および Linux エージェントを使用する必要があります。 以前のバージョンでは、プライベート ネットワーク経由で監視データをアップロードすることはできません。

**Log Analytics Windows エージェント**

Log Analytics エージェント バージョン 10.20.18038.0 以降を使用します。

**Log Analytics Linux エージェント**

エージェント バージョン 1.12.25 以降を使用します。 できない場合は、VM で次のコマンドを実行します。

```cmd
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -X
$ sudo /opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <workspace key>
```
### <a name="azure-portal"></a>Azure portal
Application Insights や Log Analytics などの Azure Monitor ポータル エクスペリエンスを使用するには、プライベート ネットワークで Azure portal および Azure Monitor の拡張機能にアクセスできるようにする必要があります。 **AzureActiveDirectory**、**AzureResourceManager**、**AzureFrontDoor.FirstParty**、および **AzureFrontdoor.Frontend** [サービス タグ](../../firewall/service-tags.md)をネットワーク セキュリティ グループに追加します。

### <a name="programmatic-access"></a>プログラムによるアクセス
REST API、[CLI](/cli/azure/monitor) または PowerShell をプライベート ネットワーク上の Azure Monitor で使用するには、[サービス タグ](../../virtual-network/service-tags-overview.md) **AzureActiveDirectory** と **AzureResourceManager** をファイアウォールに追加します。

### <a name="application-insights-sdk-downloads-from-a-content-delivery-network"></a>コンテンツ配信ネットワークから Application Insights SDK をダウンロードする
ブラウザーが CDN からコードをダウンロードしないように、スクリプトに JavaScript コードをバンドルします。 例については [GitHub](https://github.com/microsoft/ApplicationInsights-JS#npm-setup-ignore-if-using-snippet-setup) を参照してください。

### <a name="browser-dns-settings"></a>ブラウザーの DNS 設定
プライベート リンクを介して Azure Monitor リソースに接続している場合、これらのリソースへのトラフィックは、お使いのネットワーク上で構成されているプライベート エンドポイントを経由する必要があります。 プライベート エンドポイントを有効にするには、「[プライベート エンドポイントへの接続](./private-link-configure.md#connect-to-a-private-endpoint)」の説明に従って、DNS 設定を更新します。 一部のブラウザーでは、ユーザーが設定したものではなく、ブラウザー自身の DNS 設定が使用されています。 ブラウザーが Azure Monitor パブリック エンドポイントに接続を試みる際に、プライベート リンクを完全にバイパスする場合があります。 ブラウザーの設定によって、DNS 設定がオーバーライドされていないこと、また、古い DNS 設定がキャッシュされていないことを確認してください。 

### <a name="querying-limitation-externaldata-operator"></a>クエリの制限: externaldata 演算子
[`externaldata` 演算子](/azure/data-explorer/kusto/query/externaldata-operator?pivots=azuremonitor)は、ストレージ アカウントからデータを読み取りますが、ストレージにプライベートでアクセスすることは保証されないため、Private Link 経由ではサポートされていません。

## <a name="next-steps"></a>次のステップ
- [Private Link の構成](private-link-configure.md)方法について確認します
- カスタム ログとカスタマー マネージド キー (CMK) の[プライベート ストレージ](private-storage.md)について確認する
- [Automation 用の Private Link](../../automation/how-to/private-link-security.md) について確認します