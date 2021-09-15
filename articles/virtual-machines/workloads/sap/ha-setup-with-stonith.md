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
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0ae2b1ebd0b48b38a0f5b7a27097c89f94205d88
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423684"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith-device"></a>STONITH デバイスを使用した SUSE での高可用性セットアップ

この記事では、SUSE オペレーティング システムで STONITH デバイスを使用して、HANA Large Instances の高可用性をセットアップする手順を説明します。

**免責事項:** *このガイドは、Microsoft HANA Large Instances 環境を試験的にセットアップした経験に基づいて作成しています。HANA Large Instances の Microsoft サービス管理チームは、このオペレーティング システムの問い合わせには対応していません。オペレーティング システムに関する、このガイドの範囲外のトラブルシューティングや説明については、SUSE にお問い合わせください。Microsoft サービス管理チームは、実地に STONITH デバイスの設定を行っており、STONITH デバイスの問題については十分に対応し、解決をサポートをしています。*

## <a name="prerequisites"></a>前提条件

SUSE クラスタリングを使用して高可用性 (HA) を設定するには、次の前提条件を満たす必要があります。

- HANA Large Instances がプロビジョニングされている
- オペレーティング システム (OS) が登録されている
- HANA Large Instances サーバーが SMT サーバーに接続されていて、パッチやパッケージを取得できる
- オペレーティング システムに最新のパッチがインストールされている
- Network Time Protocol (NTP タイム サーバー) がセットアップされている
- HA のセットアップに関する SUSE のドキュメントの最新バージョンを読み、理解している

## <a name="setup-details"></a>セットアップの詳細
このガイドでは、次のセットアップを使用します。
- オペレーティング システム:SLES 12 SP1 for SAP
- HANA L インスタンス: 2xS192 (4 ソケット、2 TB)
- HANA バージョン:HANA 2.0 SP1
- サーバー名: sapprdhdb95 (node1) および sapprdhdb96 (node2)
- STONITH デバイス: iSCSI ベースの STONITH デバイス
- HANA Large Instances ノードの 1 つでの NTP のセットアップ

HANA システム レプリケーション　(HSR) を有効にして HANA Large Instances をセットアップするときは、Microsoft サービス管理チームに STONITH デバイスの設定を依頼できます。 これは、必ずプロビジョニング時に行う必要があります。 

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

## <a name="identify-the-sbd-device"></a>SBD デバイスを識別する

> [!NOTE]
> このセクションの内容は既存のお客様にのみ当てはまります。 新規顧客には Microsoft サービス管理チームから SBD デバイス名を提供しますので、このセクションは飛ばしてください。

既存のお客様のみ: Microsoft サービス管理チームが STONITH デバイスを構成した後、お客様はセットアップ用の SBD デバイスを決定する必要があります。

1. */etc/iscsi/initiatorname.isci* を次のように変更します。 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft サービス管理からこの文字列を受け取ります。 **両** ノードのファイルを変更します。ノード番号はノードごとに異なります。
    
    ![ノードの InitiatorName 値を含む initiatorname ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. */etc/iscsi/iscsid.conf* を変更します。*node.session.timeo.replacement_timeout=5* および *node.startup = automatic* を設定します。 **両** ノードのファイルを変更します。

3. 検出コマンドを実行します。4 つのセッションが表示されます。 両ノードでこれを実行します。

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    ![iscsiadm discovery コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. コマンドを実行して iSCSI デバイスにサインインします。4 つのセッションが表示されます。 **両** ノードでこれを実行します。

    ```
    iscsiadm -m node -l
    ```
    ![iscsiadm node コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. 再スキャン スクリプト *rescan-scsi-bus.sh* を実行します。このスクリプトを実行すると、新しく作成されたディスクが表示されます。  両ノードでこれを実行します。 0 より大きい LUN 番号が表示されます (例: 1、2 など)。

    ```
    rescan-scsi-bus.sh
    ```
    ![スクリプトの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. デバイス名を取得するには、*fdisk -l* コマンドを実行します。 両ノードでこれを実行します。 サイズが **178 MiB** のデバイスを選択します。

    ```
      fdisk –l
    ```
    
    ![fdisk コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>SBD デバイスを初期化する

このセクションと以下のセクションは、新規と既存の両方のお客様に適用されます。

1. SBD デバイスを **両方** のノードで初期化します。

    ```
    sbd -d <SBD Device Name> create
    ```
    ![sbd create コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. デバイスに書き込まれた内容を確認します。 **両** ノードでこれを行います。

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-cluster"></a>クラスターを構成する

このセクションでは、SUSE HA クラスターをセットアップする手順について説明します。

1. 最初に、パッケージをインストールします。 ha_sles と SAPHanaSR-doc のパターンがインストールされているかどうかを確認します。 インストールされていない場合はインストールします。 **両** ノードにこれらをインストールします。

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![pattern コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    ![SAPHanaSR-doc コマンドの結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. 次に、クラスターをセットアップします。 *ha-cluster-init* コマンドまたは yast2 ウィザードを使用して、クラスターをセットアップできます。 この例では、yast2 ウィザードを使用しました。 このステップは **プライマリ ノードでのみ** 実施します。

    1. **[yast2]**  >  **[High Availability]\(高可用性\)**  >  **[Cluster]\(クラスター\)** に移動します
    
        ![YaST コントロール センターのスクリーンショット。[High Availability]\(高可用性\) と [Cluster]\(クラスター\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
        
        ![[Install]\(インストール\) と [Cancel]\(キャンセル\) ボタンが存在するダイアログ ボックスのスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
        
    1. halk2 パッケージは既にインストールされているので、 **[Cancel]\(キャンセル\)** を選択します。
    
        ![キャンセル オプションに関するメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)
    
    1. **[続行]** をクリックします。
    
        予期される値 = デプロイしているノードの数 (この場合は 2)。
        
        ![[Enable Security Auth]\(セキュリティ認証を有効にする\) チェック ボックスが表示された [Cluster Security]\(クラスター セキュリティ\) を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
        
    1. **[次へ]** を選択します。
    
        ![[Sync Host]\(同期ホスト\) と [Sync File]\(同期ファイル\) 一覧が表示されたクラスターの構成ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
        ノード名を追加し、[Add suggested files]\(示されたファイルを追加する\) を選択します。
        
    1. **[Turn csync2 ON]\(csync2 をオンにする\)** を選択します。
    
    1. **[Generate Pre-Shared-Keys]\(事前共有キーの生成\)** を選択します。下のポップアップが表示されます。
    
        ![キーが生成されたことを示すメッセージを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. **[OK]** を選択します。
    
        Csync2 で IP アドレスと事前共有キーを使って認証が実行されます。 キー ファイルは csync2 -k /etc/csync2/key_hagroup で生成されます。 作成された key_hagroup ファイルを、クラスターのすべてのメンバーに手動でコピーする必要があります。 **必ずファイルをノード 1 からノード 2 にコピーします**。
        
        ![クラスターのすべてのメンバーにキーをコピーするのに必要なオプションが表示されたクラスターの構成ダイアログ ボックスを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. **[次へ]** を選択します。
        ![クラスター サービス ウィンドウのスクリーンショット｡](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
        
        既定では [Booting]\(ブート\) オプションは off になっています。 これを on に変更し、起動時に Pacemaker を開始するようにします。 セットアップの要件に基づいて選ぶことができます。
    
    1. **[Next]\(次へ\)** をクリックしてクラスターの設定を完了します。

## <a name="set-up-the-softdog-watchdog"></a>softdog ウォッチドッグをセットアップします

このセクションでは、ウォッチドッグ (softdog) の構成について説明します。

1. 次の行を、**両方** のノードの */etc/init.d/boot.local* に追加します。
    
    ```
    modprobe softdog
    ```
    ![softdog 行が追加された boot ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. 次のようにして、**両方** のノードの */etc/sysconfig/sbd* ファイルを更新します。
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![SBD_DEVICE 値が追加された sbd ファイルを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. 次のコマンドを実行して、カーネル モジュールを **両方** のノードに読み込みます。
    
    ```
    modprobe softdog
    ```
    ![modprobe softdog コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. softdog が **両方** のノードで動作していることを、次のようにして確認します。
    
    ```
    lsmod | grep dog
    ```
    ![lsmod コマンドの実行結果が表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. **両方** のノードで SBD デバイスを起動します。

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![start コマンドが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. **両方** のノードで SBD デーモンをテストします。 両方のノードでこれを設定した後、2 つのエントリが表示されます。
    
    ```
    sbd -d <SBD Device Name> list
    ```
    ![2 つのエントリが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. **一方** のノードにテスト メッセージを送信します。

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    ![2 つのエントリが表示されたコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
8. **2 つ目** のノード (ノード 2) で、メッセージの状態を確認できます。
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![他のメンバーのテスト値を表示するメンバーの 1 つを含むコンソールウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. SBD の設定を適用するには、 */etc/sysconfig/sbd* ファイルを次のように編集します。 ファイルを **両** ノードで編集します。

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. **プライマリ ノード** (ノード 1) で Pacemaker サービスを開始します。

    ```
    systemctl start pacemaker
    ```
    ![pacemaker の開始後の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Pacemaker のサービスが *うまく機能しない* 場合は「*シナリオ 5: Pacemaker サービスが失敗する*」をご覧ください。

## <a name="join-the-cluster"></a>クラスターに参加する

次に、クラスターにノードを参加させます。

- ノードを追加します。 **node2** で次のコマンドを実行して、node2 をクラスターに参加させます。

    ```
    ha-cluster-join
    ```
    クラスターの追加時に *エラー* が出る場合は「*シナリオ 6: node2 がクラスターに参加できない*」を読んでください。

## <a name="validate-the-cluster"></a>クラスターを検証する

1. クラスター サービスを開始します。 確認し、必要に応じて **両方** のノードでクラスターを初めて開始します。
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![pacemaker の状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. 状態を監視します。 *crm_mon* コマンドを実行して **両** ノードがオンラインであることを確認します。 これは、クラスターの **どのノードでも** 実行できます。
    
    ```
    crm_mon
    ```
    ![crm_mon の結果が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    hawk にサインインして、クラスターの状態 *https://\<node IP>:7630* を確認することもできます。 既定のユーザーは hacluster で、パスワードは linux です。 必要であれば、*passwd* コマンドでパスワードを変更できます。
    
## <a name="configure-cluster-properties-and-resources"></a>クラスターのプロパティとリソースを構成する

このセクションでは、クラスター リソースを構成する手順について説明します。
この例では、次のリソースをセットアップします。その他のリソースは、(必要であれば) SUSE HA ガイドを参考にして設定できます。

- クラスターのブートストラップ
- STONITH デバイス
- 仮想 IP アドレス

構成は、**1 つのノード** でのみ行います。 これはプライマリ ノードで行ってください。

1. クラスターのブートストラップなどを構成する: クラスターのブートストラップを追加します。 ファイルを作成し、次のようにテキストを追加します。
    
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
2. 構成をクラスターに追加します。

    ```
    crm configure load update crm-bs.txt
    ```
    ![crm コマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. STONITH デバイスを構成する: リソース STONITH を追加し、ファイルを作成して、次のようにテキストを追加します。

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
      - 構成をクラスターに追加します。
        
          ```
          crm configure load update crm-sbd.txt
          ```
        
4. 仮想 IP アドレスを構成する: リソースの仮想 IP を追加します。 ファイルを作成し、次のようにテキストを追加します。

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    - 構成をクラスターに追加します。
    
        ```
        crm configure load update crm-vip.txt
        ```
        
5. 次に、リソースを検証します。 *crm_mon* コマンドを実行すると、2 つのリソースが表示されます。

    ![2 つのリソースが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    - *https://\<node IP address>:7630/cib/live/state* でも状態を確認できます。
    
        ![2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>フェールオーバー プロセスをテストする

1. フェールオーバー プロセスをテストするには、node1 の Pacemaker サービスを停止すると、リソースが node2 にフェールオーバーします。

    ```
    Service pacemaker stop
    ```
2. Pacemaker のサービスを **ノード 2** で停止します。するとリソースが **ノード 1** にフェールオーバーします。

    **フェールオーバー前**  
    ![フェールオーバー前の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    **フェールオーバー後**  
    ![フェールオーバー後の 2 つのリソースの状態を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![フェールオーバー後のリソースの状態が表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>トラブルシューティング
このセクションでは、セットアップ時に起こり得るいくつかのエラーについて説明します。 これらの問題は必ず発生するとは限りません。

### <a name="scenario-1-cluster-node-not-online"></a>シナリオ 1:クラスター ノードがオンラインではない

クラスター マネージャーでオンラインになっていないノードがある場合は、これをオンラインにするために次の方法を試すことができます。

1. この iSCSI サービスを起動します。

    ```
    service iscsid start
    ```
    
2. その iSCSI ノードにサインインします。

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

1. 必要なパッケージをインストールします。 "root" ユーザーとしてログインし、SMT セットアップでパッケージをダウンロードしてインストールする必要があります。

2. パッケージをインストールするには、[yast] > [Software]\(ソフトウェア\) > [Software Management]\(ソフトウェア管理\) > [Dependencies]\(依存関係\) > [Install recommended packages…]\(推奨されるパッケージをインストールする...\) オプションを使います。 次のスクリーンショットは、予想される画面を示しています。

    >[!NOTE]
    >この手順を両ノードで実行し、yast2 の GUI を両ノードで利用できるようにする必要があります。
    
    ![YaST コントロール センターが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. [Dependencies]\(依存関係\) で、 **[Install Recommended Packages]\(推奨パッケージのインストール\)** を選択します。

    ![コンソール ウィンドウのスクリーンショット。[Install Recommended Packages]\(推奨パッケージのインストール\) が選択されている。](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. 変更内容を確認し **[OK]** を選択します。

    ![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    パッケージのインストールが続行されます。

    ![インストールの進み具合を示す、コンソール ウィンドウのスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. **[次へ]** を選択します。

    ![成功メッセージが表示されたコンソール ウィンドウを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

6. **[完了]** を選択します。

7. libqt4 パッケージと libyui-qt パッケージもインストールする必要があります。
    
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

1. [Yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) で次のパターンを選択します。

    - SAP HANA サーバー ベース
    - C/C++ コンパイラとツール
    - 高可用性
    - SAP アプリケーション サーバー ベース

    次の画面では、パターンをインストールする手順を示します。

    [yast2] > [Software]\(ソフトウェア\) > [Software management]\(ソフトウェア管理\) の使用

    ![インストールを開始するために [Software]\(ソフトウェア\) と [Online Update]\(オンライン更新\) が選択された YaST コントロール センターを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. パターンを選択します。

    ![[C / C++ Compiler and Tools]\(C / C ++ コンパイラとツール\) の項目で 1 つ目のパターンを選択する操作のスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![[C / C++ Compiler and Tools]\(C / C ++ コンパイラとツール\) の項目で 2 つ目のパターンを選択する操作のスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

3. **[承諾]** を選択します。

    ![依存関係を解決するように変更されたパッケージが表示された [Changed Packages]\(変更されたパッケージ\) ダイアログ ボックスを示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. **[続行]** を選択します。

    ![[Performing Installation]\(インストールの実行中\) 状態ページが示されたスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. インストールが完了したら **[Next]\(次へ\)** をクリックします。

    ![インストールのレポートが表示されたスクリーンショット。](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>シナリオ 4: gcc アセンブリ エラーが出て HANA のインストールが失敗する
次のエラーが出て、HANA のインストールが失敗します。

![オペレーティング システムが gcc 5 のアセンブリを実行できる状態でないことを表すエラーのスクリーンショット。](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

- この問題を修正するには、次のスクリーンショットのようにして、ライブラリ (libgcc_sl と libstdc++6) をインストールする必要があります。

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

- これを修正するには、 */usr/lib/systemd/system/fstrim.timer* ファイルから次の行を削除します

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

1. それを修正するには、両方のノードで次のように実行します。

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![最初のノードでコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![2 番目のノードでコマンドを実行するコンソール ウィンドウの部分を示すスクリーンショット。](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. 上の修正を行ってから、ノード 2 をクラスターに追加します。

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
