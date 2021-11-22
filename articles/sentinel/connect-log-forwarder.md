---
title: ログ フォワーダーをデプロイして Syslog および CEF ログを Microsoft Sentinel に取り込む | Microsoft Docs
description: Syslog と CEF ログを Microsoft Sentinel に取り込むプロセスの一環として、Syslog デーモンと Log Analytics エージェントで構成されるログ フォワーダーをデプロイする方法について説明します。
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6279e4cee368a36d66db5f41a326194fb04fe397
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518586"
---
# <a name="deploy-a-log-forwarder-to-ingest-syslog-and-cef-logs-to-microsoft-sentinel"></a>ログ フォワーダーをデプロイして Syslog および CEF ログを Microsoft Sentinel に取り込む

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Syslog と CEF ログを Microsoft Sentinel に取り込むには (特に Log Analytics エージェントを直接インストールできないデバイスとアプライアンスから)、デバイスからログを収集して Microsoft Sentinel ワークスペースに転送する Linux マシンを指定して構成する必要があります。 このマシンは、オンプレミス環境の物理マシンや仮想マシン、Azure VM のほか、別のクラウドの VM でもかまいません。 

このマシンには、このプロセスの要素である 2 つのコンポーネントがあります。

- syslog デーモン (**rsyslog** または **syslog-ng**)。ログを収集します。
- **Log Analytics エージェント** (別名 OMS エージェント)。ログを Microsoft Sentinel に転送します。

以下に用意されているリンクを使用し、指定したマシンで、次のタスクを実行するスクリプトを実行します。

- Linux 用 Log Analytics エージェント ("OMS エージェント" ともいいます) をインストールし、次の用途に構成します。
    - 組み込みの Linux Syslog デーモンからの CEF メッセージを TCP ポート 25226 でリッスンする
    - メッセージの解析とエンリッチが行われる Microsoft Sentinel ワークスペースに対し、TLS で安全にメッセージを送信する

- 組み込みの Linux Syslog デーモン (rsyslog.d/syslog-ng) を次の用途に構成します。
    - セキュリティ ソリューションからの Syslog メッセージを TCP ポート 514 でリッスンする
    - CEF として識別したメッセージだけを localhost の Log Analytics エージェントに TCP ポート 25226 を使用して転送する
 
## <a name="prerequisites"></a>前提条件

使用するマシンは次の要件を満たしている必要があります。

- **ハードウェア (物理/仮想)**

    - Linux マシンには、少なくとも **4 つの CPU コアと 8 GB の RAM** が必要です。

        > [!NOTE]
        > - **rsyslog** デーモンを使用している 1 つのログ フォワーダー マシンには、収集される **最大 8500 EPS (1 秒あたりのイベント数)** のサポートされる容量があります。

- **オペレーティング システム**

    - CentOS 7 と 8 (6 は不可)、マイナー バージョンを含む (64 ビット/32 ビット)
    - Amazon Linux 2017.09 (64 ビットのみ)
    - Oracle Linux 7 (64 ビット/32 ビット)
    - Red Hat Enterprise Linux (RHEL) Server 7 と 8 (6 は不可)、マイナー バージョンを含む (64 ビット/32 ビット)
    - Debian GNU と Linux 8 と 9 (64 ビット/32 ビット)
    - Ubuntu Linux 14.04 LTS と 16.04 LTS (64 ビット/32 ビット)、および 18.04 LTS (64 ビットのみ)
    - SUSE Linux Enterprise Server 12、15 (64 ビットのみ)

- **デーモンのバージョン**

    - Rsyslog: v8
    - Syslog-ng:2.1 - 3.22.1

- **パッケージ**
    - Linux マシンに **python 2.7** または **3** がインストールされている必要があります。<br>`python --version` または `python3 --version` コマンドを使用して確認してください。

- **Syslog RFC サポート**
  - Syslog RFC 3164
  - Syslog RFC 5424
 
- **構成**
    - 指定した Linux マシンに対する昇格されたアクセス許可 (sudo) が必要です。
    - Log Analytics エージェントをインストールする前に Linux マシンを Azure ワークスペースに接続することは避けてください。

- **データ**
    - 場合によっては、プロセスの間に Microsoft Sentinel ワークスペースの **ワークスペース ID** と **ワークスペースの主キー** が必要になります。 これらは、ワークスペース設定の **[エージェント管理]** で確認できます。

### <a name="security-considerations"></a>セキュリティに関する考慮事項

組織のセキュリティ ポリシーに従って、コンピューターのセキュリティを構成してください。 たとえば、企業のネットワーク セキュリティ ポリシーに合わせてネットワークを構成し、デーモンのポートとプロトコルを要件に合わせて変更することができます。 [Azure で VM をセキュリティ保護する](../virtual-machines/security-policy.md)方法に関するページと[ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)に関するページの手順を行うと、マシンのセキュリティ構成を改善できます。

デバイスが TLS 経由で Syslog および CEF ログを送信している場合 (ログ フォワーダーがクラウド内にある場合など) は、TLS で通信するように Syslog デーモン (rsyslog または syslog-ng) を構成する必要があります。 詳しくは、次のドキュメントをご覧ください。  
- [TLS を使用した Syslog トラフィックの暗号化 – rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html)
- [TLS を使用したログ メッセージの暗号化 – syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298)

## <a name="run-the-deployment-script"></a>展開スクリプトを実行する
 
1. Microsoft Sentinel のナビゲーション メニューから、 **[Data connectors]\(データ コネクタ\)** を選択します。 コネクタ ギャラリーから製品のコネクタを選択し (製品がリストにない場合は **Common Event Format** (CEF) を選択)、右下の **[コネクタ ページを開く]** ボタンをクリックします。 

1. コネクタ ページの、 **[1.2 Linux マシンへの CEF コレクターのインストール]** の手順の中で、 **[Run the following script to install and apply the CEF collector]\(CEF コレクターをインストールして適用するには、次のスクリプトを実行します\)** の下にあるリンクをコピーします。  
そのページにアクセスできない場合は、次のテキストからリンクをコピーします (プレースホルダーを置き換えるように、上記から **ワークスペース ID** と **主キー** をコピーして貼り付けます)。

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```
1. ログ フォワーダーのコマンド ラインにリンクまたはテキストを貼り付け、実行します。

1. スクリプトの実行中に、エラーまたは警告メッセージが表示されないことを確認してください。
    - *[コンピューター]* フィールドのマッピングに関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[デプロイ スクリプトの説明](#mapping-command)を参照してください。

1. [CEF メッセージを送信するようにデバイスを構成します](connect-common-event-format.md#configure-your-device)。

    > [!NOTE]
    > **同じマシンを使用してプレーンな Syslog *と* CEF メッセージの両方を転送する**
    >
    > このログ フォワーダー マシンを使用して [Syslog メッセージ](connect-syslog.md)と CEF を転送する予定であれば、Syslog および CommonSecurityLog テーブルへのイベントの重複を回避するために、以下を実行します。
    >
    > 1. CEF 形式でフォワーダーにログを送信する各ソース マシンで、Syslog 構成ファイルを編集し、CEF メッセージの送信に使用されているファシリティを削除する必要があります。 これで、CEF で送信されるファシリティは、Syslog で送信されません。 この方法の詳細については、「[Linux エージェントでの Syslog の構成](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent)」を参照してください。
    >
    > 1. これらのマシンで次のコマンドを実行して、エージェントと Microsoft Sentinel の Syslog 構成との同期を無効にする必要があります。 これで、前の手順で構成に加えた変更が上書きされなくなります。<br>
    > `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="deployment-script-explained"></a>デプロイ スクリプトの説明

次に、デプロイ スクリプトによって実行されるアクションをコマンドごとに説明します。

syslog デーモンを選択して、適切な説明を表示してください。

# <a name="rsyslog-daemon"></a>[rsyslog デーモン](#tab/rsyslog)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics エージェントをインストールします。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **ポート 25226 でリッスンして、CEF メッセージを Microsoft Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    - Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog デーモンを構成する**

    - syslog 構成ファイル `/etc/rsyslog.conf` を使用して TCP 通信用のポート 514 を開きます。

    - 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/rsyslog.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **Syslog デーモンと Log Analytics エージェントを再起動する**

    - rsyslog デーモンを再起動します。
    
        ```bash
        service rsyslog restart
        ```

    - Log Analytics エージェントを再起動します。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Computer* フィールドのマッピングが想定どおりになっていることを確認する**

    - 次のコマンドを使用して、Syslog ソースの *[コンピューター]* フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドは、正しいマッピングを確認して、エージェントを再起動します。
    
        ```bash
        sudo sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng デーモン](#tab/syslogng)

1. **Log Analytics エージェントをダウンロードしてインストールする**

    - Log Analytics (OMS) Linux エージェントのインストール スクリプトをダウンロードします。

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Log Analytics エージェントをインストールします。
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **ポート 25226 でリッスンして、CEF メッセージを Microsoft Sentinel に転送するよう、Log Analytics エージェントの構成を設定する**

    - Log Analytics エージェントの GitHub リポジトリから構成をダウンロードします。

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **Syslog デーモンを構成する**

    - syslog 構成ファイル `/etc/syslog-ng/syslog-ng.conf` を使用して TCP 通信用のポート 514 を開きます。

    - 特殊な構成ファイル `security-config-omsagent.conf` を syslog デーモン ディレクトリ `/etc/syslog-ng/conf.d/` に挿入して、CEF メッセージを TCP ポート 25226 で Log Analytics エージェントに転送するようにデーモンを構成します。

        `security-config-omsagent.conf` ファイルの内容は次のとおりです。

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **Syslog デーモンと Log Analytics エージェントを再起動する**

    - syslog-ng デーモンを再起動します。
    
        ```bash
        service syslog-ng restart
        ```

    - Log Analytics エージェントを再起動します。

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. ***Computer* フィールドのマッピングが想定どおりになっていることを確認する**

    - 次のコマンドを使用して、Syslog ソースの *[コンピューター]* フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドは、正しいマッピングを確認して、エージェントを再起動します。
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Log Analytics エージェントをデプロイして、CEF アプライアンスを Microsoft Sentinel に接続する方法について説明しました。 Microsoft Sentinel の詳細については、次の記事を参照してください。

- [Cef および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使用した脅威の検出](./detect-threats-built-in.md)の概要。
