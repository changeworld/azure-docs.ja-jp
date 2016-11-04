---
title: Log Analytics の VMware の監視ソリューション | Microsoft Docs
description: VMware の監視ソリューションを利用して ESXi ホストのログ管理と監視を行う方法について説明します。
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2016
ms.author: banders

---
# <a name="vmware-monitoring-(preview)-solution-in-log-analytics"></a>Log Analytics の VMware の監視 (プレビュー) ソリューション
Log Analytics の VMware の監視ソリューションは、大規模な VMware ログに対する一元化されたログ記録と監視のアプローチを作成するのに役立つソリューションです。 この記事では、このソリューションを使用して 1 つの場所で ESXi ホストのトラブルシューティング、キャプチャ、管理を行う方法を説明します。 このソリューションでは、すべての ESXi ホストの詳細なデータを 1 つの場所に表示できます。 ESXi ホストのログを通じて、VM および ESXi ホストの上位のイベント数、状態、傾向を知ることができます。 一元化された ESXi ホストのログを表示および検索して、トラブルシューティングを実行できます。 また、ログ検索クエリに基づくアラートを作成することもできます。

## <a name="installing-and-configuring-the-solution"></a>ソリューションのインストールと構成
次の情報を使用して、ソリューションをインストールおよび構成します。

* 「[ソリューション ギャラリーから Log Analytics ソリューションを追加する](log-analytics-add-solutions.md)」で説明されている手順に従って、VMware の監視ソリューションを OMS ワークスペースに追加します。

#### <a name="supported-vmware-esxi-hosts"></a>サポートされる VMware ESXi ホスト
vSphere ESXi 5.5 および 6.0 ホスト

#### <a name="prepare-a-linux-server"></a>Linux サーバーの準備
ESXi ホストからのすべての syslog データを受信する Linux オペレーティング システムの VM を作成します。 [OMS Linux エージェント](log-analytics-linux-agents.md)は、すべての ESXi ホスト syslog データの収集ポイントです。 次の例に示すように、複数の ESXi ホストを使用して 1 台の Linux サーバーにログを転送できます。  

   ![syslog のフロー](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>syslog の収集の構成
1. VSphere の syslog 転送を設定します。 syslog 転送の設定方法の詳細については、「[Configuring syslog on ESXi 5.x and 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)」(ESXi 5.x および 6.0 での syslog の構成 (2003322)) をご覧ください。 **ESXi ホストの [構成]** > **[ソフトウェア]** > **[詳細設定]** > **[Syslog]** に移動します。
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. *Syslog.global.logHost* フィールドに、Linux サーバーとポート番号 *1514* を追加します。 たとえば、`tcp://hostname:1514` や `tcp://123.456.789.101:1514` のようにします。
3. ESXi ホストの syslog 用ファイアウォールを開きます。 **ESXi ホストの [構成]** > **[ソフトウェア]** > **[セキュリティ プロファイル]** > **[ファイアウォール]** をクリックし、**[プロパティ]** を開きます。  
   
    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  
   
    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. vSphere コンソールを調べて、syslog が正しく設定されていることを確認します。 ESXI ホストで、ポート **1514** が構成されていることを確認します。
5. ESXi ホストで `nc` コマンドを使用して、Linux サーバーと ESXi ホスト間の接続をテストします。 次に例を示します。
   
    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```
6. OMS Agent for Linux をダウンロードし、Linux サーバーにインストールします。 詳細については、[OMS Agent for Linux のドキュメント](https://github.com/Microsoft/OMS-Agent-for-Linux)をご覧ください。
7. OMS Agent for Linux をインストールしたら、/etc/opt/microsoft/omsagent/sysconf/omsagent.d ディレクトリに移動して vmware_esxi.conf ファイルを /etc/opt/microsoft/omsagent/conf/omsagent.d ディレクトリにコピーし、ファイルの所有者/グループとアクセス許可を変更します。 次に例を示します。
   
    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
8. `sudo /opt/microsoft/omsagent/bin/service_control restart` を実行して OMS Agent for Linux を再起動します。
9. OMS ポータルで、`Type=VMware_CL` のログ検索を実行します。 OMS が syslog データを収集する際には、syslog 形式が維持されます。 ポータルでは、*Hostname* や *ProcessName*など、いくつかの特定のフィールドがキャプチャされます。  
   
    ![type](./media/log-analytics-vmware/type.png)  
   
    上の図のようなログ検索結果が表示される場合は、OMS の VMware の監視ソリューション ダッシュボードを使用するように設定されています。  

## <a name="vmware-data-collection-details"></a>VMware のデータ収集の詳細
VMware の監視ソリューションは、有効にしている OMS Agents for Linux を使用して、ESXi ホストからさまざまなパフォーマンス メトリックとログ データを収集します。

次の表は、データの収集手段と、データの収集方法に関する各種情報をまとめたものです。

| プラットフォーム | OMS Agent for Linux | SCOM エージェント | Azure Storage (Azure Storage) | SCOM の要否 | 管理グループによって送信される SCOM エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |![[はい]](./media/log-analytics-vmware/oms-bullet-green.png) |![なし](./media/log-analytics-vmware/oms-bullet-red.png) |![いいえ](./media/log-analytics-vmware/oms-bullet-red.png) |![いいえ](./media/log-analytics-containers/oms-bullet-red.png) |![いいえ](./media/log-analytics-vmware/oms-bullet-red.png) |3 分おき |

次の表は、VMware の監視ソリューションによって収集されるデータ フィールドの例を示しています。

| フィールド名 | 説明 |
| --- | --- |
| Device_s |VMware ストレージ デバイス |
| ESXIFailure_s |エラーのタイプ |
| EventTime_t |イベントが発生した時刻 |
| HostName_s |ESXi ホスト名 |
| Operation_s |VM の作成または VM の削除 |
| ProcessName_s |イベント名 |
| ResourceId_s |VMware ホストの名前 |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware の SCSI ステータス |
| SyslogMessage_s |syslog データ |
| UserName_s |VM を作成または削除したユーザー |
| VMName_s |VM 名 |
| コンピューター |ホスト コンピューター |
| TimeGenerated |データが生成された時刻 |
| DataCenter_s |VMware データセンター |
| StorageLatency_s |ストレージの待機時間 (ミリ秒) |

## <a name="vmware-monitoring-solution-overview"></a>VMware の監視ソリューションの概要
OMS ポータルに VMware タイルが表示されます。 このタイルでは、エラーの概要が示されます。 タイルをクリックすると、ダッシュボード ビューが表示されます。

![タイル](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>ダッシュボード ビューのナビゲーション
**[VMware]** ダッシュボード ビューでは、ブレードが次のように分類されています。

* Failure Status Count (失敗状態の数)
* Top Host by Event Counts (イベント数が上位のホスト)
* Top Event Counts (イベント数の上位)
* Virtual Machine Activities (仮想マシンのアクティビティ)
* ESXi Host Disk Events (ESXi ホスト ディスクのイベント)

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

いずれかのブレードをクリックすると、そのブレードに固有の詳細情報を表示する Log Analytics 検索ウィンドウが開きます。

ここで検索クエリを編集して、特定の情報が見つかるように変更することができます。 OMS 検索の基本のチュートリアルについては、[OMS のログ検索のチュートリアル](log-analytics-log-searches.md)をご覧ください。

#### <a name="find-esxi-host-events"></a>ESXi ホストのイベントの検索
1 台の ESXi ホストで、そのプロセスに基づいて複数のログが生成されます。 VMware の監視ソリューションは、それらを一元化してイベント数を集計します。 この一元化されたビューを利用して、環境内のイベント数の多い ESXi ホストや、最も頻繁に発生しているイベントを把握できます。

![event](./media/log-analytics-vmware/events.png)

いずれかの ESXi ホストまたはイベント タイプをクリックすると、さらに詳しい情報を表示できます。

ESXi ホスト名をクリックすると、その ESXi ホストからの情報が表示されます。 イベント タイプで結果を絞り込む場合は、検索クエリに `“ProcessName_s=EVENT TYPE”` を追加します。 検索フィルターで **ProcessName** を選択できます。 これにより、情報が絞り込まれます。

![詳細表示](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>高い VM アクティビティの検索
どの ESXi ホストでも、仮想マシンを作成および削除することができます。 ESXi ホストで作成される VM の数を把握することは、管理者にとって役立ちます。 これはまた、パフォーマンスと容量計画を理解するのにも役立ちます。 VM アクティビティ イベントの追跡は、環境を管理する際に非常に重要です。

![詳細表示](./media/log-analytics-vmware/vmactivities1.png)

ESXi ホストの VM 作成データの詳細を表示したい場合は、ESXi ホスト名をクリックします。

![詳細表示](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>一般的な検索クエリ
このソリューションには、ESXi ホストの管理に役立つその他の便利なクエリ (高いストレージ使用量、ストレージの待機時間、パスの障害など) も含まれています。

![クエリ](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>クエリの保存
検索クエリの保存は OMS の標準機能であり、便利なクエリを保存しておくのに役立ちます。 作成したクエリが便利であることがわかったら、**[お気に入り]** をクリックして保存します。 保存したクエリは、後で [[マイ ダッシュボード]](log-analytics-dashboards.md) ページから簡単に再利用できます。このページでは、独自のカスタム ダッシュボードを作成できます。

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>クエリからのアラートの作成
クエリを作成したら、そのクエリを使用して、特定のイベントが発生したときにアラートを受け取ることもできます。 アラートの作成方法については、「[Log Analytics のアラート](log-analytics-alerts.md)」をご覧ください。 アラート クエリおよびその他のクエリの例については、ブログ記事「[Monitor VMware using OMS Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics)」(OMS Log Analytics を使用した VMware の監視) をご覧ください。

## <a name="next-steps"></a>次のステップ
* Log Analytics の[ログ検索](log-analytics-log-searches.md)を使用して、VMware ホストの詳細データを表示します。
* VMware ホストのデータを表示する[独自のダッシュ ボードを作成](log-analytics-dashboards.md)します。
* VMware ホストの特定のイベントが発生した場合の[アラートを作成](log-analytics-alerts.md)します。

<!--HONumber=Oct16_HO2-->


