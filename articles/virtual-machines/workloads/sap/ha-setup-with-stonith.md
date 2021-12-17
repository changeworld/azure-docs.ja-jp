---
title: STONITH を使用した SAP HANA on Azure (L インスタンス) の高可用性のセットアップ | Microsoft Docs
description: SUSE で STONITH デバイスを使用して、SAP HANA on Azure (L インスタンス) の高可用性を設定する方法を説明します。
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
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710839"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>STONITH デバイスを使用した SUSE での高可用性セットアップ

この記事では、SUSE オペレーティング システムで STONITH デバイスを使用して、HANA L インスタンスの高可用性 (HA) を設定する手順を説明します。

> [!NOTE]
> このガイドは、Microsoft HANA L インスタンス環境の設定を適切にテストすることで得られたものです。 HANA L インスタンスの Microsoft サービス管理チームは、このオペレーティング システムをサポートしていません。 オペレーティング システム レイヤーに関するトラブルシューティングと説明については、SUSE にお問い合わせください。 
>
> Microsoft サービス管理チームは、STONITH デバイスの設定とフル サポートを担当しています。 STONITH デバイスに関する問題のトラブルシューティングについても支援します。

## <a name="prerequisites"></a>必須コンポーネント

SUSE のクラスタリングを使用して高可用性を設定するには、次のことを行う必要があります。

- HANA L インスタンスをプロビジョニングする。
- 最新のパッチを適用したオペレーティング システムをインストールして登録する。
- HANA L インスタンス サーバーを SMT サーバーに接続し、パッチとパッケージを取得する。
- ネットワーク タイム プロトコル (NTP タイム サーバー) を設定する。
- 最新の SUSE のドキュメントで HA のセットアップに関する情報を読み、理解する。

## <a name="setup-details"></a>セットアップの詳細

このガイドでは、次のセットアップを使用します。

- オペレーティング システム: SLES 12 SP1 for SAP
- HANA L インスタンス:2xS192 (4 ソケット、2 TB)
- HANA のバージョン: HANA 2.0 SP1
- サーバー名: sapprdhdb95 (node1) および sapprdhdb96 (node2)
- STONITH デバイス: iSCSI ベース
- HANA L インスタンス ノードのいずれかでの NTP

HANA システム レプリケーションを有効にして HANA L インスタンスを設定するときは、Microsoft サービス管理チームに STONITH デバイスの設定を依頼できます。 これは、プロビジョニング時に行います。 

既に HANA Large Instances のプロビジョニングを行った方も、STONITH デバイスをセットアップできます。 サービス依頼フォーム (SRF) に次の情報を入力して Microsoft サービス管理チームに渡します。 SRF は、テクニカル アカウント マネージャーか、HANA Large Instances のオンボーディングを担当する Microsoft の連絡先に連絡して取得できます。

- サーバー名とサーバーの IP アドレス (例: myhanaserver1、10.35.0.1)
- 場所 (例: 米国東部)
- 顧客名 (例: Microsoft)
- HANA システム識別子 (SID) (例: H11)

STONITH デバイスの設定が終わったら、Microsoft サービス管理チームから、STONITH ブロック デバイス (SBD) 名と iSCSI ストレージの IP アドレスが提供されます。 この情報を使用して STONITH のセットアップの設定ができます。 

次のセクションの手順に従い、STONITH を使用して HA を設定します。

## <a name="identify-the-sbd-device"></a>SBD デバイスを識別する

> [!NOTE]
> このセクションの内容は既存のお客様にのみ当てはまります。 新規顧客には Microsoft サービス管理チームから SBD デバイス名を提供しますので、このセクションは飛ばしてください。

1. */etc/iscsi/initiatorname.isci* を次のように変更します。 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft サービス管理からこの文字列を受け取ります。 *両* ノードのファイルを変更します。 ただし、ノード番号はノードによって異なります。
    
    ![ノードの InitiatorName 値を含む initiatorname ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. `node.session.timeo.replacement_timeout=5` と `node.startup = automatic` を設定して、 */etc/iscsi/iscsid.conf* を変更します。 *両* ノードのファイルを変更します。

3. "*両方*" のノード上で次の discovery コマンドを実行します。

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    結果には 4 つのセッションが表示されます。
    
    ![discovery コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. "*両方*" のノード上で次のコマンドを実行し、iSCSI デバイスにサインインします。 

    ```
    iscsiadm -m node -l
    ```
    
    結果には 4 つのセッションが表示されます。
    
    ![node コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. 次のコマンドを使用して、*rescan-scsi-bus.sh* rescan スクリプトを実行します。 自動的に作成された新しいディスクが表示されます。  *両* ノードでこれを実行します。

    ```
    rescan-scsi-bus.sh
    ```
    
    結果には、0 より大きい LUN 番号が表示されます (例: 1、2 など)。
     
    ![スクリプトの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. デバイス名を取得するには、"*両方*" のノード上で次のコマンドを実行します。 

    ```
      fdisk –l
    ```
    
    結果で、178 MiB のサイズのデバイスを選択します。
    
    ![fdisk コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>SBD デバイスを初期化する

1. "*両方*" のノード上で次のコマンドを使用して、SBD デバイスを初期化します。

    ```
    sbd -d <SBD Device Name> create
    ```
    ![sbd create コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. "*両方*" のノード上で次のコマンドを使用して、デバイスに書き込まれた内容を確認します。

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>SUSE HA クラスターを構成する

1. "*両方*" のノード上で次のコマンドを使用して、ha_sles と SAPHanaSR-doc パターンがインストールされているかどうかを確認します。 インストールされていない場合はインストールします。

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![pattern コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![SAPHanaSR-doc コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. `ha-cluster-init` コマンドまたは yast2 ウィザードのいずれかを使用してクラスターを設定します。 この例では、yast2 ウィザードを使用しています。 この手順は "*プライマリ ノード*" でのみ実施します。

    1. **[yast2]**  >  **[High Availability]\(高可用性\)**  >  **[Cluster]\(クラスター\)** に移動します
    
        ![YaST Control Center のスクリーンショット。[High Availability]\(高可用性\) と [Cluster]\(クラスター\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. halk2 パッケージは既にインストールされているため、表示される hawk パッケージのインストールに関するダイアログで **[キャンセル]** を選択します。
    
        ![[Install]\(インストール\) と [Cancel]\(キャンセル\) ボタンが存在するダイアログを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. 表示される続行に関するダイアログで、 **[Continue]\(続行\)** を選択します。
    
        ![必要なパッケージをインストールせずに続行する旨のメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. 予期される値は、デプロイされたノード数 (この場合は 2) です。 **[次へ]** を選択します。  

     
        
    1. ノード名を追加し、 **[Add suggested files]\(示されたファイルを追加する\)** を選択します。

        ![[Sync Host]\(同期ホスト\) と [Sync File]\(同期ファイル\) 一覧が表示されたクラスターの構成ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. **[Turn csync2 ON]\(csync2 をオンにする\)** を選択します。
    
    1. **[Generate Pre-Shared-Keys]\(事前共有キーを生成する\)** を選択します。 
    
    1. 表示されるポップアップ メッセージで、 **[OK]** を選択します。
    
        ![キーが生成されたというメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. Csync2 で IP アドレスと事前共有キーを使って認証が実行されます。 キー ファイルは、`csync2 -k /etc/csync2/key_hagroup` を使用して生成されます。 
    
        ファイル *key_hagroup* が作成されたら、クラスターの全メンバーに手動でコピーします。 必ずファイルを node1 から node2 にコピーします。 **[次へ]** を選択します。
        
        ![クラスターのすべてのメンバーにキーをコピーするのに必要なオプションが表示されたクラスターの構成ダイアログ ボックスを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. 既定のオプションでは、 **[Booting]\(ブート\)** オプションは **[Off]\(オフ\)** です。 これを **[On]\(オン\)** に変更すると、起動時に pacemaker サービスが開始されます。 セットアップの要件に基づいて選ぶことができます。

        ![[Booting]\(ブート\) が有効な Cluster Service ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. **[Next]\(次へ\)** をクリックしてクラスターの設定を完了します。

## <a name="set-up-the-softdog-watchdog"></a>softdog ウォッチドッグをセットアップします

1. "*両方*" のノードの */etc/init.d/boot.local* に次の行を追加します。
    
    ```
    modprobe softdog
    ```
    ![softdog 行が追加された boot ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. "*両方*" のノード上で次のコマンドを使用して、ファイル */etc/sysconfig/sbd* を更新します。
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![SBD_DEVICE 値が追加された sbd ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. "*両方*" のノード上で次のコマンドを実行して、カーネル モジュールを読み込みます。
    
    ```
    modprobe softdog
    ```
    ![modprobe softdog コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. "*両方*" のノード上で次のコマンドを使用して、softdog が実行されていることを確認します。
    
    ```
    lsmod | grep dog
    ```
    ![lsmod コマンドの実行結果が表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. "*両方*" のノード上で次のコマンドを使用して、SBD デバイスを起動します。

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![start コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. "*両方*" のノード上で次のコマンドを使用して、SBD デーモンをテストします。 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    両方のノードの構成後の結果には、2 つのエントリが表示されます。    
    
    ![2 つのエントリが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. 次のテスト メッセージをノードの "*いずれか*" に送信します。

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. "*2 つ目*" のノード (node2) 上で次のコマンドを使用して、メッセージの状態を確認します。
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![他のメンバーのテスト値を表示するメンバーの 1 つを含むコンソールウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. SBD の構成を採用するには、"*両方*" のノード上のファイル */etc/sysconfig/SBD* を次のように更新します。

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. "*プライマリ ノード*" (node1) 上で次のコマンドを使用して、pacemaker サービスを開始します。

    ```
    systemctl start pacemaker
    ```
    ![pacemaker の開始後の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    pacemaker サービスが失敗する場合は、後述の「[シナリオ 5: Pacemaker サービスが失敗する](#scenario-5-pacemaker-service-fails)」セクションを参照してください。

## <a name="join-the-node-to-the-cluster"></a>クラスターにノードを参加させる

*node2* 上で次のコマンドを実行して、ノードをクラスターに参加させます。

```
ha-cluster-join
```

クラスターの参加中にエラーが発生した場合は、この記事で後述する「[シナリオ 6: Node2 がクラスターに参加できない](#scenario-6-node2-cant-join-the-cluster)」セクションを参照してください。

## <a name="validate-the-cluster"></a>クラスターを検証する

1. 次のコマンドを使用して確認し、必要に応じて "*両方*" のノード上でクラスターを初めて開始します。
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![pacemaker の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. 次のコマンドを実行して、"*両方*" のノードがオンラインであることを確認します。 これは、クラスターの *どのノードでも* 実行できます。
    
    ```
    crm_mon
    ```
    ![crm_mon コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    hawk にサインインして、クラスターの状態 `https://\<node IP>:7630` を確認することもできます。 既定のユーザーは **hacluster** であり、パスワードは **linux** です。 必要であれば、`passwd` コマンドでパスワードを変更できます。
    
## <a name="configure-cluster-properties-and-resources"></a>クラスターのプロパティとリソースを構成する

このセクションでは、クラスター リソースを構成する手順について説明します。
この例では、次のリソースを設定しています。 残りの部分は (必要に応じて) SUSE HA のガイドを参照して構成できます。

- クラスターのブートストラップ
- STONITH デバイス
- Virtual IP address

この構成は "*プライマリ ノード*" でのみ行います。

1. クラスターのブートストラップ ファイルを作成し、次のテキストを追加して構成します。
    
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

2. 次のコマンドを使用して、構成をクラスターに追加します。

    ```
    crm configure load update crm-bs.txt
    ```
    ![crm コマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. 次のようにリソースを追加し、ファイルを作成し、テキストを追加して、STONITH デバイスを構成します。

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    次のコマンドを使用して、構成をクラスターに追加します。
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. ファイルを作成して次のテキストを追加することで、リソースの仮想 IP アドレスを追加します。

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    次のコマンドを使用して、構成をクラスターに追加します。
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. `crm_mon` コマンドを使用してリソースを検証します。 

    結果には、2 つのリソースが表示されます。

    ![2 つのリソースが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    *https://\<node IP address>:7630/cib/live/state* でも状態を確認できます。
    
    ![2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>フェールオーバー プロセスをテストする

1. フェールオーバー プロセスをテストするには、次のコマンドを使用して node1 上の pacemaker サービスを停止します。

    ```
    Service pacemaker stop
    ```
    
    リソースは node2 にフェールオーバーされます。

2. pacemaker のサービスを node2 で停止します。するとリソースが node1 にフェールオーバーします。

    フェールオーバー前の状態は次のとおりです。  
    ![フェールオーバー前の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    フェールオーバー後の状態は次のとおりです。  
    ![フェールオーバー後の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![フェールオーバー後のリソースの状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>トラブルシューティング

このセクションでは、設定時に発生する可能性のあるエラー シナリオについて説明します。

### <a name="scenario-1-cluster-node-not-online"></a>シナリオ 1:クラスター ノードがオンラインではない

クラスター マネージャーでオンラインと表示されないノードがある場合は、これをオンラインにするために次の手順を試すことができます。

1. 次のコマンドを使用して iSCSI サービスを開始します。

    ```
    service iscsid start
    ```
    
2. 次のコマンドを使用してその iSCSI ノードにサインインします。

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

この記事では、yast2 の GUI 画面で高可用性クラスターを設定します。 yast2 が図のような GUI ウィンドウで開かず、Qt エラーがスローされる場合は、次の手順で必要なパッケージをインストールします。 グラフィカル ウィンドウが開く場合は、この手順を省略できます。

Qt エラーの例を次に示します。

![エラー メッセージが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

想定される出力の例を次に示します。

![[High Availability]\(高可用性) と [Cluster]\(クラスター\) が強調表示された YaST Control Center を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. 必ず "root" ユーザーとしてログインし、SMT セットアップでパッケージをダウンロードしてインストールします。

2. **[yast]**  >  **[Software]\(ソフトウェア\)**  >  **[Software Management]\(ソフトウェア管理\)**  >  **[Dependencies]\(依存関係\)** に移動し、 **[Install recommended packages]\(推奨パッケージのインストール\)** を選択します。 

    >[!NOTE]
    >両方のノードから yast2 の GUI を利用できるように、"*両方*" のノード上でこの手順を実行します。
    
    次のスクリーンショットは、想定される画面を示しています。
    
    ![YaST Control Center が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. **[Dependencies]\(依存関係\)** で、 **[Install Recommended Packages]\(推奨パッケージのインストール\)** を選択します。

    ![コンソール ウィンドウのスクリーンショット。[Install Recommended Packages]\(推奨パッケージのインストール\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. 変更内容を確認し **[OK]** を選択します。

    ![インストール対象として選択されたパッケージの一覧が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    パッケージのインストールが続行されます。

    ![インストールの進み具合を示す、コンソール ウィンドウのスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. **[次へ]** を選択します。    

6. **[Installation Successfully Finished]\(インストールが正常に完了しました\)** 画面が表示されたら、 **[Finish]\(完了\)** を選択します。

    ![成功メッセージが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. 次のコマンドを使用して、libqt4 および libyui-qt パッケージをインストールします。
    
    ```
    zypper -n install libqt4
    ```
    ![最初のパッケージをインストールするコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![2 つ目のパッケージをインストールするコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![2 つ目のパッケージをインストールするコンソール ウィンドウを示すスクリーンショット (続き)。](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    yast2 で GUI を表示できるようになります。

    ![[Software]\(ソフトウェア\) と [Online Update]\(オンライン更新\) が選択された YaST Control Center を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>シナリオ 3: yast2 に高可用性オプションが表示されない

高可用性オプションを yast2 Control Center に表示するには、他のパッケージもインストールする必要があります。

1. **[Yast2]**  >  **[Software]\(ソフトウェア\)**  >  **[Software Management]\(ソフトウェア管理\)** に移動します。 次に、 **[Software]\(ソフトウェア\)**  >  **[Online Update]\(オンライン更新\)** を選択します。  

    ![[Software]\(ソフトウェア\) と [Online Update]\(オンライン更新\) が選択された YaST Control Center を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. 次の項目のパターンを選択します。 次に **[Accept]\(承認\)** を選択します。

    - SAP HANA サーバー ベース
    - C/C++ コンパイラとツール
    - 高可用性
    - SAP アプリケーション サーバー ベース

    ![コンパイラとツールの項目で 1 つ目のパターンを選択する操作を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![コンパイラとツールの項目で 2 つ目のパターンを選択する操作を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. 依存関係を解決するように変更されたパッケージの一覧で、 **[Continue]\(続行\)** を選択します。

    ![依存関係を解決するように変更されたパッケージが表示された [Changed Packages]\(変更されたパッケージ\) ダイアログを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. **[Performing Installation]\(インストールの実行中\)** 状態ページで、 **[Next]\(次へ\)** を選択します。

    ![[Performing Installation]\(インストールの実行中\) 状態ページが示されたスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. インストールが完了すると、インストール レポートが表示されます。 **[完了]** を選択します。

    ![インストール レポートを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>シナリオ 4: gcc アセンブリ エラーが出て HANA のインストールが失敗する

HANA のインストールに失敗すると、次のようなエラーが発生することがあります。

![オペレーティング システムが gcc 5 のアセンブリを実行できる状態でないことを表すエラーのスクリーンショット。](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

この問題を解決するには、次のスクリーンショットに示すように、libgcc_sl と libstdc++6 のライブラリをインストールします。

![必要なライブラリをインストールするコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>シナリオ 5: Pacemaker サービスが失敗する

pacemaker サービスを開始できない場合は、次の情報が表示されます。

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

これを修正するには、 */usr/lib/systemd/system/fstrim.timer* ファイルから次の行を削除します。

```
Persistent=true
```
    
![削除される値 Persistent=true が含まれている fstrim ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>シナリオ 6: Node2 がクラスターに参加できない

*ha-cluster-join* コマンドを使用して node2 を既存のクラスターに参加させる操作に問題がある場合は、次のようなエラーが表示されます。

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![SSH キーが特定の IP アドレスから取得できないというエラー メッセージが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

これを修正するには:

1. "*両方のノード*" 上で次のコマンドを実行します。

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![1 つ目のノード上でコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![2 つ目のノード上でコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. node2 がクラスターに追加されたことを確認します。

    ![join コマンドが成功したコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>次のステップ

SUSE HA のセットアップについて詳しくは、次の記事をご覧ください。 

- [SAP HANA SR Performance Optimized Scenario (SAP HANA SR パフォーマンス最適化シナリオ)](https://www.suse.com/support/kb/doc/?id=000019450) (SUSE Web サイト)
- [Fencing and STONITH (フェンスと STONITH)](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html) (SUSE Web サイト)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA - Part 1: Basics (Pacemaker Cluster for SAP HANA を使用するための準備 - パート 1: 基本)](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) (SAP ブログ)
- [Be Prepared for Using Pacemaker Cluster for SAP HANA – Part 2: Failure of Both Nodes (Pacemaker Cluster for SAP HANA を使用するための準備 - パート 2: 両方のノードの失敗)](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) (SAP ブログ)

オペレーティング システムのファイルレベルのバックアップと復元を行う方法を確認します。

> [!div class="nextstepaction"]
> [OS のバックアップと復元](large-instance-os-backup.md)
