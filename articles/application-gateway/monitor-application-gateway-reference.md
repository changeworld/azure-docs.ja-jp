---
title: Azure Application Gateway データの監視のリファレンス
description: Application Gateway を監視する際に必要となる重要なリファレンス資料
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 06/10/2021
ms.openlocfilehash: 5ece5b220a59f562774fde7b093236ed5946f019
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124836246"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>Azure Application Gateway データの監視のリファレンス

Azure Application Gateway の監視データの収集と分析の詳細については、[Azure Application Gateway の監視](monitor-application-gateway.md)に関するページを参照してください。

## <a name="metrics"></a>メトリック

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>Application Gateway V2 のメトリック

リソース プロバイダーと種類: [Microsoft.Compute/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>タイミングのメトリック
Application Gateway には、要求と応答に関連するいくつかのタイミング メトリックが組み込まれています。これらはすべて、ミリ秒単位で測定されます。

> [!NOTE]
>
> Application Gateway に複数のリスナーがある場合は、意味のある推論を得るために、異なる待機時間メトリックを比較しながら、常に "*リスナー*" ディメンションでフィルター処理します。


| メトリック | ユニット | 説明|
|:-------|:-----|:------------|
|**バックエンド接続時間**|ミリ秒|バックエンド アプリケーションとの接続を確立するために費やされた時間です。<br><br>これには、新しい接続を確立するためにバックエンド サーバーの TCP スタックが要した時間だけでなく、ネットワーク待機時間も含まれます。 TLS の場合、これにはハンドシェイクに費やされた時間も含まれます。|
|**バックエンド先頭バイト応答時間**|ミリ秒|バックエンド サーバーへの接続の確立を開始してから応答ヘッダーの最初のバイトを受信するまでの時間間隔です。<br><br>これにより、Application Gateway からバックエンドへのアクセス要求にかかった時間、バックエンド アプリケーションが応答するまでの時間 (サーバーがコンテンツを生成し、その後データベース クエリをフェッチするためにかかった時間)、および応答の先頭バイトがバックエンドから Application Gateway に到着するまでにかかった時間の合計であるバックエンド接続時間を概算します。|
|**バックエンド最終バイト応答時間**|ミリ秒|バックエンド サーバーへの接続の確立を開始してから応答本文の最後のバイトを受信するまでの時間間隔です。<br><br>これにより、バックエンド先頭バイト応答時間とデータ転送時間の合計が概算されます。 この数値は、要求されたオブジェクトのサイズとサーバー ネットワークの待機時間によって大きく異なる場合があります。|
|**アプリケーション ゲートウェイの合計時間**|ミリ秒|要求の受信、処理、および応答の送信にかかった平均時間。<br><br>これは、Application Gateway が HTTP 要求の最初のバイトを受信してから、最後の応答バイトがクライアントに送信されるまでの間隔です。 これには、Application Gateway によって行われた処理時間、バックエンド最終バイト応答時間、Application Gateway ですべての応答を送信するのにかかった時間、クライアント RTT が含まれます。|
|**クライアント RTT**|ミリ秒|クライアントと Application Gateway の間の平均ラウンドトリップ時間。|

これらのメトリックを使用することで、観測された速度低下の原因が、クライアント ネットワーク、Application Gateway のパフォーマンス、バックエンド ネットワークとバックエンド サーバーの TCP スタックの飽和、バックエンド アプリケーションのパフォーマンス、または大きなファイル サイズの内どれかを判断できます。

たとえば、*バックエンド先頭バイト応答時間* に急増傾向が見られる一方、*バックエンド接続時間* が安定傾向を見せている場合、Application Gateway からバックエンドへの待機時間と、接続の確立にかかった時間が安定しており、急増はバックエンド アプリケーションの応答時間の増加が原因であると推測できます。 一方、*バックエンド先頭バイト応答時間* の急増が *バックエンド接続時間* に対応する急増に関連付けられている場合は、Application Gateway とバックエンドサーバーの間のネットワーク、またはバックエンド サーバーの TCP スタックが飽和状態になっていることが推測できます。 

*バックエンド最終バイト応答時間* が急増しているにもかかわらず、*バックエンド先頭バイト応答時間* が安定している場合は、この急増は要求されているファイルが大きいことが原因であると推測できます。

同様に、*Application Gateway の合計時間* が急増しているにもかかわらず、*バックエンド最終バイト応答時間* が安定している場合は、Application Gateway でのパフォーマンスのボトルネック、またはクライアントと Application Gateway 間のネットワークのボトルネックのいずれかの兆候である場合があります。 さらに、*クライアント RTT* に対応する急増も見られる場合、この低下はクライアントと Application Gateway 間のネットワークが原因であることを示します。

#### <a name="application-gateway-metrics"></a>Application Gateway メトリック

| メトリック | ユニット | 説明|
|:-------|:-----|:------------|
|**受信バイト数**|バイト|Application Gateway でクライアントから受信したバイト数。|
|**送信バイト数**|バイト|Application Gateway からクライアントに送信されたバイト数。|
|**クライアント TLS プロトコル**|カウント|Application Gateway との接続を確立したクライアントによって開始された TLS 要求と非 TLS 要求の数です。 TLS プロトコルの分布を表示するには、TLS プロトコル ディメンションでフィルター処理します。|
|**現在の容量ユニット**|カウント|トラフィックの負荷分散に使用される容量ユニットの数。 容量ユニットには、コンピューティング ユニット、永続的接続、スループットの 3 つの決定項があります。 各容量ユニットは最大で次のものにより構成されます: 1 個のコンピューティング ユニット、2500 個の永続的接続、または 2.22 Mbps のスループット。|
|**現在のコンピューティング ユニット**|カウント|消費されたプロセッサ容量の数です。 コンピューティング ユニットに影響する要因は、1 秒あたりの TLS 接続数、URL 書き換え計算、WAF ルールの処理です。|
|**現在の接続数**|カウント|クライアントから Application Gateway に対してアクティブになっているコンカレント接続の合計数。|
|**推定課金対象容量ユニット**|カウント|v2 SKU では、料金モデルは使用量によって決まります。 容量ユニットでは、固定費に追加して課金される使用量ベースの費用が測定されます。 *推定課金対象容量ユニット* は、課金が推定される使用中の容量ユニットの数を示します。 これは *現在の容量ユニット* (トラフィックの負荷を分散するために必要な容量ユニット) と *固定請求可能容量ユニット* (プロビジョニングが維持される最小容量ユニット) のうち大きい方の値として計算されます。|
|**失敗した要求**|カウント|Application Gateway が 5xx サーバー エラーコードで処理した要求の数。 これには、Application Gateway から生成された 5xx コードと、バックエンドから生成された 5xx のコードが含まれます。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。|
|**固定請求可能容量ユニット**|カウント|Application Gateway 構成の *最小スケール ユニット* 設定 (1 つのインスタンスが 10 個の容量ユニットに変換される) に従ってプロビジョニングが維持される容量ユニットの最小数。|
|**1 秒あたりの新しい接続数**|カウント|クライアントから Application Gateway、および Application Gateway からバックエンド メンバーに対して確立される、1 秒あたりの新しい TCP 接続の平均数。|
|**応答の状態**|status code|Application Gateway によって返される HTTP 応答の状態です。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。|
|**スループット**|バイト/秒|Application Gateway で処理された 1 秒あたりのバイト数。|
|**要求の合計数**|カウント|Application Gateway が処理した成功した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。|

#### <a name="backend-metrics"></a>バックエンド メトリック

| メトリック | ユニット | 説明|
|:-------|:-----|:------------|
|**バックエンド応答の状態**|カウント|バックエンドによって返された HTTP 応答状態コードの数です。 これには、Application Gateway によって生成された応答コードは含まれません。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。|
|**正常なホストの数**|カウント|正常性プローブによって正常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの正常なホストの数を表示できます。|
|**異常なホストの数**|カウント|正常性プローブによって異常であると判定されたバックエンドの数です。 バックエンド プールごとにフィルター処理を行って、特定のバックエンド プールの異常なホストの数を表示できます。|
|**正常なホストごとの 1 分あたりの要求**|カウント|バックエンド プール内の正常なメンバーが 1 分間に受信した要求の平均数です。 *BackendPool HttpSettings* ディメンションを使用してバックエンド プールを指定する必要があります。|


### <a name="application-gateway-v1-metrics"></a>Application Gateway V1 のメトリック

#### <a name="application-gateway-metrics"></a>Application Gateway メトリック

| メトリック | ユニット | 説明|
|:-------|:-----|:------------|
|**CPU 使用率**|パーセント|Application Gateway に割り当てられた CPU の使用率を表示します。 通常の状況では、CPU 使用率が 90% を超えることは望ましくありません。これは Application Gateway の背後でホストされている Web サイトで待機時間が発生し、クライアント エクスペリエンスを乱す可能性があるためです。 インスタンス数を増やすか、SKU のサイズを大きくするか、またはその両方を行うことによって、Application Gateway の構成を変更し、CPU 使用率を間接的に制御または向上させることができます。|
|**現在の接続数**|カウント|Application Gateway で確立された現在の接続の数。|
|**失敗した要求**|カウント|接続の問題が原因で失敗した要求の数。 この数には、"*要求タイムアウト*" HTTP 設定を超えたために失敗した要求と、Application Gateway とバックエンド間の接続に問題があるために失敗した要求が含まれます。 この数には、正常なバックエンドが使用できないために発生した失敗は含まれません。 バックエンドからの 4xx と 5xx の応答も、このメトリックの一部とは見なされません。|
|**応答の状態**|status code|Application Gateway によって返される HTTP 応答の状態です。 応答状態コードの分布をさらに分類し、2xx、3xx、4xx、5xx のカテゴリで応答を表示できます。|
|**スループット**|バイト/秒|Application Gateway で処理された 1 秒あたりのバイト数。|
|**要求の合計数**|カウント|Application Gateway が処理した成功した要求の数です。 要求の数をさらにフィルター処理することで、各々のまたは特定のバックエンド プール http 設定の組み合わせごとに数を表示できます。|
|**Web アプリケーション ファイアウォールのブロックされた要求数**|カウント|WAF によってブロックされた要求の数。|
|**Web アプリケーション ファイアウォールのブロックされた要求の分布**|カウント|WAF によってブロックされた要求の数をフィルター処理して、各々のまたは特定の WAF ルール グループまたは WAF ルール ID の組み合わせごとに数を表示します。|
|**Web アプリケーション ファイアウォールの合計規則の分布**|カウント|特定の WAF ルール グループまたは WAF ルール ID の組み合わせごとに受信した要求の数。|


<!-- Keep this text as-is -->
詳細については、[Azure Monitor でサポートされているすべてのプラットフォーム メトリック](../azure-monitor/essentials/metrics-supported.md)の一覧を参照してください。



## <a name="metric-dimensions"></a>メトリック ディメンション

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

メトリック ディメンションの詳細については、「[多次元メトリック](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)」を参照してください。


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure Application Gateway では、Azure Monitor の一部のメトリックのディメンションがサポートされています。 各メトリックには、そのメトリック専用のディメンションに関する説明が含まれています。


## <a name="resource-logs"></a>リソース ログ
<!-- REQUIRED. Please  keep headings in this order -->

このセクションでは、Azure Application Gateway 用に収集できるリソース ログの種類を示します。 

<!-- List all the resource log types you can have and what they are for -->  

[Azure Monitorでサポートされているすべてのリソース ログのカテゴリの種類](../azure-monitor/essentials/resource-logs-schema.md)の一覧も参照してください。

> [!NOTE]
> パフォーマンス ログは v1 SKU でのみ使用できます。 v2 SKU の場合は、パフォーマンス データに[メトリック](#metrics)を使用します。

詳細については、「[Application Gateway のバックエンドの正常性および診断ログ](application-gateway-diagnostics.md#access-log)」を参照してください

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>Application Gateway

リソース プロバイダーと種類: [Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| カテゴリ | 表示名 | 情報|
|:---------|:-------------|------------------|
| **Activitylog**   | アクティビティ ログ | アクティビティ ログのエントリは既定で収集されます。 [Azure アクティビティ ログ](../azure-monitor/essentials/activity-log.md) (以前の操作ログと監査ログ) を使用すると、Azure サブスクリプションに送信されるすべての操作とその操作の状態を表示できます。 |
|**ApplicationGatewayAccessLog**|アクセス ログ| このログを使用して Application Gateway のアクセス パターンを表示し、重要な情報を分析できます。 これには、呼び出し元の IP アドレス、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。アクセス ログは 60 秒ごとに収集されます。 このログには、Application Gateway のインスタンスごとに 1 つのレコードが含まれます。 Application Gateway のインスタンスは、instanceId プロパティで識別されます。|
| **ApplicationGatewayPerformanceLog**|パフォーマンス ログ|このログを使用すると、Application Gateway のインスタンスの実行状況を確認できます。 このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。 パフォーマンス ログは 60 秒ごとに収集されます。 パフォーマンス ログは v1 SKU でのみ使用できます。 v2 SKU の場合は、パフォーマンス データに[メトリック](#metrics)を使用します。|
|**ApplicationGatewayFirewallLog**|ファイアウォール ログ|このログを使用すると、Web アプリケーション ファイアウォールが構成された Application Gateway の、検出モードまたは防止モードでログに記録された要求を表示することができます。 ファイアウォール ログは 60 秒ごとに収集されます。|


## <a name="azure-monitor-logs-tables"></a>Azure Monitor ログ テーブル
<!-- REQUIRED. Please keep heading in this order -->

このセクションでは、Azure Application Gateway に関連し、Log Analytics によるクエリに使用できるすべての Azure Monitor ログの Kusto テーブルを参照します。 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|リソースの種類 | メモ |
|-------|-----|
| [Application Gateway](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |AzureActivity、AzureDiagnostics、AzureMetrics を含みます |


すべての Azure Monitor ログ/Log Analytics テーブルのリファレンスについては、[Azure Monitor ログ テーブル リファレンス](/azure/azure-monitor/reference/tables/tables-resourcetype)に関するページを参照してください。

### <a name="diagnostics-tables"></a>診断テーブル
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure Application Gateway では、[Azure Diagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) テーブルを使用してリソース ログ情報を格納します。 関係する列を次に示します。

**Azure Diagnostics**

| プロパティ | 説明 |
|:--- |:---|
requestUri_s | クライアント要求の URI。|
メッセージ | 「SQL インジェクション攻撃」などの情報メッセージ|
userAgent_s | クライアント要求のユーザー エージェントの詳細|
ruleName_s | この要求を処理するために使用される要求ルーティング規則|
httpMethod_s | クライアント要求の HTTP メソッド|
instanceId_s | クライアント要求が評価のためにルーティングされる Appgw インスタンス|
httpVersion_s | クライアント要求の HTTP バージョン|
clientIP_s | 要求が行われた IP|
host_s | クライアント要求のホスト ヘッダー|
requestQuery_s | クライアント要求に含まれているクエリ文字列|
sslEnabled_s | クライアント要求で SSL が有効になっているかどうか|


## <a name="see-also"></a>参照

<!-- replace below with the proper link to your main monitoring service article -->
- Azure Application Gateway の監視については、[Azure Application Gateway の監視](monitor-application-gateway.md)に関するページをご覧ください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。