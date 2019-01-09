---
title: Azure 間レプリケーションの問題とエラーに関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするとき発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 4a18e009f7defc8d41846b867f9b7a65d2b853dd
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/03/2019
ms.locfileid: "53993333"
---
# <a name="troubleshoot-azure-to-azure-vm-ongoing-replication-issues"></a>実行中の Azure 間の VM レプリケーションに関する問題のトラブルシューティング

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題と、そのトラブルシューティング方法について説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。


## <a name="recovery-points-not-getting-generated"></a>復旧ポイントが生成されない

エラー メッセージ:No crash consistent recovery point available for the VM in the last 60 minutes. (VM で使用できる 60 分以内のクラッシュ コンシステント復旧ポイントがありません。)</br>
エラー ID:153007 </br>

Azure Site Recovery は、一貫してソース リージョンからディザスター リカバリー リージョンにデータをレプリケートし、クラッシュ コンシステント ポイントを 5 分ごとに作成します。 Site Recovery が 60 分間にわたって復旧ポイントを作成できないと、ユーザーが警告されます。 このエラーを引き起こす可能性がある原因を次に示します。

**原因 1:[ソース仮想マシンにおけるデータ変更率が高い](#high-data-change-rate-on-the-source-virtal-machine)**    
**原因 2:[ネットワーク接続の問題](#Network-connectivity-issue)**

## <a name="causes-and-solutions"></a>原因とソリューション

### <a name="high-data-change-rate-on-the-source-virtal-machine"></a>ソース仮想マシンにおけるデータ変更率が高い
Azure Site Recovery がイベントを起動するのは、ソース仮想マシン上のデータ変更率が、サポートされている制限よりも高い場合です。 問題が高チャーンによるものかどうか確認するには、[レプリケートされたアイテム]> [VM] に移動して、[Events -last 72 hours]\(イベント - 過去 72 時間\) をクリックします。
次のスクリーンショットに示されるように、[データ変更率がサポートされている制限を超えています] というイベントが表示されるはずです。

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

このイベントをクリックすると、次のスクリーンショットのように正確なディスク情報が表示されます。

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


#### <a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限
以下の表は、Azure Site Recovery の制限を示したものです。 上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 また、ディスクごとのデータ チャーンと仮想マシンごとのデータ チャーンという考慮すべき 2 つの制限があることにも注意する必要があります。
たとえば、次の表の Premium P20 ディスクを見ると、Site Recovery はディスクごとに 5 MB/s のチャーンを処理できます。VM あたりのディスクの最大数が 5 であるため、VM ごとの合計チャーンの制限は 25 MB/s になります。

**レプリケーション先のストレージ** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB  | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |  (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |10 MB/s | (ディスクあたり) 842 GB

### <a name="solution"></a>解決策
Azure Site Recovery のデータ変更率制限はディスクの種類に基づいていることを理解する必要があります。 この問題が繰り返されているか一時的なものかを判断するには、影響を受ける仮想マシンのデータ変更率のパターンを確認することが重要です。
影響を受ける仮想マシンのデータ変更率を確認するには次のようにします。 [ソース仮想マシン] > [監視] の下のメトリックに移動し、次に示すメトリックを追加します。

![high_data_change_rate](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. [メトリックの追加] をクリックし、[OS Disk Write Bytes/sec]\(OS ディスク書き込みバイト/秒\) と [Data Disk Write Bytes/sec]\(データ ディスク書き込みバイト/秒\) を追加します。
2. スクリーンショットに表示される値の急上昇を監視します。
3. OS ディスクと結合されたすべてのデータ ディスクで発生している書き込み操作の合計が表示されます。 これらのメトリックでは、ディスク レベルあたりの情報は示されませんが、合計データ チャーン パターンの指標として役立ちます。

上記のようなケースでは、データの急激な増加が時折しか発生せず、データ変更率が一時的に 10 MBps (Premium の場合) と 2 MBps (Standard の場合) を超えてから低下する場合、レプリケーションは追いつきます。 ただし、ほとんどの時間において、サポートされる制限をチャーンが大きく下回っている場合、可能であれば、次のいずれかのオプションを検討してください。

**オプション 1:** 高データ変更率を引き起こしているディスクを除外します。 </br>
現在、[Site Recovery Powershell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) を使用してディスクを除外できます。

**オプション 2:** ディザスター リカバリー ストレージ ディスク層を変更します。 </br>
このオプションを使用できるのは、ディスク データ チャーンが 10 MB/s 未満の場合です。 たとえば、P10 ディスクを含む VM で、8 MB/s を超えて 10 MB/s 未満のデータ チャーンが発生しているとします。 顧客が保護中にターゲット ストレージのために P30 ディスクを使用できる場合、問題は解決できます。

### <a name="Network-connectivity-issue"></a>ネットワーク接続の問題

#### <a name="network-latency-to-cache-storage-account-"></a>キャッシュ ストレージ アカウントへのネットワーク待機時間:
 サイトの回復は、キャッシュ ストレージ アカウントにレプリケートされたデータを送信し、仮想マシンからキャッシュ ストレージ アカウントへのデータのアップロードが 3 秒に 4 MBより遅い場合、問題が発生する可能性があります。 待ち時間に関連する問題があるかどうかをチェックするには、[azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)を使用して、仮想マシンから、キャッシュ ストレージ アカウントにデータをアップロードします。<br>
待ち時間が長い場合は、VM からの送信ネットワーク トラフィックを制御するためのネットワーク仮想アプライアンスを使用しているかどうか確認します。 この場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスがスロットルされる可能性があります。 レプリケーション トラフィックが NVA に送られないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することをお勧めします。 [ネットワーク仮想アプライアンスの構成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)を参照してください

#### <a name="network-connectivity"></a>ネットワーク接続
Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。</br>
すべての URL が接続されていることを確認するには[Site Recovery URL に対する送信接続](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)を参照してください 