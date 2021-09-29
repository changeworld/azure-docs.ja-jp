---
title: Azure のトラフィック分析スキーマ | Microsoft Docs
description: Azure ネットワーク セキュリティ グループのフロー ログを分析するための、Traffic Analytics のスキーマについて説明します。
author: vinynigam
manager: agummadi
ms.service: network-watcher
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: vinigam
ms.openlocfilehash: 2ec8f1df778a95b571f89f3213c2cc71163de955
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597229"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Traffic Analytics のスキーマとデータ集計

Traffic Analytics は、クラウド ネットワークでのユーザーとアプリケーションのアクティビティを可視化するクラウドベースのソリューションです。 Traffic Analytics では、Network Watcher のネットワーク セキュリティ グループ (NSG) フロー ログを分析して、Azure クラウドでのトラフィック フローに関する洞察を示します。 トラフィック分析を使用すると、次のことが可能になります。

- Azure サブスクリプション全体のネットワーク アクティビティを視覚化し、ホットスポットを特定する。
- 開いているポート、インターネット にアクセスしようとしているアプリケーション、承認されていないネットワークに接続している仮想マシン (VM) などの情報を使用して、ネットワークに対するセキュリティの脅威を特定し、ネットワークをセキュリティで保護する。
- Azure リージョン間やインターネット上でのトラフィック フロー パターンを把握して、ネットワークのデプロイをパフォーマンスと容量に最適化する。
- ネットワークでの接続の失敗の原因となるネットワークの構成の誤りを特定する。
- ネットワークの使用状況 (バイト数、パケット数、またはフロー数) を把握する。

### <a name="data-aggregation"></a>データの集計

1. "FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までの間に NSG で記録されるすべてのフロー ログは、Traffic Analytics によって処理される前に、ストレージ アカウント内で 1 分間おきに BLOB としてキャプチャされます。
2. Traffic Analytics の既定の処理間隔は 60 分です。 つまり、Traffic Analytics は 60 分ごとに、集計のための BLOB をストレージから取得します。 選択した処理間隔が 10 分の場合、Traffic Analytics は 10 分ごとにストレージ アカウントから BLOB を取得します。
3. ソース IP、宛先 IP、宛先ポート、NSG 名、NSG ルール、フロー方向、およびトランスポート層プロトコル (TCP または UDP) が同じであるフロー (注:ソース ポートは集計から除外されます) は、Traffic Analytics によって 1 つのフローにまとめられます
4. この単一レコードは Traffic Analytics によって修飾され (詳しくは下記のセクションを参照)、Log Analytics に取り込まれます。このプロセスには最大で 1 時間かかります。
5. FlowStartTime_t フィールドは、"FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までのフロー ログ処理間隔の間に集計されたフロー (同じ 4 タプル) の、最初の発生日時を示します。
6. TA 内のリソースについては、UI に示されるフローは NSG から見たフロー総数ですが、Log Analytics のユーザーには 1 つのレコードのみが表示されます。 すべてのフローを表示するには、blob_id フィールドを使用します (これはストレージから参照できます)。 そのレコードの合計フロー数は、BLOB 内にある個々 のフローと一致します。

下のクエリは、過去 30 日間に Azure 以外のパブリック IP と対話したすべてのサブネットを確認するのに役立ちます。
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet1_s, Subnet2_s  
```
上記のクエリでフローの BLOB パスを表示するには、次のクエリを使用します。

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

上記のクエリでは、BLOB に直接アクセスするための URL を構築します。 プレース ホルダーを含む URL を次に示します。

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Traffic Analytics のスキーマで使用されるフィールド
  > [!IMPORTANT]
  > Traffic Analytics のスキーマは、2019 年 8 月 22 日に更新されました。 新しいスキーマでは、送信元 IP と送信先 IP が別々に得られ、FlowDirection フィールドを解析する必要がないため、クエリが簡素化されます。 </br>
  > FASchemaVersion_s は 1 から 2 に更新されました。 </br>
  > 非推奨となったフィールド: VMIP_s、Subscription_s、Region_s、NSGRules_s、Subnet_s、VM_s、NIC_s、PublicIPs_s、FlowCount_d </br>
  > 新しいフィールド: SrcPublicIPs_s、DestPublicIPs_s、NSGRule_s </br>
  > 非推奨となったフィールドは、2019 年 11 月 22 日までご利用いただけます。

Traffic Analytics は Log Analytics をベースに構築されています。そのため、Traffic Analytics によって修飾されたデータに対してカスタム クエリを実行し、同様にアラートを設定することもできます。

以下に示すのは、このスキーマで使用されるフィールドとその内容です

| フィールド | Format | 説明 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Traffic Analytics データのテーブル
| SubType_s | FlowLog | フロー ログのサブタイプ。 "FlowLog" のみを使用します。SubType_s の他の値は製品の内部動作用です。 |
| FASchemaVersion_s |   2   | スキーマ バージョン。 NSG フロー ログ バージョンは反映されません |
| TimeProcessed_t   | 日付と時刻 (UTC)  | Traffic Analytics がストレージ アカウントから未加工のフロー ログを処理した時刻 |
| FlowIntervalStartTime_t | 日付と時刻 (UTC) |  フロー ログ処理間隔の開始時刻。 これは、フロー間隔の計測が開始される時刻です |
| FlowIntervalEndTime_t | 日付と時刻 (UTC) | フロー ログ処理間隔の終了時刻 |
| FlowStartTime_t | 日付と時刻 (UTC) |  "FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までのフロー ログ処理間隔の間にキャプチャされた (集計対象の) フローの、最初の発生日時。 このフローは、集計ロジックに基づいて集計されます |
| FlowEndTime_t | 日付と時刻 (UTC) | "FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までのフロー ログ処理間隔の間にキャプチャされた (集計対象の) フローの、最後の発生日時。 フロー ログ v2 に関して言うと、このフィールドには、同じ 4 タプルを持つ最後のフローの開始時刻が格納されます (未加工のフロー レコードでは "B" としてマークされます) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Unknown Private <br> * Unknown | この表の下の注記で説明しています |
| SrcIP_s | 送信元 IP アドレス | AzurePublic フローと ExternalPublic フローの場合には空白になります |
| DestIP_s | 宛先 IP アドレス | AzurePublic フローと ExternalPublic フローの場合には空白になります |
| VMIP_s | VM の IP | AzurePublic フローと ExternalPublic フローに使用されます |
| PublicIP_s | パブリック IP アドレス | AzurePublic フローと ExternalPublic フローに使用されます |
| DestPort_d | 宛先ポート | トラフィックが受信されるポート |
| L4Protocol_s  | * T <br> * U  | トランスポート プロトコル。 T = TCP <br> U = UDP |
| L7Protocol_s  | プロトコル名 | 宛先ポートから抽出されます |
| FlowDirection_s | * I = Inbound (受信)<br> * O = Outbound (送信) | NSG を出入りするフローの方向 (フロー ログに基づく) |
| FlowStatus_s  | * A = NSG ルールで許可されている <br> *  D = NSG ルールで拒否されている  | NSG によって許可/ブロックされたフローの状態 (フロー ログに基づく) |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | フローに関連付けられたネットワーク セキュリティ グループ (NSG) |
| NSGRules_s | \<Index value 0)>\|\<NSG_RULENAME>\|\<Flow Direction>\|\<Flow Status>\|\<FlowCount ProcessedByRule> |  このフローを許可または拒否した NSG ルール |
| NSGRule_s | NSG_RULENAME |  このフローを許可または拒否した NSG ルール |
| NSGRuleType_s | * User Defined *  Default |   フローによって使用された NSG ルールの種類 |
| MACAddress_s | MAC アドレス | フローがキャプチャされた NIC の MAC アドレス |
| Subscription_s | このフィールドには、Azure 仮想ネットワーク/ネットワーク インターフェイス/仮想マシンのサブスクリプションが入力されます | FlowType が S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow、および UnknownPrivate の場合 (片側だけが Azure の場合) にのみ適用されます |
| Subscription1_s | サブスクリプション ID | フロー内のソース IP が属している仮想ネットワーク/ネットワーク インターフェイス/仮想マシンのサブスクリプション ID |
| Subscription2_s | サブスクリプション ID | フロー内の宛先 IP が属している仮想ネットワーク/ネットワーク インターフェイス/仮想マシンのサブスクリプション ID |
| Region_s | フロー内の IP が属している仮想ネットワーク/ネットワーク インターフェイス/仮想マシンの Azure リージョン | FlowType が S2S、P2S、AzurePublic、ExternalPublic、MaliciousFlow、および UnknownPrivate の場合 (片側だけが Azure の場合) にのみ適用されます |
| Region1_s | Azure リージョン | フロー内のソース IP が属している仮想ネットワーク/ネットワーク インターフェイス/仮想マシンの Azure リージョン |
| Region2_s | Azure リージョン | フロー内の宛先 IP が属している仮想ネットワークの Azure リージョン |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  トラフィックを送信または受信している VM に関連付けられた NIC |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | フロー内のソース ID アドレスに関連付けられた NIC |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | フロー内の宛先 ID アドレスに関連付けられた NIC |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | ネットワーク インターフェイス NIC_s に関連付けられた仮想マシン |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | フロー内のソース ID アドレスに関連付けられた仮想マシン |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | フロー内の宛先 ID アドレスに関連付けられた仮想マシン |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | NIC_s に関連付けられたサブネット |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | フロー内のソース ID アドレスに関連付けられたサブネット |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | フロー内の宛先 ID アドレスに関連付けられたサブネット |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | フロー内のソース ID アドレスに関連付けられたアプリケーション ゲートウェイ |
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | フロー内の宛先 ID アドレスに関連付けられたアプリケーション ゲートウェイ |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | フロー内のソース ID アドレスに関連付けられたロード バランサー |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | フロー内の宛先 ID アドレスに関連付けられたロード バランサー |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | フロー内のソース ID アドレスに関連付けられたローカル ネットワーク ゲートウェイ |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | フロー内の宛先 ID アドレスに関連付けられたローカル ネットワーク ゲートウェイ |
| ConnectionType_s | 可能な値は、VNetPeering、VpnGateway、および ExpressRoute です |    接続の種類 |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | 接続名。 フローの種類が P2S の場合、これは \<gateway name\>_\<VPN Client IP\> として書式設定されます |
| ConnectingVNets_s | 仮想ネットワーク名のスペース区切りリスト | ハブとスポークのトポロジの場合は、ハブの仮想ネットワークがここに入力されます |
| Country_s | 2 文字の国コード (ISO 3166-1 alpha-2) | フローの種類が ExternalPublic の場合に入力されます。 PublicIPs_s フィールド内のすべての IP アドレスは、同じ国コードになります |
| AzureRegion_s | Azure リージョンの場所 | フローの種類が AzurePublic の場合に入力されます。 PublicIPs_s フィールド内のすべての IP アドレスは、同じ Azure リージョンになります |
| AllowedInFlows_d | | 許可された受信フローの数。 これは、同じ 4 タプルを共有したフローの数を表します (フローがキャプチャされたネットワーク インターフェイスへの受信) |
| DeniedInFlows_d |  | 拒否された受信フローの数。 (フローがキャプチャされたネットワーク インターフェイスへの受信) |
| AllowedOutFlows_d | | 許可された送信フローの数 (フローがキャプチャされたネットワーク インターフェイスへの送信) |
| DeniedOutFlows_d  | | 拒否された送信フローの数 (フローがキャプチャされたネットワーク インターフェイスへの送信) |
| FlowCount_d | 非推奨になりました。 同じ 4 タプルに一致したフローの合計数。 フローの種類が ExternalPublic または AzurePublic の場合、さまざまなパブリック IP アドレスからのフローもカウントに含められます。
| InboundPackets_d | フローの宛先からソースに送信されたパケットを表します | これは、NSG のフロー ログ スキーマがバージョン 2 の場合にのみ入力されます |
| OutboundPackets_d  | フローのソースから宛先に送信されたパケットを表します | これは、NSG のフロー ログ スキーマがバージョン 2 の場合にのみ入力されます |
| InboundBytes_d |  フローの宛先からソースに送信されたバイト数を表します | これは、NSG のフロー ログ スキーマがバージョン 2 の場合にのみ入力されます |
| OutboundBytes_d |フローのソースから宛先に送信されたバイト数を表します | これは、NSG のフロー ログ スキーマがバージョン 2 の場合にのみ入力されます |
| CompletedFlows_d  |  | このフィールドには、NSG のフロー ログ スキーマがバージョン 2 の場合にのみ、ゼロ以外の値が入力されます |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | バーで区切られたエントリ |
| SrcPublicIPs_s | <SOURCE_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | バーで区切られたエントリ |
| DestPublicIPs_s | <DESTINATION_PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | バーで区切られたエントリ |

### <a name="public-ip-details-schema"></a>パブリック IP の詳細のスキーマ

Traffic Analytics を使用すると、お客様の環境内のすべてのパブリック IP についての WHOIS データと地理的な場所を取得できます。 悪意のある IP の場合は、Microsoft セキュリティ インテリジェンス ソリューションによって識別される DNS ドメイン、脅威の種類、スレッドの説明が表示されます。 IP の詳細は Log Analytics ワークスペースに発行されるため、カスタム クエリを作成してアラートを設定できます。 トラフィック分析ダッシュボードから事前に設定されたクエリにアクセスすることもできます。

パブリック IP の詳細のスキーマを次に示します。

| フィールド | Format | 説明 |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalyticsIPDetails_CL | Traffic Analytics の IP の詳細のデータを含むテーブル |
| SubType_s | FlowLog | フロー ログのサブタイプ。 **"FlowLog" のみを使用します**。SubType_s の他の値は製品の内部動作用です |
| FASchemaVersion_s | 2 | スキーマ バージョン。 NSG フロー ログ バージョンは反映されません |
| FlowIntervalStartTime_t | 日付と時刻 (UTC) | フロー ログ処理間隔の開始時刻。 これは、フロー間隔の計測が開始される時刻です |
| FlowIntervalEndTime_t | 日付と時刻 (UTC) | フロー ログ処理間隔の終了時刻 |
| FlowType_s | * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow | この表の下の注記で説明しています |
| IP | パブリック IP | レコード内に情報が示されるパブリック IP |
| 場所 | IP の場所 | - Azure Public IP の場合: IP が属している仮想ネットワーク、ネットワーク インターフェイス、仮想マシンの Azure リージョン <br> - External Public IP と悪意のある IP の場合: IP が位置する 2 文字の国番号 (ISO 3166-1 alpha-2) |
| PublicIPDetails | IP に関する情報 | - AzurePublic IP の場合: IP の背後にある Azure サービス <br> - ExternalPublic/Malicious IP: IP の WhoIS 情報 |
| ThreatType | 悪意のある IP によってもたらされる脅威 | **悪意のある IP のみ**: 脅威の一覧から現在有効な値のいずれか (以下で説明します) |
| ThreatDescription | 脅威の説明 | **悪意のある IP のみ**: 悪意のある IP によってもたらされる脅威の説明 |
| DNSDomain | DNS ドメイン | **悪意のある IP のみ**: この IP に関連付けられているドメイン名 |

脅威の種類の一覧:

| 値 | 説明 |
|:---   |:---    |
| ボットネット | インジケーターは、ボットネット ノードとメンバーの詳細を示しています。 |
| C2 | インジケーターは、ボットネットのコマンド アンド コントロール ノードの詳細を示しています。 |
| CryptoMining | このネットワーク アドレスと URL に関連するトラフィックは、CyrptoMining とリソースの不正使用を示します。 |
| DarkNet | Darknet ノードとネットワークのインジケーターです。 |
| DDos | アクティブまたは今後の DDoS キャンペーンに関連するインジケーター。 |
| MaliciousUrl | マルウェアを配信している URL。 |
| マルウェア | 悪意のあるファイルを記述するインジケーター。 |
| フィッシング | フィッシング キャンペーンに関連するインジケーター。 |
| プロキシ | プロキシ サービスのインジケーターです。 |
| PUA | 望ましくない可能性のあるアプリケーション。 |
| WatchList | これは、脅威が何であるかを正確に判断できない場合、または手動の解釈を必要とする場合にインジケーターが配置される一般バケットです。 これは通常、システムにデータを送信するパートナーが使用すべきではありません。 |


### <a name="notes"></a>Notes

1. フローが AzurePublic または ExternalPublic の場合は、VMIP_s にはお客様所有の Azure VM IP が入力され、PublicIPs_s フィールドにはパブリック IP アドレスが入力されます。 これらの 2 種類のフローについては、SrcIP_s フィールドと DestIP_s フィールドではなく、VMIP_s と PublicIPs_s が使用されます。 AzurePublic と ExternalPublicIP のアドレスについては、さらなる集計を実行して、お客様のログ分析ワークスペースに取り込まれるレコード数が最小限に抑えられるようにします。(このフィールドは間もなく非推奨になります。それ以降は、Azure VM がそのフローでソースであったか宛先であったかに応じて、SrcIP_ と DestIP_s が使用されるようになります)
1. フローの種類の詳細:フローは、関連している IP アドレスに基づいて、以下のフロー タイプに分類されます。
1. IntraVNet – フロー内の両方の IP アドレスが、同じ Azure 仮想ネットワーク内に存在しています。
1. InterVNet - フロー内の IP アドレスが、2 つの異なる Azure 仮想ネットワーク内に存在しています。
1. S2S – (Site To Site) 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、VPN ゲートウェイまたは Express Route を介して Azure 仮想ネットワークに接続された、お客様のネットワーク (サイト) に属しています。
1. P2S - (Point To Site) 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、VPN ゲートウェイを介して Azure 仮想ネットワークに接続された、お客様のネットワーク (サイト) に属しています。
1. AzurePublic - 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、Microsoft が所有する、Azure 内部のパブリック IP アドレスに属しています。 お客様所有のパブリック IP アドレスは、このタイプのフローには含まれません。 たとえば、お客様所有の VM が Azure サービス (ストレージ エンドポイント) にトラフィックを送信している場合、その VM はこのタイプのフローに分類されます。
1. ExternalPublic - 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、Azure ではないパブリック IP です。これは、"FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までの処理間隔用に Traffic Analytics が使用する ASC フィードで、悪意のあるフローとして報告されません。
1. MaliciousFlow - 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、Azure ではないパブリック IP です。これは、"FlowIntervalStartTime_t" から "FlowIntervalEndTime_t" までの処理間隔用に Traffic Analytics が使用する ASC フィードで、悪意のあるフローとして報告されます。
1. UnknownPrivate - 一方の IP アドレスは Azure 仮想ネットワークに属していて、もう一方の IP アドレスは、RFC 1918 で定義されたプライベート IP 範囲に属しています。後者は、Traffic Analytics によってお客様所有のサイトまたは Azure 仮想ネットワークにマップできませんでした。
1. Unknown – フロー内のどちらの IP アドレスも、Azure 内およびオンプレミス (サイト) 上のカスタマ トポロジを使用してマップできません。
1. 一部のフィールド名は、\_s または \_d が付加されます。 これらは、送信先と送信元を示すものではなく、それぞれデータ型としての string と decimal を示しています。

### <a name="next-steps"></a>次の手順
よく寄せられる質問への回答を確認するには、[トラフィック分析に関する FAQ](traffic-analytics-faq.yml) をご覧ください。機能に関する詳細を確認するには、[Traffic Analytics のドキュメント](traffic-analytics.md)をご覧ください
