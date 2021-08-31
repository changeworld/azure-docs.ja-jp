---
title: STONITH を使用した SAP HANA on Azure (L インスタンス) の高可用性のセットアップ | Microsoft Docs
description: SUSE で STONITH デバイスを使用して、SAP HANA on Azure (Large Instances) の高可用性を設定する方法を説明します。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2c387658b163cdbaf3d318ed03a53205e8a286ba
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2021
ms.locfileid: "112455250"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith-device"></a>STONITH デバイスを使用した SUSE での高可用性セットアップ

この記事では、SUSE オペレーティング システムで STONITH デバイスを使用して、HANA Large Instances の高可用性をセットアップする詳しい方法を説明します。

**免責事項:** *このガイドは、Microsoft HANA Large Instances 環境を試験的にセットアップした経験に基づいて作成しています。HANA Large Instances の Microsoft サービス管理チームは、このオペレーティング システムの問い合わせには対応していません。オペレーティング システムに関する、このガイドの範囲外のトラブルシューティングや説明については、SUSE にお問い合わせください。Microsoft サービス管理チームは、実地に STONITH デバイスの設定を行っており、STONITH デバイスの問題については十分に対応し、解決をサポートをしています。*

## <a name="pre-requisites"></a>前提条件

SUSE でクラスタリングを利用した高可用性 (HA) をセットアップするには、次の要件を満たす必要があります。

- HANA L インスタンスがプロビジョニングされている
- オペレーティング システム (OS) を登録してある
- HANA L インスタンスが SMT サーバーに接続されていて、修正プログラム/パッケージを取得できる
- オペレーティング システムに最新のパッチをインストールしてある
- ネットワーク タイム プロトコル (NTP タイム サーバー) をセットアップしてある
- HA のセットアップに関する SUSE のドキュメントの最新バージョンを読み、理解している

## <a name="setup-details"></a>セットアップの詳細
このガイドでは、次のセットアップを使用します。
- オペレーティング システム:SLES 12 SP1 for SAP
- HANA L インスタンス: 2xS192 (4 ソケット、2 TB)
- HANA バージョン:HANA 2.0 SP1
- サーバー名: sapprdhdb95 (node1) および sapprdhdb96 (node2)
- STONITH デバイス: iSCSI ベースの STONITH デバイス
- HANA L インスタンス ノードの 1 つに NTP のセットアップ

HANA システム レプリケーション　(HSR) を有効にして HANA Large Instances をセットアップするときは、Microsoft サービス管理チームに STONITH デバイスのセットアップを依頼できます。 これは、必ずプロビジョニング時に行う必要があります。 

既に HANA Large Instances のプロビジョニングを行った方も、STONITH デバイスをセットアップできます。 サービス依頼フォーム (SRF) に次の情報を入力して Microsoft サービス管理チームに渡します。 SRF は、テクニカル アカウント マネージャーに依頼するか、HANA Large Instances のオンボーディングに関する Microsoft の連絡先に連絡すれば手に入ります。

- サーバー名とサーバーの IP アドレス (例: myhanaserver1、10.35.0.1)
- 場所 (例: 米国東部)
- 顧客名 (例: Microsoft)
- SID - HANA システム識別子 (例: H11)

STONITH デバイスの設定が終わったら、Microsoft サービス管理チームから、STONITH ブロック デバイス (SBD) 名と iSCSI ストレージの IP アドレスが提供されます。 この情報を使用して STONITH のセットアップの設定ができます。 

これらの手順に従って、STONITH を使用する HA のセットアップを行います。

1.  SBD デバイスを識別する。
2.  SBD デバイスを初期化する。
3.  クラスターを設定します。
4.  softdog ウォッチドッグをセットアップします。
5.  クラスターにノードを参加させる。
6.  クラスターを検証する。
7.  クラスターのリソースを構成する。
8.  フェールオーバー プロセスをテストする。

## <a name="1---identify-the-sbd-device"></a>1. SBD デバイスを識別する
このセクションでは、Microsoft サービス管理チームによる STONITH デバイスの設定が済んだ後で、セットアップに使用する SBD デバイスを把握する方法を説明します。 **このセクションの内容は既存の顧客にのみ当てはまります**。 新規顧客には Microsoft サービス管理チームから SBD デバイス名を提供しますので、このセクションは飛ばしてください。

1.1 */etc/iscsi/initiatorname.isci* を次のように変更します 

``` 
iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
```

Microsoft サービス管理からこの文字列を受け取ります。 **両** ノードのファイルを変更します。ノード番号はノードごとに異なります。

![ノードの InitiatorName 値を含む initiatorname ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/initiatorname.png)

1.2 */etc/iscsi/iscsid.conf* を次のように変更します。*node.session.timeo.replacement_timeout=5* および *node.startup = automatic* を設定します。 **両** ノードのファイルを変更します。

1.3 検出コマンドを実行します。すると 4 つのセッションが表示されます。 両ノードでこれを実行します。

```
iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
```

![iscsiadm discovery コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

1.4 コマンドを実行して iSCSI デバイスにサインインします。すると 4 つのセッションが表示されます。 **両** ノードでこれを実行します。

```
iscsiadm -m node -l
```
![iscsiadm node コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

1.5 再スキャン スクリプト *rescan-scsi-bus.sh* を実行します。このスクリプトを実行すると、新しく作成されたディスクが表示されます。  両ノードでこれを実行します。 0 よりも大きい LUN 番号が表示されます (例: 1、2 などの数字)。

```
rescan-scsi-bus.sh
```
![スクリプトの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

1.6 デバイス名を取得するには、*fdisk -l* コマンドを実行します。 両ノードでこれを実行します。 サイズが **178 MiB** のデバイスを選択します。

```
  fdisk –l
```

![fdisk コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="2---initialize-the-sbd-device"></a>2. SBD デバイスを初期化する

2.1 SBD デバイスを **両** ノードで初期化します。

```
sbd -d <SBD Device Name> create
```
![sbd create コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2.2 デバイスに書き込まれた内容を確認します。 **両** ノードでこれを行います。

```
sbd -d <SBD Device Name> dump
```

## <a name="3---configuring-the-cluster"></a>3. クラスターを設定する
このセクションでは、SUSE HA クラスターをセットアップする手順について説明します。

### <a name="31-package-installation"></a>3.1 パッケージのインストール
3.1.1   ha_sles および SAPHanaSR-doc パターンがインストールされているかどうかを確認します。 インストールされていない場合はインストールします。 **両** ノードにこれらをインストールします。
```
zypper in -t pattern ha_sles
zypper in SAPHanaSR SAPHanaSR-doc
```
![pattern コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。 ](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
![SAPHanaSR-doc コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)

### <a name="32-setting-up-the-cluster"></a>3.2 クラスターのセットアップ
3.2.1 *ha-cluster-init* コマンド、または yast2 のウィザードでクラスターをセットアップします。 この例では、yast2 ウィザードを使用します。 このステップは **プライマリ ノードでのみ** 実施します。

[yast2] > [High Availabiligy]\(高可用性\) > [Cluster]\(クラスター\) を順に選択します

![YaST コントロール センターのスクリーンショット。[High Availability]\(高可用性\) と [Cluster]\(クラスター\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

![[Install]\(インストール\) と [Cancel]\(キャンセル\) ボタンが存在するダイアログ ボックスのスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)

halk2 パッケージは既にインストールされているので、 **[Cancel]\(キャンセル\)** を選択します。

![キャンセル オプションに関するメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)

**[続行]** をクリックします。

予期される値 = デプロイしているノードの数 (この場合は 2)。

![[Enable Security Auth]\(セキュリティ認証を有効にする\) チェック ボックスが表示された [Cluster Security]\(クラスター セキュリティ\) を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)

**[次へ]** を選択します。

![[Sync Host]\(同期ホスト\) と [Sync File]\(同期ファイル\) 一覧が表示されたクラスターの構成ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)

ノード名を追加し、[Add suggested files]\(示されたファイルを追加する\) を選択します。

**[Turn csync2 ON]\(csync2 をオンにする\)** を選択します。

**[Generate Pre-Shared-Keys]\(事前共有キーの生成\)** を選択します。下のポップアップが表示されます。

![キーが生成されたことを示すメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-key-file.png)

**[OK]** を選択します。

Csync2 で IP アドレスと事前共有キーを使って認証が実行されます。 キー ファイルは csync2 -k /etc/csync2/key_hagroup で生成されます。 作成された key_hagroup ファイルを、クラスターのすべてのメンバーに手動でコピーする必要があります。 **必ずファイルをノード 1 からノード 2 にコピーします**。

![クラスターのすべてのメンバーにキーをコピーするのに必要なオプションが表示されたクラスターの構成ダイアログ ボックスを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)

**[次へ]** を選択します。
![クラスター サービス ウィンドウのスクリーンショット｡](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)

既定では [Booting]\(ブート\) オプションは off になっています。 これを on に変更し、起動時に Pacemaker を開始するようにします。 セットアップの要件に基づいて選ぶことができます。

**[Next]\(次へ\)** をクリックしてクラスターの設定を完了します。

## <a name="4---setting-up-the-softdog-watchdog"></a>4. softdog ウォッチドッグをセットアップする
このセクションでは、ウォッチドッグ (softdog) の構成について説明します。

4.1 次の行を、**両方** のノードの */etc/init.d/boot.local* に追加します。

```
modprobe softdog
```
![softdog 行が追加された boot ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)

4.2 次の手順で */etc/sysconfig/sbd* ファイルを **両** ノードで更新します。

```
SBD_DEVICE="<SBD Device Name>"
```
![SBD_DEVICE 値が追加された sbd ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-device.png)

4.3 次のコマンドを実行して、カーネル モジュールを **両** ノードにロードします。

```
modprobe softdog
```
![modprobe softdog コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4.4 softdog が **両** ノードで動作していることを、次の方法で確認します。

```
lsmod | grep dog
```
![lsmod コマンドの実行結果が表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)

4.5 SBD デバイスを **両** ノードで起動します。

```
/usr/share/sbd/sbd.sh start
```
![start コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)

4.6 SBD デーモンを **両** ノードでテストします。 両ノードでこれを設定した後は、2 つの項目が表示されます。

```
sbd -d <SBD Device Name> list
```
![2 つのエントリが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.7 **一方** のノードにテストメッセージを送信します。

```
sbd  -d <SBD Device Name> message <node2> <message>
```
![2 つのエントリが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list.png)

4.8 **2 つ目** のノード (ノード 2) で、メッセージの状態を確認できます。

```
sbd  -d <SBD Device Name> list
```
![他のメンバーのテスト値を表示するメンバーの 1 つを含むコンソールウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)

4.9 SDB の設定を適用するには、 */etc/sysconfig/sbd* ファイルを次のように編集します。 ファイルを **両** ノードで編集します。

```
SBD_DEVICE=" <SBD Device Name>" 
SBD_WATCHDOG="yes" 
SBD_PACEMAKER="yes" 
SBD_STARTMODE="clean" 
SBD_OPTS=""
```
4.10 Pacemaker のサービスを **プライマリ ノード** (ノード 1) で起動します。

```
systemctl start pacemaker
```
![pacemaker の開始後の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)

Pacemaker のサービスが *うまく機能しない* 場合は「*シナリオ 5: Pacemaker サービスが失敗する*」をご覧ください。

## <a name="5---joining-the-cluster"></a>5. クラスターに参加する
このセクションでは、ノードをクラスターに追加する方法を説明します。

### <a name="51-add-the-node"></a>5.1 ノードを追加する
**node2** で次のコマンドを実行して、node2 をクラスターに参加させます。

```
ha-cluster-join
```
クラスターの追加時に *エラー* が出る場合は「*シナリオ 6: node2 がクラスターに参加できない*」を読んでください。

## <a name="6---validating-the-cluster"></a>6. クラスターの検証

### <a name="61-start-the-cluster-service"></a>6.1 クラスター サービスを開始する
確認し、必要に応じて **両方** のノードでクラスターを初めて開始します。

```
systemctl status pacemaker
systemctl start pacemaker
```
![pacemaker の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)

### <a name="62-monitor-the-status"></a>6.2 状態を監視する

*crm_mon* コマンドを実行して **両** ノードがオンラインであることを確認します。 これは、クラスターの **どのノードでも** 実行できます。

```
crm_mon
```
![crm_mon の結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon.png)

hawk にサインインして、クラスターの状態 *https://\<node IP>:7630* を確認することもできます。 既定のユーザーは hacluster で、パスワードは linux です。 必要であれば、*passwd* コマンドでパスワードを変更できます。

## <a name="7-configure-cluster-properties-and-resources"></a>7. クラスターのプロパティとリソースを設定する

このセクションでは、クラスター リソースを構成する手順について説明します。
この例では、次のリソースをセットアップします。その他のリソースは、(必要であれば) SUSE HA ガイドを参考にして設定できます。 設定は、**1 つのノードでのみ** 行います。 これはプライマリ ノードで行ってください。

- クラスターのブートストラップ
- STONITH デバイス
- 仮想 IP アドレス

### <a name="71-cluster-bootstrap-and-more"></a>7.1 クラスターのブートストラップその他
クラスターのブートストラップを追加します。 ファイルを作成し、次のようにテキストを追加します。

```
sapprdhdb95:~ # vi crm-bs.txt
# enter the following to crm-bs.txt
property $id="cib-bootstrap-options" \
no-quorum-policy="ignore" \
stonith-enabled="true" \
stonith-action="reboot" \
stonith-timeout="150s"
rsc_defaults $id="rsc-options" \
resource-stickiness="1000" \
migration-threshold="5000"
op_defaults $id="op-options" \
timeout="600"
```
構成をクラスターに追加します。

```
crm configure load update crm-bs.txt
```
![crm コマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)

### <a name="72-stonith-device"></a>7.2 STONITH デバイス
STONITH リソースを追加します。 ファイルを作成し、次のとおりテキストを入力します。

```
# vi crm-sbd.txt
# enter the following to crm-sbd.txt
primitive stonith-sbd stonith:external/sbd \
params pcmk_delay_max="15"
```
構成をクラスターに追加します。

```
crm configure load update crm-sbd.txt
```

### <a name="73-the-virtual-ip-address"></a>7.3 仮想 IP アドレス
仮想 IP リソースを追加します。 ファイルを作成し、次のようにテキストを追加します。

```
# vi crm-vip.txt
primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
operations $id="rsc_ip_HA1_HDB10-operations" \
op monitor interval="10s" timeout="20s" \
params ip="10.35.0.197"
```
構成をクラスターに追加します。

```
crm configure load update crm-vip.txt
```

### <a name="74-validate-the-resources"></a>7.4 リソースを確認する

*crm_mon* コマンドを実行すると、2 つのリソースが表示されます。
![2 つのリソースが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

*https://\<node IP address>:7630/cib/live/state* でも状態を確認できます。

![2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)

## <a name="8-testing-the-failover-process"></a>8.フェールオーバー プロセスをテストする
フェールオーバー プロセスをテストするには、node1 の Pacemaker サービスを停止すると、リソースが node2 にフェールオーバーします。

```
Service pacemaker stop
```
Pacemaker のサービスを **ノード 2** で停止します。するとリソースが **ノード 1** にフェールオーバーします。

**フェールオーバー前**  
![フェールオーバー前の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/Before-failover.png)  

**フェールオーバー後**  
![フェールオーバー後の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/after-failover.png)

![フェールオーバー後のリソースの状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  


## <a name="9-troubleshooting"></a>9.トラブルシューティング
このセクションでは、セットアップ時に起こり得るいくつかのエラーについて説明します。 これらの問題は必ず発生するとは限りません。

### <a name="scenario-1-cluster-node-not-online"></a>シナリオ 1:クラスター ノードがオンラインではない
クラスター マネージャーでオンラインになっていないノードがある場合は、これをオンラインにするために次の方法を試すことができます。

この iSCSI サービスを起動します。

```
service iscsid start
```

これで iSCSI ノードにサインインできるようになります。

```
iscsiadm -m node -l
```
次のような出力が表示されます。

```
sapprdhdb45:~ # iscsiadm -m node -l
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>シナリオ 2: yast2 で GUI が表示されない

このドキュメントでは、yast2 の GUI 画面で高可用性クラスターをセットアップします。 yast2 でここに示すような GUI ウィンドウが表示されず Qt のエラーが出る場合は、次の手順を実行します。 グラフィカル ウィンドウが開く場合は、この手順を省略できます。

**Error**

![エラー メッセージが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**予想される出力**

![[High Availability]\(高可用性) と [Cluster]\(クラスター\) が強調表示された YaST コントロール センターを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

yast2 で GUI が表示されない場合は、この手順を実行します。

必要なパッケージをインストールします。 "root" ユーザーとしてログインし、SMT セットアップでパッケージをダウンロードしてインストールする必要があります。

パッケージをインストールするには、[yast] > [Software]\(ソフトウェア\) > [Software Management]\(ソフトウェア管理\) > [Dependencies]\(依存関係\) > [Install recommended packages…]\(推奨されるパッケージをインストールする...\) オプションを使います。 次のスクリーンショットは、予想される画面を示しています。

>[!NOTE]
>この手順を両ノードで実行し、yast2 の GUI を両ノードで利用できるようにする必要があります。

![YaST コントロール センターが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)

[Dependencies]\(依存関係\) で [Install Recommended Packages]\(推奨パッケージのインストール\) を選択します。

![コンソール ウィンドウのスクリーンショット。[Install Recommended Packages]\(推奨パッケージのインストール\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

変更内容を確認し **[OK]** を選択します。

![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

パッケージがインストールされます。

![インストールの進み具合を示す、コンソール ウィンドウのスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

**[次へ]** を選択します。

![成功メッセージが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

**[完了]** を選択します。

libqt4 パッケージと libyui-qt パッケージもインストールする必要があります。

```
zypper -n install libqt4
```
![libqt4 パッケージをインストールするコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)

```
zypper -n install libyui-qt
```
![libyui-qt パッケージをインストールしているコンソール ウィンドウのスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)

![libyui-qt パッケージをインストールしているコンソール ウィンドウのスクリーンショット (続き)。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)

ここに示すとおり、yast2 で GUI を表示できるようになります。

![[Software]\(ソフトウェア\) と [Online Update]\(オンライン更新\) が選択された YaST コントロール センターを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>シナリオ 3: yast2 に高可用性オプションが表示されない

高可用性オプションを yast2 コントロール センターに表示するには、他のパッケージもインストールする必要があります。

[Yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) で次のパターンを選択します。

- SAP HANA サーバー ベース
- C/C++ コンパイラとツール
- 高可用性
- SAP アプリケーション サーバー ベース

次の画面では、パターンをインストールする手順を示します。

[yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) の使用

![インストールを開始するために [Software]\(ソフトウェア\) と [Online Update]\(オンライン更新\) が選択された YaST コントロール センターを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

パターンを選択します。

![[C / C++ Compiler and Tools]\(C / C ++ コンパイラとツール\) の項目で 1 つ目のパターンを選択する操作のスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

![[C / C++ Compiler and Tools]\(C / C ++ コンパイラとツール\) の項目で 2 つ目のパターンを選択する操作のスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

**[承諾]** を選択します。

![依存関係を解決するように変更されたパッケージが表示された [Changed Packages]\(変更されたパッケージ\) ダイアログ ボックスを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

**[続行]** を選択します。

![[Performing Installation]\(インストールの実行中\) 状態ページが示されたスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

インストールが完了したら **[Next]\(次へ\)** をクリックします。

![インストールのレポートが表示されたスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>シナリオ 4: gcc アセンブリ エラーが出て HANA のインストールが失敗する
次のエラーが出て、HANA のインストールが失敗します。

![オペレーティング システムが gcc 5 のアセンブリを実行できる状態でないことを表すエラーのスクリーンショット。](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

この問題を修正するには、ライブラリ (libgcc_sl と libstdc++6) を次のようにしてインストールする必要があります。

![必要なライブラリをインストールするコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>シナリオ 5: Pacemaker サービスが失敗する

Pacemaker サービスの開始中に、次の問題が発生します。

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

これを修正するには、 */usr/lib/systemd/system/fstrim.timer* ファイルから次の行を削除します

```
Persistent=true
```

![削除される値 Persistent=true が含まれている fstrim ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>シナリオ 6:node2 がクラスターに参加できない

*ha-cluster-join* コマンドでノード 2 を既存のクラスターに追加すると、次のエラーが起こります。

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![IP アドレスから SSH キーを取得できないというエラー メッセージが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

修正するには、次のものを両ノードで実行します。

```
ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
```

![最初のノードでコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

![2 番目のノードでコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

上の修正を行ってから、ノード 2 をクラスターに追加します。

![ha-cluster-join コマンドが成功したコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>次のステップ

SUSE HA のセットアップについて詳しくは、次の記事をご覧ください。 

- [SAP HANA SR パフォーマンス最適化シナリオ](https://www.suse.com/support/kb/doc/?id=000019450 )
- [ストレージ ベースのフェンス操作](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [SAP HANA 用 Pacemaker Cluster の使用 - パート 1 のブログ](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [SAP HANA 用 Pacemaker Cluster の使用 - パート 2 のブログ](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)

オペレーティング システムをファイル レベルでバックアップ、復元する方法を学ぶ。

> [!div class="nextstepaction"]
> [OS のバックアップと復元](large-instance-os-backup.md)
