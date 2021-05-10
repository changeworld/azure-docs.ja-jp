---
title: Azure Monitor の Wire Data ソリューション | Microsoft Docs
description: ワイヤー データは、Log Analytics エージェントを使用してコンピューターのネットワークとパフォーマンスのデータを統合したものです。 ネットワーク データをログ データと結び付けると、データを相関させるのに役立ちます。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2021
ms.openlocfilehash: 1a9ea544419ef5c688e78a25eeb0eb444b196ec9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732025"
---
# <a name="wire-data-20-preview-solution-in-azure-monitor-retired"></a>Azure Monitor の Wire Data 2.0 (プレビュー) ソリューション (提供終了)

![ワイヤ データのシンボル](media/wire-data/wire-data2-symbol.png)

>[!NOTE]
>Wire Data ソリューションは、[VM insights](../vm/vminsights-overview.md) と [Service Map ソリューション](../vm/service-map.md)に置き換えられました。  どちらも、Log Analytics エージェントと Dependency Agent を使用して、Azure Monitor にネットワーク接続データを収集します。
>
>Wire Data ソリューションのサポートは、**2022 年 3 月 31 日** に終了します。  提供終了日まで、Wire Data 2.0 (プレビュー) ソリューションを使用している既存のお客様は、引き続きこれを使用できます。
>
>新規および既存のお客様は、[VM insights](../vm/vminsights-enable-overview.md) または [Service Map ソリューション](../vm/service-map.md)をインストールする必要があります。  これらが収集するマップ データセットは、Wire Data 2.0 (プレビュー) データセットに相当します。  VM insights には、Service Map データセットと、分析用の追加のパフォーマンス データおよび機能が含まれます。 どちらのオファリングにも、[Azure Sentinel との接続](https://docs.microsoft.com/azure/sentinel/connect-data-sources#map-data-types-with-azure-sentinel-connection-options)があります。
 

ワイヤ データとは、Log Analytics エージェントがインストールされた Windows に接続されたコンピューターおよび Linux に接続されたコンピューター (環境内の Operations Manager によって監視されているコンピューターを含む) から収集したネットワーク データとパフォーマンス データを統合したものです。 ネットワーク データを他のログ データと結び付けると、データを相関させるのに役立ちます。

Log Analytics エージェントに加えて、ワイヤ データ ソリューションは、IT インフラストラクチャ内のコンピューターにインストールされている Microsoft Dependency Agent を使用します。 Dependency Agent では、使用されるさまざまなプロトコルやポートなど、[OSI モデル](https://en.wikipedia.org/wiki/OSI_model)のネットワーク レベル 2 ～ 3 について、コンピューターとの間で送受信されたネットワーク データを監視します。 データはその後、エージェントを使用して Azure Monitor に送信されます。

## <a name="migrate-to-azure-monitor-vm-insights-or-service-map"></a>Azure Monitor VM insights または Service Map への移行

多くの場合、お客様は、同じ VM で Wire Data 2.0 (プレビュー) と [VM insights](../vm/vminsights-overview.md) または [Service Map ソリューション](../vm/service-map.md)を既に有効にしています。  これは、VM で代替オファリングが有効になっていることを意味します。  簡単に、[Log Analytics ワークスペースから Wire Data 2.0 (プレビュー) ソリューションを削除](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution)できます。

Wire Data 2.0 (プレビュー) のみ有効になっている VM がある場合、その VM を [VM insights](../vm/vminsights-enable-overview.md) または [Service Map ソリューション](../vm/service-map.md)にオンボードし、その後 [Wire Data 2.0 (プレビュー) ソリューションを Log Analytics ワークスペースから削除](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution)できます。

## <a name="migrate-your-queries-to-the-vmconnection-table-from-azure-monitor-vm-insights"></a>Azure Monitor VM insights から VMConnection テーブルにクエリを移行する

### <a name="agents-providing-data"></a>データを提供するエージェント

#### <a name="wire-data-20-query"></a>Wire Data 2.0 のクエリ

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by Computer
| limit 500000
```

#### <a name="vm-insights-and-service-map-query"></a>VM insights と Service Map のクエリ

```
VMConnection
| summarize AggregatedValue = sum(BytesReceived + BytesSent) by Computer
| limit 500000
```

### <a name="ip-addresses-of-the-agents-providing-data"></a>データを提供するエージェントの IP アドレス

#### <a name="wire-data-20-query"></a>Wire Data 2.0 のクエリ

```
WireData
| summarize AggregatedValue = count() by LocalIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM insights と Service Map のクエリ

```
VMComputer
| distinct Computer, tostring(Ipv4Addresses)
```

### <a name="all-outbound-communications-by-remote-ip-address"></a>リモート IP アドレスによるすべての外部送信

#### <a name="wire-data-20-query"></a>Wire Data 2.0 のクエリ

```
WireData
| where Direction == "Outbound"
| summarize AggregatedValue = count() by RemoteIP
```

#### <a name="vm-insights-and-service-map-query"></a>VM insights と Service Map のクエリ

```
VMConnection
| where Direction == "outbound"
| summarize AggregatedValue = count() by RemoteIp
```

### <a name="bytes-received-by-protocol-name"></a>プロトコル名で受信したバイト数

#### <a name="wire-data-20-query"></a>Wire Data 2.0 のクエリ

```
WireData 
| where Direction == "Inbound"
| summarize AggregatedValue = sum(ReceivedBytes) by ProtocolName
```

#### <a name="vm-insights-and-service-map-query"></a>VM insights と Service Map のクエリ

```
VMConnection
| where Direction == "inbound"
| summarize AggregatedValue = sum(BytesReceived) by Protocol
```

### <a name="amount-of-network-traffic-in-bytes-by-process"></a>プロセスごとのネットワーク トラフィックの量 (バイト単位)

#### <a name="wire-data-20-query"></a>Wire Data 2.0 のクエリ

```
WireData
| summarize AggregatedValue = sum(TotalBytes) by ProcessName
```

#### <a name="vm-insights-and-service-map-query"></a>VM insights と Service Map のクエリ

```
VMConnection
| summarize sum(BytesReceived), sum(BytesSent) by ProcessName
```

### <a name="more-examples-queries"></a>その他のクエリの例

その他のクエリの例については、[VM insights のログ検索に関するドキュメント](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-log-search)と [VM insights のアラートに関するドキュメント](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-alerts#sample-alert-queries)を参照してください。

## <a name="uninstall-wire-data-20-solution"></a>Wire Data 2.0 ソリューションのアンインストール

Wire Data 2.0 をアンインストールする場合、必要なのは Log Analytics ワークスペースからソリューションを削除することのみです。  この結果は、次のようになります。

* ワークスペースに接続されている VM から Wire Data 管理パックが削除されます 
* Wire Data データ型がワークスペースに表示されなくなります

Wire Data ソリューションを削除するには、[こちらの手順](https://docs.microsoft.com/azure/azure-monitor/insights/solutions?tabs=portal#remove-a-monitoring-solution)に従ってください。

>[!NOTE]
>ワークスペースに Service Map または VM insights ソリューションがある場合、これらのソリューションでもこの管理パックが使用されるため、管理パックは削除されません。

### <a name="wire-data-20-management-packs"></a>Wire Data 2.0 管理パック

ワイヤ データが Log Analytics ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Windows サーバーに送信されます。 System Center Operations Manager エージェントを[接続された管理グループ](../agents/om-agents.md)で使用している場合は、Dependency Monitor 管理パックが System Center Operations Manager からデプロイされます。 エージェントが直接接続されている場合、管理パックは Azure Monitor によって配布されます。

この管理パックは、Microsoft.IntelligencePacks.ApplicationDependencyMonitor という名前で、 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs に書き込まれます。 管理パックで使用されるデータ ソースは、%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources&lt;AutoGeneratedID&gt;\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll です。

## <a name="uninstall-the-dependency-agent"></a>Dependency Agent のアンインストール

>[!NOTE]
>Wire Data を Service Map または VM insights に置き換える予定の場合、Dependency Agent は削除しないでください。

Dependency Agent を削除するには、次のセクションを使用します。  

### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows で Dependency Agent をアンインストールする

管理者は、コントロール パネルを使用して Dependency Agent for Windows をアンインストールできます。

また、%Programfiles%\Microsoft Dependency Agent\Uninstall.exe を実行して Dependency Agent をアンインストールすることもできます。

### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux で Dependency Agent をアンインストールする

Linux から Dependency Agent を完全にアンインストールするには、エージェント自体と、エージェントと共に自動的にインストールされたコネクタを削除する必要があります。 次の 1 つのコマンドを使用して、両方ともアンインストールできます。

```
rpm -e dependency-agent dependency-agent-connector
```

## <a name="using-the-wire-data-20-solution"></a>Wire Data 2.0 ソリューションの使用

Azure Portal の Log Analytics ワークスペースの **[概要]** ページで、 **[Wire Data 2.0]** タイルをクリックし、Wire Data ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

| **ブレード** | **説明** |
| --- | --- |
| ネットワーク トラフィックをキャプチャするエージェント | ネットワーク トラフィックをキャプチャしているエージェントの数を表示し、トラフィックをキャプチャしている上位 10 台のコンピューターを一覧表示します。 数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by Computer \| take 500000</code>) が実行されます。 リスト内のコンピューターをクリックすると、ログ検索が実行され、キャプチャされた合計バイト数が返されます。 |
| ローカル サブネット | エージェントが検出したローカル サブネットの数を表示します。  数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by LocalSubnet</code>) が実行され、すべてのサブネットと共に、各サブネット上で送信されたバイト数が一覧表示されます。 リスト内のサブネットをクリックすると、ログ検索が実行され、サブネット上で送信された合計バイト数が返されます。 |
| アプリケーション レベルのプロトコル | エージェントに検出された、使用中のアプリケーション レベルのプロトコルの数を示します。 数値をクリックすると、ログ検索 (<code>WireData \| summarize sum(TotalBytes) by ApplicationProtocol</code>) が実行されます。 プロトコルをクリックすると、ログ検索が実行され、そのプロトコルを使用して送信された合計バイト数が返されます。 |

![ワイヤ データ ダッシュボード](./media/wire-data/wire-data-dash.png)

**[ネットワーク トラフィックをキャプチャするエージェント]** ブレードを使用して、コンピューターが消費しているネットワーク帯域幅の量を確認することができます。 このブレードを使用すると、環境内で "_最もおしゃべりな_" コンピュータを簡単に見つけることができます。 そのようなコンピュータは過負荷になったり、異常動作を行ったり、通常より多くのネットワーク リソースを使用したりしている可能性があります。

![各コンピューターで使用されるネットワーク帯域幅が表示されている、Wire Data 2.0 ダッシュボードの [ネットワーク トラフィックをキャプチャするエージェント] ブレードのスクリーンショット。](./media/wire-data/log-search-example01.png)

同様に、 **[ローカル サブネット]** ブレードを使用して、サブネットを通過しているネットワーク トラフィックの量を確認することができます。 ユーザーは、多くの場合、アプリケーションにとって重要な領域の周辺にサブネットを定義します。 このブレードによって、これらの領域へのビューが提供されます。

![各ローカル サブネットによって消費されるネットワーク帯域幅を示す Wire Data 2.0 ダッシュボードの [ローカルサブネット] ブレードのスクリーンショット。](./media/wire-data/log-search-example02.png)

**[アプリケーション レベル プロトコル]** ブレードは、どのプロトコルが使用されているかを確認するのに役立ちます。 たとえば、ネットワーク環境で SSH が使用されていないと予想される場合があります。 このブレードで利用可能な情報を表示すると、すぐにこの予想の正誤を確認できます。

![各プロトコルによって使用されているネットワーク帯域幅を示す Wire Data 2.0 ダッシュボードの [アプリケーション レベル プロトコル] ブレードのスクリーンショット。](./media/wire-data/log-search-example03.png)

また、プロトコルのトラフィックが時間の経過とともに増加しているか減少しているかを知っておくと便利です。 たとえば、アプリケーションによって送信されているデータ量が増加している場合、注意すべきことや注目すべきことがある可能性があります。

## <a name="input-data"></a>入力データ

ワイヤ データでは、有効になっているエージェントを使用して、ネットワーク トラフィックについてのメタデータを収集します。 各エージェントは、約 15 秒おきにデータを送信します。

## <a name="output-data"></a>出力データ

入力データの種類ごとに、種類が _WireData_ であるレコードが作成されます。 WireData レコードには、次の表に示したプロパティがあります。

| プロパティ | 説明 |
|---|---|
| Computer | データが収集されたコンピューター名 |
| TimeGenerated | レコードの時刻 |
| LocalIP | ローカル コンピューターの IP アドレス |
| SessionState | 接続または切断 |
| ReceivedBytes | 受信したバイト数 |
| ProtocolName | 使用されるネットワーク プロトコルの名前 |
| IPVersion | IP バージョン |
| Direction | 受信または送信 |
| MaliciousIP | 既知の悪意のある発信元の IP アドレス |
| 重大度 | 疑いのあるマルウェアの重大度 |
| RemoteIPCountry | リモート IP アドレスの国や地域 |
| ManagementGroupName | Operations Manager 管理グループの名前 |
| SourceSystem | データが収集されたソース |
| SessionStartTime | セッションの開始時刻 |
| SessionEndTime | セッションの終了時刻 |
| LocalSubnet | データが収集されたサブネット |
| LocalPortNumber | ローカル ポート番号 |
| RemoteIP | リモート コンピューターで使用されるリモート IP アドレス |
| RemotePortNumber | リモート IP アドレスで使用されるポート番号 |
| SessionID | 2 つの IP アドレス間の通信セッションを識別する一意の値 |
| SentBytes | 送信されたバイト数 |
| TotalBytes | セッション中に送信された合計バイト数 |
| ApplicationProtocol | 使用されるネットワーク プロトコルの種類   |
| ProcessID | Windows プロセス ID |
| ProcessName | プロセスのパスとファイル名 |
| RemoteIPLongitude | IP の経度の値 |
| RemoteIPLatitude | IP の緯度の値 |

## <a name="next-steps"></a>次のステップ

- 仮想マシンの監視を有効にするための要件と方法については、[VM insights のデプロイ](./vminsights-enable-overview.md)に関するページを参照してください。
- [ログを検索](../logs/log-query-overview.md) して、詳細なワイヤ データ検索レコードを確認します。
