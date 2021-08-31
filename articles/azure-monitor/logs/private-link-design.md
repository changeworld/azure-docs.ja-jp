---
title: Private Link のセットアップ設計
description: Private Link のセットアップ設計
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: ccee8017bce3109cc6d47bbeb225ca7c1edf5d66
ms.sourcegitcommit: 47491ce44b91e546b608de58e6fa5bbd67315119
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122201944"
---
# <a name="design-your-private-link-setup"></a>Private Link のセットアップ設計

Azure Monitor Private Link のセットアップを設定する前に、ネットワーク トポロジ、特に DNS ルーティング トポロジを検討してください。
「[しくみ](./private-link-security.md#how-it-works)」で説明されているように、Private Link の設定はすべての Azure Monitor リソースへのトラフィックに影響します。 これは特に、Application Insights リソースに当てはまります。 この設定は、プライベート エンドポイントに接続されているネットワークだけでなく、同じ DNS を共有している他のすべてのネットワークにも影響します。

> [!NOTE]
> 最もシンプルで最も安全なのは次のアプローチです。
> 1. 単一のプライベート エンドポイントと単一の AMPLS を指定して、単一の Private Link 接続を作成します。 ネットワークがピアリングされている場合は、共有 (またはハブ) VNet 上に Private Link 接続を作成します。
> 2. "*すべての*" Azure Monitor リソース (Application Insights コンポーネントおよび Log Analytics ワークスペース) をその AMPLS に追加します。
> 3. ネットワーク エグレス トラフィックを可能な限りブロックします。

何らかの理由で単一の Private Link と単一の Azure Monitor Private Link Scope (AMPLS) を使用できない場合、次善の方法は、分離ネットワーク用に分離された Private Link 接続を作成することです。 スポーク VNet を使用している (または連携できる) 場合は、「[Azure のハブスポーク ネットワーク トポロジ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)」のガイダンスに従います。 次に、関連するスポーク VNet で、Private Link の設定を個別にセットアップします。 DNS ゾーンを他のスポーク ネットワークと共有すると DNS がオーバーライドされるため、**必ず DNS ゾーンも分離** してください。

## <a name="plan-by-network-topology"></a>ネットワーク トポロジ別の計画
### <a name="hub-spoke-networks"></a>ハブスポーク ネットワーク
ハブスポーク トポロジでは、ハブ (メイン) VNet に Private Link を設定し、各スポーク VNet には設定しないことにより、DNS オーバーライドの問題を回避できます。 このセットアップは特に、スポーク VNet で使用される Azure Monitor リソースが共有されている場合に合理的です。 

![ハブアンドスポークのシングル PE](./media/private-link-security/hub-and-spoke-with-single-private-endpoint.png)

> [!NOTE]
> たとえば、各 VNet が限られた一連の監視リソースにアクセスできるようにするために、スポーク VNet 用に個別の Private Link を作成することをお勧めします。 このような場合は、VNet ごとに専用のプライベート エンドポイントと AMPLS を作成できますが、**DNS がオーバーライドされないように、同じ DNS ゾーンが共有されていないことも確認する必要があります**。

### <a name="peered-networks"></a>ピアリングされたネットワーク
ネットワーク ピアリングは、ハブスポーク以外のさまざまなトポロジで使用されます。 このようなネットワークは、互いの IP アドレスに到達でき、多くの場合は同一の DNS を共有しています。 このような場合、推奨事項はハブスポークに似ています。他のすべての (関連する) ネットワークから到達される単一のネットワークを選択し、そのネットワーク上で Private Link 接続を設定します。 複数のプライベート エンドポイントや AMPLS オブジェクトを作成しないでください。これは、最終的には DNS に含まれる最後のセットのみが適用されるためです。

### <a name="isolated-networks"></a>分離ネットワーク
#<a name="if-your-networks-arent-peered-you-must-also-separate-their-dns-in-order-to-use-private-links-once-thats-done-you-can-create-a-private-link-for-one-or-many-network-without-affecting-traffic-of-other-networks-that-means-creating-a-separate-private-endpoint-for-each-network-and-a-separate-ampls-object-your-ampls-objects-can-link-to-the-same-workspacescomponents-or-to-different-ones"></a>ネットワークがピアリングされていない場合は、**プライベート リンクを使用するために DNS も分離する必要があります**。 分離が完了したら、他のネットワークのトラフィックには影響を与えずに、1 つの (または多くの) ネットワークに対してプライベート リンクを作成できます。 これは、ネットワークごとに個別のプライベート エンドポイントと、個別の AMPLS オブジェクトを作成することを意味します。 AMPLS オブジェクトは、同じワークスペースやコンポーネントにリンクすることも、異なるワークスペースやコンポーネントにリンクすることもできます。

### <a name="testing-locally-edit-your-machines-hosts-file-instead-of-the-dns"></a>ローカルでのテスト: DNS ではなくマシンのホスト ファイルを編集する 
オール オア ナッシングの動作のローカル バイパスとして、Private Link のレコードで DNS を更新するのではなく、選択したマシンのホスト ファイルを編集して、それらのマシンのみが Private Link エンドポイントに要求を送信するようにすることができます。
* Private Link を設定しますが、プライベート エンドポイントに接続する際は、DNS と自動統合 **しない** ことを選択します (手順 5b)。
* マシンのホスト ファイルで関連するエンドポイントを構成します。 マッピングが必要な Azure Monitor エンドポイントを確認するには、「[エンドポイントの DNS 設定の確認](./private-link-configure.md#reviewing-your-endpoints-dns-settings)」を参照してください。

このアプローチは、運用環境にはお勧めしません。

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
Log Analytics ワークスペースや Application Insights コンポーネントは、公衆ネットワーク、つまりリソースの AMPLS に接続されていないネットワークからのアクセスを受け入れるように設定することも、ブロックするように設定することもできます。
この細分性により、ワークスペースごとにニーズに応じてアクセスを設定できます。 たとえば、ネットワークに接続された Private Link (つまり、特定の VNet) 経由のインジェストのみを受け入れ、パブリックであれプライベートであれすべてのネットワークからのクエリを受け入れるように選択することができます。 公衆ネットワークからのクエリをブロックすると、接続されている AMPLS の外部のクライアント (マシン、SDK など) はそのリソース内のデータに対してクエリを実行できないことにご注意ください。 このデータには、ログ、メトリック、ライブ メトリック ストリームへのアクセスと、ブック、ダッシュボード、クエリ API ベースのクライアント エクスペリエンス、Azure portal の分析情報などの上に構築されたエクスペリエンスが含まれます。 Azure portal の外部で実行され、Log Analytics データに対してクエリを実行するエクスペリエンスも、この設定の影響を受けます。


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
ストレージ アカウントは、カスタム ログの取り込みプロセスで使用されます。 既定では、サービスで管理されるストレージ アカウントが使用されます。 ただし、プライベート リンクにカスタム ログを取り込むには、独自のストレージ アカウントを使用して、それらを Log Analytics ワークスペースに関連付ける必要があります。 [コマンド ライン](/cli/azure/monitor/log-analytics/workspace/linked-storage)を使用してこのようなアカウントをセットアップする方法の詳細について参照してください。

独自のストレージ アカウントを接続する方法の詳細については、[ログ取り込み用の顧客所有ストレージ アカウント](private-storage.md)に関する記事を参照してください。

### <a name="automation"></a>Automation
Update Management、Change Tracking、Inventory などの、Automation アカウントを必要とする Log Analytics ソリューションを使用する場合は、Automation アカウント用に個別の Private Link を設定する必要もあります。 詳細は、「[Azure Private Link を使用して、ネットワークを Azure Automation に安全に接続する](../../automation/how-to/private-link-security.md)」を参照してください。

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
- [プライベート ストレージ](private-storage.md)について確認します
- [Automation 用の Private Link](../../automation/how-to/private-link-security.md) について確認します