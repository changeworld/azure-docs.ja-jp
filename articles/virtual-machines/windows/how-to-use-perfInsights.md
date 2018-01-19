---
title: "Microsoft Azure で PerfInsights を使用する方法 | Microsoft Docs"
description: "PerfInsights を使用して Windows VM のパフォーマンスに関する問題のトラブルシューティングを行う方法を説明します。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: f15875610e2035c6f4c10c36e19c02f3e045b3ea
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2017
---
# <a name="how-to-use-perfinsights"></a>PerfInsights を使用する方法 

[PerfInsights](http://aka.ms/perfinsightsdownload) は、役立つ診断情報を収集する自動化されたスクリプトです。 また、I/O ストレス読み込みを実行し、Azure で Windows 仮想マシンのパフォーマンスの問題をトラブルシューティングするのに役立つ分析レポートを提供します。 これは、仮想マシンでスタンドアロン スクリプトとして実行することも、[Azure パフォーマンス診断 VM 拡張機能](performance-diagnostics-vm-extension.md)をインストールしてポータルから直接実行することもできます。

仮想マシンでパフォーマンスの問題が発生している場合は、サポートに連絡する前にこのスクリプトを実行してください。

## <a name="supported-troubleshooting-scenarios"></a>サポートされているトラブルシューティングのシナリオ

PerfInsights は、いくつかの種類の情報を収集して分析できます。 以降のセクションでは、一般的なシナリオについて説明します。

### <a name="collect-basic-configuration"></a>収集の基本構成 

このシナリオでは、ディスク構成やその他の重要な情報を収集します。これには次のものが含まれます。

-   イベント ログ

-   すべての着信および発信接続のネットワークの状態

-   ネットワークとファイアウォールの構成設定

-   システムで現在実行されているすべてのアプリケーションのタスク一覧

-   仮想マシンに対して msinfo32 によって作成された情報ファイル

-   Microsoft SQL Server データベースの構成設定 (SQL Server を実行しているサーバーとして VM が識別されている場合)

-   ストレージの信頼性カウンター

-   重要な Windows 修正プログラム

-   インストールされているフィルター ドライバー

これは、システムに影響しない情報のパッシブ コレクションです。 

>[!Note]
>このシナリオは、次の各シナリオに自動的に追加されます。

### <a name="benchmarking"></a>ベンチマーク

このシナリオでは、VM に接続されているすべてのドライブに対して [Diskspd](https://github.com/Microsoft/diskspd) ベンチマーク テスト (IOPS および MBPS) を実行します。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

### <a name="slow-vm-analysis"></a>低速 VM の分析 

このシナリオでは、Generalcounters.txt ファイルで指定されているカウンターを使用して、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) トレースを実行します。 VM が、SQL Server を実行しているサーバーとして識別された場合は、パフォーマンス カウンター トレースを実行します。 それを、Sqlcounters.txt ファイル内にあるカウンターを使用して実行します。ここには、パフォーマンス診断データも含まれています。

### <a name="slow-vm-analysis-and-benchmarking"></a>低速 VM の分析とベンチマーク

このシナリオでは、[パフォーマンス カウンター](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) トレースに続けて [Diskspd](https://github.com/Microsoft/diskspd) ベンチマーク テストを実行します。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

### <a name="azure-files-analysis"></a>Azure Files 分析 

このシナリオでは、特別なパフォーマンス カウンター キャプチャと、ネットワーク トレースを実行します。 このキャプチャには、すべてのサーバー メッセージ ブロック (SMB) クライアント共有カウンターが含まれています。 キャプチャに含まれる重要な SMB クライアント共有のパフォーマンス カウンターをいくつか次に示します。

| **種類**     | **SMB クライアント共有カウンター** |
|--------------|-------------------------------|
| IOPS         | データ要求数/秒             |
|              | 読み取り要求数/秒             |
|              | 書き込み要求数/秒            |
| Latency      | 平均秒数/データ要求         |
|              | 平均秒数/読み取り                 |
|              | 平均秒数/書き込み                |
| IO サイズ      | Avg.バイト/データ要求       |
|              | Avg.バイト/読み取り               |
|              | Avg.バイト/書き込み              |
| Throughput   | データ バイト数/秒                |
|              | 読み取りバイト数/秒                |
|              | 書き込みバイト数/秒               |
| キューの長さ | Avg.読み取りキューの長さ        |
|              | Avg.書き込みキューの長さ       |
|              | Avg.データ キューの長さ        |

### <a name="custom-slow-vm-analysis"></a>低速 VM のカスタム分析 

低速 VM のカスタム分析を実行する場合は、並行して実行するトレースを選択します。 必要に応じて、それらのすべて (パフォーマンス カウンター、Xperf、ネットワーク、および StorPort) を実行できます。 トレースが完了した後、このツールは Diskspd ベンチマークを実行します (選択されている場合)。 

> [!Note]
> このシナリオはシステムに影響する可能性があるため、稼働中の運用システムでは実行しないでください。 また、このシナリオは、問題を回避するために、必要に応じて、専用のメンテナンス期間に実行してください。 トレースまたはベンチマーク テストによってワークロードが増加すると、VM のパフォーマンスに悪影響を及ぼす場合があります。
>

## <a name="what-kind-of-information-is-collected-by-the-script"></a>スクリプトで収集される情報の種類

Windows VM、ディスクまたは記憶域プールの構成、パフォーマンス カウンター、ログ、およびさまざまなトレースに関する情報が収集されます。 これは、使用しているパフォーマンス シナリオによって異なります。 次の表で詳細に説明します。

|収集されるデータ                              |  |  | パフォーマンス シナリオ |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                              | 収集の基本構成 | ベンチマーク | 低速 VM の分析 | 低速 VM の分析とベンチマーク | Azure Files 分析 | 低速 VM のカスタム分析 |
| イベント ログの情報      | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| システム情報               | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ボリューム マップ                       | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ディスク マップ                         | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| 実行中のタスク                    | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ストレージの信頼性カウンター     | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ストレージ情報              | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| Fsutil 出力                    | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| フィルター ドライバー情報               | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| Netstat 出力                   | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ネットワーク構成            | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| ファイアウォールの構成           | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| SQL Server 構成         | [はい]                        | [はい]                                | [はい]                      | [はい]                            | [はい]                  | [はい]                  |
| パフォーマンス診断トレース * | [はい]                        | [はい]                                | [はい]                      |                                | [はい]                  | [はい]                  |
| パフォーマンス カウンター トレース **     |                            |                                    |                          |                                |                      | [はい]                  |
| SMB カウンター トレース **             |                            |                                    |                          |                                | [はい]                  |                      |
| SQL Server カウンター トレース **      |                            |                                    |                          |                                |                      | [はい]                  |
| XPerf トレース                      |                            |                                    |                          |                                |                      | [はい]                  |
| StorPort トレース                   |                            |                                    |                          |                                |                      | [はい]                  |
| ネットワーク トレース                    |                            |                                    |                          |                                | [はい]                  | [はい]                  |
| Diskspd ベンチマーク トレース ***      |                            | [はい]                                |                          | [はい]                            |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>パフォーマンス診断トレース (*)

バックグラウンドでルール ベースのエンジンを実行してデータを収集し、継続的なパフォーマンスの問題を診断します。 現在サポートされているルールは次のとおりです。

- HighCpuUsage ルール: CPU 使用率が高い期間を検出し、その期間で CPU 使用率が最も高いコンシューマーを表示します。
- HighDiskUsage ルール: 物理ディスクでディスク使用量が多い期間を検出し、その期間でディスク使用量が最も多いコンシューマーを表示します。
- HighResolutionDiskMetric ルール: 物理ディスクごとに 50 ミリ秒あたりの IOPS、スループット、および IO 待機時間の指標を表示します。 ディスク調整期間をすばやく特定するときに役立ちます。
- HighMemoryUsage ルール: メモリ使用量が多い期間を検出し、その期間でメモリ使用量が最も多いコンシューマーを表示します。

> [!NOTE] 
> 現時点では、.NET Framework 3.5 以降のバージョンが搭載された Windows バージョンがサポートされています。

### <a name="performance-counter-trace-"></a>パフォーマンス カウンター トレース (**)

次のパフォーマンス カウンターを収集します。

- \Process、\Processor、\Memory、\Thread、\PhysicalDisk、および \LogicalDisk
- \Cache\Dirty Pages、\Cache\Lazy Write Flushes/sec、\Server\Pool Nonpaged、Failures、および \Server\Pool Paged Failures
- \Network Interface、\IPv4\Datagrams、\IPv6\Datagrams、\TCPv4\Segments、\TCPv6\Segments、\Network Adapter、\WFPv4\Packets、\WFPv6\Packets、\UDPv4\Datagrams、\UDPv6\Datagrams、\TCPv4\Connection、\TCPv6\Connection、\Network QoS Policy\Packets、\Per Processor Network Interface Card Activity、および \Microsoft Winsock BSP の選択されたカウンター

#### <a name="for-sql-server-instances"></a>SQL Server インスタンス向け
- \SQL Server:Buffer Manager、\SQLServer:Resource Pool Stats、および \SQLServer:SQL Statistics\
- \SQLServer:Locks、\SQLServer:General、Statistics
- \SQLServer:Access Methods

#### <a name="for-azure-files"></a>Azure Files 向け
\SMB Client Shares

### <a name="diskspd-benchmark-trace-"></a>Diskspd ベンチマーク トレース (***)
Diskspd I/O ワークロード テスト (OS ディスク [書き込み] とプール ドライブ [読み取り/書き込み])

## <a name="run-the-perfinsights-script-on-your-vm"></a>VM で PerfInsights スクリプトを実行する

### <a name="what-do-i-have-to-know-before-i-run-the-script"></a>スクリプトを実行する前の確認事項 

#### <a name="script-requirements"></a>スクリプトの要件

-  このスクリプトは、パフォーマンスに問題がある VM で実行する必要があります。 

-  Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2、Windows Server 2016、Windows 8.1、および Windows 10 のオペレーティング システムがサポートされています。

#### <a name="possible-problems-when-you-run-the-script-on-production-vms"></a>運用 VM でこのスクリプトを実行するときに発生する可能性のある問題

-  Xperf または Diskspd を使用するように構成された任意のベンチマーク シナリオまたは "低速 VM のカスタム分析" シナリオの場合、このスクリプトは VM のパフォーマンスに悪影響を与える可能性があります。 運用環境ではこれらのシナリオを実行しないでください。

-  Diskspd を使用するように構成された任意のベンチマーク シナリオまたは "低速 VM のカスタム分析" シナリオの場合は、I/O ワークロードに干渉するバックグラウンド アクティビティが他に存在しないことを確認してください。

-  既定では、スクリプトは、一時ストレージ ドライブを使用してデータを収集します。 トレースが長時間有効になっている場合は、収集されたデータ量が関連している可能性があります。 これにより使用できる一時ディスク上の領域が減ることがあるため、このドライブに依存するすべてのアプリケーションに影響を与える場合があります。

### <a name="how-do-i-run-perfinsights"></a>PerfInsights の実行方法 

PerfInsights は、[Azure パフォーマンス診断 VM 拡張機能](performance-diagnostics-vm-extension.md)をインストールすることによって仮想マシンで実行できます。 また、スタンドアロン スクリプトとしても実行できます。 

**Azure Portal から PerfInsights をインストールして実行する**

このオプションの詳細については、「[Install Azure Performance Diagnostics VM Extension (Azure パフォーマンス診断 VM 拡張機能をインストールする)](performance-diagnostics-vm-extension.md#install-the-extension)」を参照してください。  

**スタンドアロン モードで PerfInsights スクリプトを実行する**

PerfInsights スクリプトを実行するには、次の手順のようにします。


1. [PerfInsights.zip](http://aka.ms/perfinsightsdownload) をダウンロードします。

2. PerfInsights.zip ファイルのブロックを解除します。 これを行うには、PerfInsights.zip ファイルを右クリックし、**[プロパティ]** を選択します。 **[全般]** タブで **[ブロックの解除]** を選択し、**[OK]** を選択します。 これにより、追加のセキュリティ プロンプトは表示されずに、確実にスクリプトが実行されます。  

    ![[ブロックの解除] が強調表示された PerfInsights プロパティのスクリーンショット](media/how-to-use-perfInsights/unlock-file.png)

3.  圧縮された PerfInsights.zip ファイルを一時ドライブ (既定では通常、D ドライブ) に展開します。 圧縮されたファイルには、次のファイルとフォルダーが含まれます。

    ![zip フォルダー内のファイルのスクリーンショット](media/how-to-use-perfInsights/file-folder.png)

4.  Windows PowerShell を管理者として開き、PerfInsights.ps1 スクリプトを実行します。

    ```
    cd <the path of PerfInsights folder >
    Powershell.exe -ExecutionPolicy UnRestricted -NoProfile -File .\\PerfInsights.ps1
    ```

    実行ポリシーを変更することを確認するために "y" の入力が必要になる場合があります。

    **[Notice and Consent] (注意事項および同意)** ダイアログ ボックスには、Microsoft サポートと診断情報を共有するためのオプションがあります。 続行するには、ライセンス契約に同意する必要もあります。 選択を行ってから、**[スクリプトの実行]** を選択します。

    ![[Notice and Consent] (注意事項および同意) ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/disclaimer.png)

5.  スクリプトを実行するときは、サポート案件番号を送信します (入手可能な場合)。 **[OK]** をクリックします。
    
    ![サポート ID ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/enter-support-number.png)

6.  一時ストレージ ドライブを選択します。 このスクリプトは、ドライブのドライブ文字を自動検出できます。 この段階で問題が発生した場合は、ドライブを選択するように求められることがあります (既定のドライブは D)。 生成されたログは、そのドライブの log\_collection フォルダーに格納されます。 ドライブ文字を入力するか、または受け入れたら、**[OK]** を選択します。

    ![一時ドライブ ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/enter-drive.png)

7.  表示された一覧から、トラブルシューティングのシナリオを選択します。

       ![トラブルシューティング シナリオの一覧のスクリーンショット](media/how-to-use-perfInsights/select-scenarios.png)

UI を使わずに PerfInsights を実行することもできます。 次のコマンドは、UI なしで "低速 VM の分析" のトラブルシューティング シナリオを実行します。 手順 4 で示されたものと同じ免責事項と EULA に同意するように求められます。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30"

PerfInsights をサイレント モードで実行する場合は、**- AcceptDisclaimerAndShareDiagnostics** パラメーターを使用します。 たとえば、次のコマンドを使用します。

        powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -NoGui -Scenario vmslow -TracingDuration 30 -AcceptDisclaimerAndShareDiagnostics"

### <a name="how-do-i-troubleshoot-issues-while-running-the-script"></a>スクリプトの実行中に問題のトラブルシューティングを行う方法

スクリプトが異常終了した場合は、次のように、スクリプトを cleanup スイッチと共に実行することによって整合性のある状態に戻ることができます。

    powershell.exe -ExecutionPolicy UnRestricted -NoProfile -Command ".\\PerfInsights.ps1 -Cleanup"

一時ドライブの自動検出中に問題が発生した場合は、そのドライブを選択するように求められることがあります (既定のドライブは D)。

![一時ドライブ ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/enter-drive.png)

スクリプトによってユーティリティ ツールがアンインストールされ、一時フォルダーが削除されます。

### <a name="troubleshoot-other-script-issues"></a>スクリプトに関するその他の問題をトラブルシューティングする 

スクリプトの実行時に何からの問題が発生した場合は、Ctrl + C キーを押してスクリプトを中断します。 数回試行した後も引き続きスクリプトの失敗が発生する場合は、起動時に "-Debug" パラメーター オプションを使用して、デバッグ モードでスクリプトを実行します。

障害が発生した後、PowerShell コンソールの出力全体をコピーして、問題のトラブルシューティングを支援する Microsoft サポート担当者に送信します。

### <a name="how-do-i-run-the-script-in-custom-slow-vm-analysis-mode"></a>"低速 VM のカスタム分析" モードでスクリプトを実行する方法

**[低速 VM のカスタム分析]** を選択することによって、複数のトレースを並行して有効にできます (複数のトレースを選択するには、Shift キーを使用します)。

![シナリオの一覧のスクリーンショット](media/how-to-use-perfInsights/select-scenario.png)

パフォーマンス カウンター トレース、Xperf トレース、ネットワーク トレース、または Storport トレースのシナリオを選択したら、以降のダイアログ ボックスの手順に従います。 トレースを開始した後、低速なパフォーマンスの問題の再現を試みてください。

トレースは、次のダイアログ ボックスで開始します。

![[Start Performance Counter Trace] (パフォーマンス カウンター トレースの開始) ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/start-trace-message.png)

トレースを停止するには、2 番目のダイアログ ボックスで、コマンドを確認する必要があります。

![[Stopping Performance Counter Trace] (パフォーマンス カウンター トレースの停止)ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/stop-trace-message.png)
![[Stopping All traces] (すべてのトレースの停止)ダイアログ ボックスのスクリーンショット](media/how-to-use-perfInsights/ok-trace-message.png)

トレースまたは操作が完了すると、D:\\log\_collection (または一時ドライブ) に新しいファイルが生成されます。 このファイルの名前は **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** です。 このファイルは、分析のためにサポート担当者に送信できます。

## <a name="review-the-diagnostics-report"></a>診断レポートを確認する

**CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** ファイル内に、PerfInsights の結果が詳細に示された HTML レポートを見つけることができます。 レポートを確認するには、**CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** ファイルを展開し、**PerfInsights Report.html** ファイルを開きます。

**[検索結果]** タブを選択します。

![PerfInsights レポートのスクリーンショット](media/how-to-use-perfInsights/findingtab.png)
![PerfInsights レポートのスクリーンショット](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> 重大と分類された結果は、パフォーマンスの問題の原因となる可能性のある既知の問題です。 重要と分類された結果は、最適ではないが、必ずしもパフォーマンス問題の原因とはならない構成です。 情報と分類された結果は、単なる情報提供のためのステートメントです。

すべての重大な結果および重要な結果に対する推奨事項とリンクを確認します。 それらがパフォーマンスに影響を与える可能性について、またパフォーマンスが最適化された構成のベスト プラクティスについても学習してください。

### <a name="storage-tab"></a>[ストレージ] タブ

**[Findings]\(評価結果\)** セクションには、ストレージに関連する分析結果と推奨事項が表示されます。

**[ディスク マップ]** および **[ボリューム マップ]** セクションは、論理ボリュームと物理ディスクがどのように関連しあっているかについて説明しています。

物理ディスクの観点 ([ディスク マップ]) では、このテーブルはディスク上で実行されているすべての論理ボリュームを示しています。 次の例では、**PhysicalDrive2** は複数のパーティション上に作成された 2 つの論理ボリューム (J および H) を実行しています。

![ディスク タブのスクリーンショット](media/how-to-use-perfInsights/disktab.png)

ボリュームの観点 ([ボリューム マップ]) では、このテーブルは各論理ボリュームにあるすべての物理ディスクを示しています。 RAID/動的ディスクの場合は、1 つの論理ボリュームを複数の物理ディスク上で実行する可能性があることに注意してください。 次の例では、*C:\\mount* は、物理ディスク 2 および 3 上で *SpannedDisk* として構成されたマウント ポイントです。

![ボリューム タブのスクリーンショット](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>[SQL Server] タブ

ターゲット VM が SQL Server インスタンスをホストしている場合は、レポートに **SQL** という名前の追加のタブが表示されます。

![SQL タブのスクリーンショット](media/how-to-use-perfInsights/sqltab.png)

このセクションには、**[検索結果]** タブと、VM 上でホストされている SQL Server インスタンスごとの追加のタブが含まれています。

**[検索結果]** タブには、見つかった SQL に関連するすべてのパフォーマンスの問題の一覧が推奨事項と共に含まれています。

次の例では、**PhysicalDrive0** (C ドライブを実行しています) が表示されます。 これは、**modeldev** と **modellog** の両方のファイルが C ドライブに配置され、それらが別の種類 (それぞれ、データ ファイルとトランザクション ログなど) であるためです。

![ログ情報のスクリーンショット](media/how-to-use-perfInsights/loginfo.png)

SQL Server の特定のインスタンスのタブには、選択されたインスタンスに関する基本的な情報を表示する一般的なセクションが含まれています。 これらのタブにはまた、高度な情報 (設定、構成、ユーザー オプションなど) のための追加のセクションも含まれています。

### <a name="diagnostic-tab"></a>[診断] タブ
**[診断]** タブには、PerfInsights を実行している期間中の、コンピューター上の最大の CPU、ディスク、およびメモリ コンシューマーに関する情報が含まれています。 また、システムに欠けている可能性のある重要な修正プログラム、タスクの一覧、および重要なシステム イベントに関する情報を見つけることもできます。 

## <a name="references-to-the-external-tools-used"></a>使用する外部ツールのリファレンス

### <a name="diskspd"></a>Diskspd

Diskspd は、Microsoft からのストレージ ロード ジェネレーターおよびパフォーマンス テスト ツールです。 詳細については、[Diskspd](https://github.com/Microsoft/diskspd) に関するページをご覧ください。

### <a name="xperf"></a>Xperf

Xperf は、Windows パフォーマンス ツールキットからのトレースをキャプチャするためのコマンドライン ツールです。 詳細については、「[Windows Performance Toolkit – Xperf (Windows パフォーマンス ツールキット - Xperf)](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/)」を参照してください。

## <a name="next-steps"></a>次の手順

さらに詳しく調べるために、診断ログとレポートを Microsoft サポートにアップロードできます。 サポートは、トラブルシューティング プロセスを支援するために、PerfInsights によって生成された出力を送信するよう要求する可能性があります。

次のスクリーンショットは、受信する可能性があるものと同様のメッセージを示しています。

![Microsoft サポートからのサンプル メッセージのスクリーンショット](media/how-to-use-perfInsights/supportemail.png)

メッセージ内の指示に従って、ファイル転送ワークスペースにアクセスします。 セキュリティを強化するために、最初の使用時にパスワードを変更する必要があります。

サインインすると、PerfInsights によって収集された **CollectedData\_yyyy-MM-dd\_hh\_mm\_ss.zip** ファイルをアップロードするためのダイアログ ボックスが表示されます。
