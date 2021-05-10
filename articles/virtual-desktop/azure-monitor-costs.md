---
title: Windows Virtual Desktop のコストと価格の見積もりを監視する - Azure
description: Windows Virtual Desktop 向けの Azure Monitor を使用してコストと価格を見積もる方法。
author: Heidilohr
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 26262f83e14602d6ea93f96ec47630ef870c357d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309307"
---
# <a name="estimate-azure-monitor-costs"></a>Azure Monitor コストを見積もる

Azure Monitor ログは、環境によって生成されたデータを収集、インデックス、および格納するサービスです。 このため、Azure Monitor 価格モデルは、Log Analytics ワークスペースによって 1 日あたりギガバイト単位で取得され、処理される (または "取り込まれた") データの量に基づいています。 Log Analytics ワークスペースのコストは、収集されるデータの量だけでなく、選択した Azure 支払いプランと、環境で生成されるデータを格納するために選択した期間に基づいています。

この記事では、Azure Monitor の価格のしくみを理解するのに役立つ次の事項について説明します。

- この機能を有効にする前にデータ インジェストとストレージ コストを事前に見積もる方法
- この機能を使用する場合のコストを削減するためにインジェストとストレージを測定および制御する方法

>[!NOTE]
> この記事に記載されているすべてのサイズと価格は、見積もりのしくみを示す例にすぎません。 Azure Monitor Log Analytics の価格モデルと Azure リージョンに基づく、より正確な評価については、「[Azure Monitor 価格](https://azure.microsoft.com/pricing/details/monitor/)」をご覧ください。

## <a name="estimate-data-ingestion-and-storage-costs"></a>データ インジェストとストレージ コストを見積もる

Log Analytics ワークスペースでログとして書き込まれた定義済みのデータのセットを使用することをお勧めします。 次の見積もりの例では、既定の構成で課金対象データを確認します。

Windows Virtual Desktop 向けの Azure Monitor の定義済みデータセットは次のとおりです。

- セッション ホストからのパフォーマンス カウンター
- セッション ホストからの Windows イベント ログ
- サービス インフラストラクチャからの Windows Virtual Desktop 診断

データ インジェストとストレージ コストは、環境のサイズ、正常性、使用状況によって異なります。 この記事の例では、予想されるコスト範囲を計算するために、[仮想マシンのサイズ設定のガイドライン](/windows-server/remote/remote-desktop-services/virtual-machine-recs)に基づいた、ライトからパワーの電力使用量の正常な仮想マシンに基づいて、データ インジェストとストレージ コストの範囲を予想します。

この例で使用するライト電力使用量の VM には、次のコンポーネントが含まれています。

- 4 つの vCPU、1 つのディスク
- 1 日あたり 16 セッション
- 平均セッション時間は2時間 (120 分)
- セッションあたり 100 プロセス

この例で使用するパワー電力使用量の VM には、次のコンポーネントが含まれています。

- 6 つの vCPU、1 つのディスク
- 1 日あたり 6 セッション
- 平均セッション時間は 4 時間 (240 分)
- セッションあたり 200 プロセス

## <a name="estimating-performance-counter-ingestion"></a>パフォーマンス カウンター インジェストの見積もり

パフォーマンス カウンターは、システム リソースがどのように実行されているかを示します。 パフォーマンス カウンターのデータ インジェストは、環境のサイズと使用状況によって異なります。 ほとんどの場合、パフォーマンス カウンターは、Windows Virtual Desktop 向けの Azure Monitor のデータ インジェストの 80～99% を構成するはずです。

見積もりを開始する前に、各パフォーマンス カウンターが特定の頻度でデータを送信するということを理解しておくことが重要です。 既定のサンプル レートを分単位で設定します (このレートは設定で編集することもできます) が、カウンターに応じて異なる乗算係数で適用されます。 次の要因がレートに影響します。

- 各カウンターは、仮想マシン (VM) ごとに、VM の実行中の 1 分あたりの既定のサンプル レートで、環境内の VM ごとにデータを送信します。 これらのカウンターが 1 日に送信するレコードの数を見積もるには、1 分あたりの既定のサンプル レートを環境内の VM 数で乗算し、その数に 1 日あたりの平均 VM 実行時間を乗算します。

   まとめ

   1 分あたりの既定のサンプル レート× VM SKU の CPU コア数 × VM の数 × 1 日あたりの VM の平均実行時間 = 1 日あたりの送信レコード数

- CPU ごとの係数について、各カウンターは、VM の実行中に環境内の各 VM の vCPU あたりの既定のサンプル レート (1 分あたり) を送信します。 カウンターが 1 日あたりに送信するレコードの数を見積もるには、1 分あたりの既定のサンプル レートを VM SKU の CPU コア数で乗算し、その数に VM の実行時間 (分) と環境内の VM の数を乗算します。

   まとめ
   
   1 分あたりの既定のサンプル レート× VM SKU の CPU コア数 × VM の実行時間 (分) × VM の数 = 1 日あたりの送信レコード数

- ディスクごとの係数について、各カウンターは、環境内の各 VM の各ディスクの既定のサンプル レートでデータを送信します。 これらのカウンターで 1 日に送信されるレコードの数は、1 分あたりの既定のサンプル レートと VM SKU のディスク数を乗算し、それに 1 時間あたり 60 分を乗算し、最後に VM の平均アクティブ時間を乗算した値と等しくなります。

   まとめ

   1 分あたりの既定のサンプル レート× VM SKU のディスク数 × 1 時間あたり 60 分 × VM の数 × 1 日あたりの VM の平均実行時間 = 1 日あたりの送信レコード数

- セッションごとの係数について、各カウンターは、セッションが接続されている間、環境内の各セッションの既定のサンプル レートでデータを送信します。 これらのカウンターが 1 日に送信するレコードの数を見積もるには、1 分あたりの既定のサンプル レートに 1 日あたりの平均セッション数と平均セッション時間を乗算します。

   まとめ

   1 分あたりの既定のサンプル レート× 1 日あたりのセッション数 × 平均セッション時間 = 1 日あたりの送信レコード数

- プロセスごとの係数について、各カウンターは、環境内の各セッションの各プロセスの既定のレートでデータを送信します。 これらのカウンターが 1 日に送信するレコードの数を見積もるには、1 分あたりの既定のサンプル レートに 1 日あたりの平均セッション数を乗算し、その値に平均セッション時間とセッションあたりの平均プロセス数を乗算します。
  
   まとめ

   1 分あたりの既定のサンプル レート × 1 日あたりのセッション数 × 平均セッション時間 × セッションあたりの平均プロセス数 = 1 日あたりの送信レコード数

次の表は、Windows Virtual Desktop 向けの Azure Monitor が収集する 20 個のパフォーマンス カウンターとその既定の料金を示しています。

| カウンター名 | 既定のサンプル レート | 頻度の係数 |
|--------------|---------------------|------------------|
| Logical Disk(C:)\\ 空き領域 (%) | 60 秒  | ディスクあたり             |
| Logical Disk(C:)\\Avg.ディスク キューの長さ   | 30 秒    | ディスクあたり             |
| Logical Disk(C:)\\Avg.Disk sec/Transfer  | 60 秒       | ディスクあたり             |
| Logical Disk(C:)\\Current Disk Queue Length  | 30 秒      | ディスクあたり             |
| Memory(\*)\\Available Mbytes | 30 秒    | VM あたり  |
| Memory(\*)\\Page Faults/sec | 30 秒   | VM あたり  |
| Memory(\*)\\Pages/sec | 30 秒   | VM あたり  |
| Memory(\*)\\% Committed Bytes in Use | 30 秒    | VM あたり  |
| PhysicalDisk(\*)\\Avg.ディスク キューの長さ | 30 秒      | ディスクあたり             |
| PhysicalDisk(\*)\\Avg.Disk sec/Read | 30 秒  | ディスクあたり             |
| PhysicalDisk(\*)\\Avg.Disk sec/Transfer | 30 秒  | ディスクあたり             |
| PhysicalDisk(\*)\\Avg.Disk sec/Write | 30 秒 | ディスクあたり             |
| Processor Information(_Total)\\% Processor Time | 30 秒 | コア/CPU あたり         |
| Terminal Services(\*)\\Active Sessions          | 60 秒 | VM あたり  |
| Terminal Services(\*)\\Inactive Sessions        | 60 秒 | VM あたり  |
| Terminal Services(\*)\\Total Sessions | 60 秒 | VM あたり  |
| User Input Delay per Process(\*)\\Max Input Delay         | 30 秒 | プロセスあたり          |
| User Input Delay per Session(\*)\\Max Input Delay        | 30 秒 | セッションごと          |
| RemoteFX Network(\*)\\Current TCP RTT | 30 秒 | VM あたり  |
| RemoteFX Network(\*)\\Current UDP Bandwidth     | 30 秒 | VM あたり  |

各レコードのサイズを 200 バイトに見積もると、既定のサンプル レートでライト ワークロードを実行している VM の例では、VM ごとに 1 日あたり約 90 メガバイトのパフォーマンス カウンター データが送信されます。 一方、パワー ワークロードを実行する VM の例では、VM ごとに 1 日あたり約 130 メガバイトのパフォーマンス カウンター データが送信されます。 ただし、レコードのサイズと環境の使用状況は異なる場合があるため、配置で使用される 1 日あたりのメガバイト数は異なる場合があります。

入力遅延パフォーマンス カウンターの詳細については、[ユーザー入力遅延のパフォーマンス カウンター](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)に関する記事を参照してください。

## <a name="estimating-windows-event-log-ingestion"></a>Windows イベント ログ インジェストの見積もり

Windows イベント ログは、Windows 仮想マシン上の Log Analytics エージェントによって収集されるデータ ソースです。 システムやアプリケーションなどの標準ログのイベントに加えて、監視が必要なアプリケーションによって作成されるカスタム ログも収集できます。

Windows Virtual Desktop 向けの Azure Monitor の既定の Windows イベントは次のとおりです。

- Application
- Microsoft-Windows-TerminalServices-RemoteConnectionManager/Admin
- Microsoft-Windows-TerminalServices-LocalSessionManager/Operational
- システム
- Microsoft-FSLogix-Apps/Operational
- Microsoft-FSLogix-Apps/Admin

Windows イベントは、環境内でイベントの条件が満たされるたびに送信されます。 正常な状態のマシンは、異常な状態のマシンよりも少数のイベントを送信します。 イベント数は予測できないため、この見積もりために正常な環境からの例に基づいて、1 日あたり 1,000 から 10,000 イベントまでの範囲を使用します。 たとえば、この例の各イベント レコード サイズを 1,500 バイトに見積もると、指定した環境で 1 日あたり約 2～15 メガバイトのイベント データが返されます。

Windows イベントの詳細については、[Windows イベント レコードのプロパティ](../azure-monitor/agents/data-sources-windows-events.md)に関するページを参照してください。

## <a name="estimating-diagnostics-ingestion"></a>診断インジェストの見積もり

診断サービスは、ユーザーと管理者の両方のアクションのアクティビティ ログを作成します。

診断カウンターが追跡するアクティビティ ログの名前を次に示します。

- WVDCheckpoints
- WVDConnections
- WVDErrors
- WVDFeeds
- WVDManagement
- WVDAgentHealthStatus

サービスは、環境がレコードを作成するために必要な条件を満たしている場合は常に診断情報を送信します。 診断レコード数は予測できないため、この見積もりために正常な環境からの例に基づいて、1 日あたり 500 から 1,000 イベントまでの範囲を使用します。

たとえば、この例の各診断レコード サイズを 200 バイトに見積もると、インジェスト データの合計は 1 日あたりの VM あたり 1 MB 未満になります。

アクティビティ ログのカテゴリの詳細については、「[Windows Virtual Desktop の診断](diagnostics-log-analytics.md)」をご覧ください。

## <a name="estimating-total-costs"></a>合計コストの見積もり

最後に、総コストを見積もります。 この例では、前のセクションの例の値に基づいて、次の結果を取得するとします。

| データ ソース        | 1 日あたりの見積もりサイズ (メガバイト単位)   |
|-------------------------------------|------------------------------------------|
| パフォーマンス カウンター   | 90 - 130 |
| イベント    | 2 - 15 |
| Windows Virtual Desktop 診断 | \< 1 |

この例では、Windows Virtual Desktop 向けの Azure Monitor の合計インジェスト データは、1 日あたりの VM あたり 92～145 メガバイトです。 つまり、各 VM は 31 日ごとに約 3～5 ギガバイトのデータをインジェストします。

[Log Analytics 価格](https://azure.microsoft.com/pricing/details/monitor/)に既定の従量課金制モデルを使用すると、1 月あたりの Azure Monitor データ収集とストレージ コストを見積もることができます。 データ インジェストに応じて、Log Analytics 価格の容量予約モデルを検討することもできます。

## <a name="manage-your-data-ingestion-to-reduce-costs"></a>データ インジェストを管理してコストを削減する

このセクションでは、コストを削減するためにデータ インジェストを測定および管理する方法について説明します。

ブックの権限とアクセス許可を管理する方法の詳細については、「[アクセス制御](../azure-monitor/visualize/workbooks-access-control.md)」をご覧ください。

>[!NOTE]
>データ ポイントを削除すると、Windows Virtual Desktop 向けの Azure Monitor の対応するビジュアルに影響します。

### <a name="log-analytics-settings"></a>Log Analytics の設定

データ インジェストを管理するために Log Analytics の設定を最適化するための推奨事項を次に示します。

- Windows Virtual Desktop リソースの指定された Log Analytics ワークスペースを使用して、Log Analytics が Windows Virtual Desktop 展開内の仮想マシンのパフォーマンス カウンターとイベントのみを収集するようにします。
- Log Analytics ストレージの設定を調整してコストを管理します。 保有期間を短縮したり、固定ストレージ価格レベルがコスト効率に優れているかどうかを評価したり、異常な展開の影響を制限するためにインジェストできるデータ量に境界を設定したりすることができます。 詳細については、「[Azure Monitor ログの使用量とコストを管理する](../azure-monitor/platform/manage-cost-storage.md)」をご覧ください。

### <a name="remove-excess-data"></a>余分なデータを削除する

既定の構成は、Windows Virtual Desktop 向けの Azure Monitor に推奨される唯一のデータ セットです。 データ ポイントを追加してホスト診断で表示するオプションが常にあります。ブラウザーをホストするか、カスタム グラフを作成します。ただし、データを追加すると Log Analytics コストが増加します。 コストを削減するために、これらを削除することができます。

### <a name="measure-and-manage-your-performance-counter-data"></a>パフォーマンス カウンター データの測定と管理

実際の監視コストは、環境のサイズ、使用状況、正常性によって異なります。 Log Analytics ワークスペースでデータ インジェストの測定方法を理解するには、「[インジェスト ログ データ ボリュームについて](../azure-monitor/logs/manage-cost-storage.md#understanding-ingested-data-volume)」をご覧ください。

セッション ホストが使用しているパフォーマンス カウンターは、Windows Virtual Desktop 向けの Azure Monitor のインジェスト データの最大のソースになる可能性があります。 Log Analytics ワークスペースの次のカスタム クエリ テンプレートでは、過去 1 日のパフォーマンス カウンターあたりの頻度とインジェスト メガバイト数を追跡できます。

```azure
let WVDHosts = dynamic(['Host1.MyCompany.com', 'Host2.MyCompany.com']);
Perf
| where TimeGenerated > ago(1d)
| where Computer in (WVDHosts)
| extend PerfCounter = strcat(ObjectName, ":", CounterName)
| summarize Records = count(TimeGenerated), InstanceNames = dcount(InstanceName), Bytes=sum(_BilledSize) by PerfCounter
| extend Billed_MBytes = Bytes / (1024 * 1024), BytesPerRecord = Bytes / Records
| sort by Records desc
```

>[!NOTE]
>テンプレートのプレースホルダー値を実際の環境で使用する値に置き換えてください。そうしないと、クエリは機能しません。

このクエリでは、Windows Virtual Desktop 向けの Azure Monitor の既定のカウンターだけではなく、環境で有効にしたすべてのパフォーマンス カウンターが表示されます。 この情報は、カウンターの頻度を下げたり、カウンターを完全に削除したりするなど、コストを削減するためにターゲットとなる領域を理解するのに役立ちます。

また、パフォーマンス カウンターを削除することによってコストを削減することもできます。 パフォーマンス カウンターを削除したり、既存のカウンターを編集して頻度を下げたりする方法については、「[パフォーマンスカウンターの構成](../azure-monitor/platform/data-sources-performance-counters.md#configuring-performance-counters)」をご覧ください。

### <a name="manage-windows-event-logs"></a>Windows イベント ログを管理する

Windows イベントは、すべてのホストが正常な場合にデータ インジェストが急増することはほとんどありません。 異常なホストは、ログに送信されるイベントの数を増やすことがありますが、この情報はホストの問題を修正するために重要な場合があります。 そのままにしておくことをお勧めします。 Windows イベント ログの管理方法の詳細については、「[Windows イベント ログの構成](../azure-monitor/agents/data-sources-windows-events.md#configuring-windows-event-logs)」をご覧ください。

### <a name="manage-diagnostics"></a>診断を管理する

Windows Virtual Desktop 診断は、データ ストレージのコストの 1% 未満にあたるため、削除することはお勧めしません。 Windows Virtual Desktop 診断を管理するには、「[診断機能に Log Analytics を使用する](diagnostics-log-analytics.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Windows Virtual Desktop 向けの Azure Monitor の詳細については、次の記事をご覧ください。

- 「[Windows Virtual Desktop 向けの Azure Monitor を使用してデプロイを監視する](azure-monitor.md)」
- 用語と概念の詳細については、[用語集](azure-monitor-glossary.md)をご覧ください。
- 問題が発生した場合は、[トラブルシューティング ガイド](troubleshoot-azure-monitor.md)をご覧ください。
- 監視コストの管理の詳細については、「[Azure Monitor での使用量と推定コストの監視](../azure-monitor/usage-estimated-costs.md)」をご覧ください。
