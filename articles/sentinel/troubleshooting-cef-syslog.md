---
title: Microsoft Sentinel と CEF または Syslog データ コネクタ間の接続のトラブルシューティング | Microsoft Docs
description: Microsoft Sentinel CEF または Syslog データ コネクタに関する問題をトラブルシューティングする方法について説明します。
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
ms.openlocfilehash: 627660330493b2d26c003145fc41dbadaa780052
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132517257"
---
# <a name="troubleshoot-your-cef-or-syslog-data-connector"></a>CEF または Syslog データ コネクタのトラブルシューティング

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

この記事では、Microsoft Sentinel の CEF または Syslog データ コネクタを検証してトラブルシューティングするための一般的な方法について説明します。

たとえば、ログが Microsoft Sentinel の Syslog または共通セキュリティ ログ テーブルに表示されない場合、データ ソースの接続に失敗しているか、データが取り込まれない別の原因が発生している可能性があります。

コネクタの展開が失敗した場合に発生する他の現象としては、**security_events.conf** または **security-omsagent.config.conf** ファイルのいずれかが欠落している場合や、rsyslog サーバーがポート 514 をリッスンしていない場合などがあります。

詳細については、「[Common Event Format を使用して外部ソリューションを接続する](connect-common-event-format.md)」と、「[Syslog を使用して Linux ベースのソースからデータを収集する](connect-syslog.md)」を参照してください。

> [!NOTE]
> Windows 用 Log Analytics エージェントは、多くの場合、*Microsoft Monitoring Agent (MMA)* と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、*OMS エージェント* と呼ばれます。
>

> [!TIP]
> トラブルシューティングの際には、この記事に記載されている手順を順に実行して、Syslog コレクター、オペレーティング システム、または OMS エージェントの問題をチェックして解決することをお勧めします。
>
> ドキュメントに記載されている手順とは異なる方法を使用してコネクタを展開し、問題が発生した場合は、展開を消去してから、ドキュメントに記載されている方法で再度インストールすることをお勧めします。
>

## <a name="validate-cef-connectivity"></a>CEF 接続の検証

[ログ フォワーダーをデプロイ](connect-common-event-format.md)し、[CEF メッセージを送信するようにセキュリティ ソリューションを構成](./connect-common-event-format.md)したら (手順 2)、本セクションの手順に従って、セキュリティ ソリューションと Microsoft Sentinel との間の接続を検証します。

1. 次の前提条件を満たしていることを確認します。

    - ログ フォワーダー マシンに対する昇格されたアクセス許可 (sudo) が必要です。

    - ログ フォワーダー マシンに **python 2.7** または **3** がインストールされている必要があります。 `python –version` コマンドを使用して確認してください。

    - 場合によっては、プロセスの間にワークスペース ID とワークスペースの主キーが必要になることがあります。 それらはワークスペース リソースの **[エージェント管理]** で確認できます。

1. Microsoft Sentinel ナビゲーション メニューから **[ログ]** を開きます。 **CommonSecurityLog** スキーマを使用して、セキュリティ ソリューションからログが届いているかどうかを確認するクエリを実行します。

    ログが **Log Analytics** に表示されるようになるまで、最大 20 分かかる場合があります。

1. クエリの結果がまったく表示されない場合は、セキュリティ ソリューションからイベントが生成されていることを確認するか、なんらかのイベントを生成してみてください。それらのイベントが、指定した Syslog フォワーダー マシンに転送されていることを確認します。

1. ログ フォワーダーで次のスクリプトを実行し (プレースホルダーをワークスペース ID に置き換えます)、セキュリティ ソリューション、ログ フォワーダー、および Microsoft Sentinel 間の接続を確認します。 デーモンが適切なポートでリッスンしていること、転送が適切に構成されていること、デーモンと Log Analytics エージェントとの間の通信がブロックされていないことが、このスクリプトによって確認されます。 また、モック メッセージ "TestCommonEventFormat" を送信することで、エンドツーエンドの接続が確認されます。 <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - **"*コンピューター*" フィールドのマッピング** に関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[検証スクリプトの説明](#mapping-command)を参照してください。

    - **Cisco ASA ファイアウォール ログの解析** に関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[検証スクリプトの説明](#parsing-command)を参照してください。

### <a name="cef-validation-script-explained"></a>CEF 検証スクリプトの説明

この検証スクリプトでは、次のチェックが実行されます。

# <a name="rsyslog-daemon"></a>[rsyslog デーモン](#tab/rsyslog)

1. ファイル <br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    が存在し、有効であることを確認します。

1. このファイルに次のテキストが含まれていることを確認します。

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 次のコマンドを使用して、Cisco ASA ファイアウォール イベントの解析が想定どおりに構成されていることを確認します。

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>解析に問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドによって、正しい解析が確認され、エージェントが再起動されます。

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 次のコマンドを使用して、Syslog ソースの "*コンピューター*" フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドによって、正しいマッピングが確認され、エージェントが再起動されます。

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. マシンのセキュリティにネットワーク トラフィックをブロックするような改良 (ホスト ファイアウォールなど) がなされているかどうかを確認します。

1. CEF として識別されたメッセージを TCP ポート 25226 で Log Analytics エージェントに送信するよう、syslog デーモン (rsyslog) が適切に構成されていることを確認します。

    - 構成ファイル: `/etc/rsyslog.d/security-config-omsagent.conf`

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
        ```

1. Syslog デーモンと Log Analytics エージェントを再起動します。

    ```bash
    service rsyslog restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 必要な接続が確立されていることを確認します。データの受信には TCP 514 が、また Syslog デーモンと Log Analytics エージェントとの間の内部通信には TCP 25226 が使用されます。

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog デーモンによりポート 514 でデータが受信されていること、およびエージェントによりポート 25226 でデータが受信されていることを確認します。

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Microsoft Sentinel ワークスペースから次のクエリを実行することによって観察できます。

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```

# <a name="syslog-ng-daemon"></a>[syslog-ng デーモン](#tab/syslogng)

1. ファイル <br>
    `/etc/opt/microsoft/omsagent/[WorkspaceID]/conf/omsagent.d/security_events.conf`<br>
    が存在し、有効であることを確認します。

1. このファイルに次のテキストが含まれていることを確認します。

    ```bash
    <source>
        type syslog
        port 25226
        bind 127.0.0.1
        protocol_type tcp
        tag oms.security
        format /(?<time>(?:\w+ +){2,3}(?:\d+:){2}\d+|\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}.[\w\-\:\+]{3,12}):?\s*(?:(?<host>[^: ]+) ?:?)?\s*(?<ident>.*CEF.+?(?=0\|)|%ASA[0-9\-]{8,10})\s*:?(?<message>0\|.*|.*)/
        <parse>
            message_format auto
        </parse>
    </source>

    <filter oms.security.**>
        type filter_syslog_security
    </filter>
    ```

1. 次のコマンドを使用して、Cisco ASA ファイアウォール イベントの解析が想定どおりに構成されていることを確認します。

    ```bash
    grep -i "return ident if ident.include?('%ASA')" /opt/microsoft/omsagent/plugin/security_lib.rb
    ```

    - <a name="parsing-command"></a>解析に問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドによって、正しい解析が確認され、エージェントが再起動されます。

        ```bash
        # Cisco ASA parsing fix
        sed -i "s|return '%ASA' if ident.include?('%ASA')|return ident if ident.include?('%ASA')|g" /opt/microsoft/omsagent/plugin/security_lib.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. 次のコマンドを使用して、Syslog ソースの "*コンピューター*" フィールドが、Log Analytics エージェントで正しくマップされていることを確認します。

    ```bash
    grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
    ```

    - <a name="mapping-command"></a>マッピングに問題がある場合は、**次のコマンドを手動で実行** するよう指示するエラー メッセージが表示されます (プレースホルダーをワークスペース ID に置き換えます)。 このコマンドによって、正しいマッピングが確認され、エージェントが再起動されます。

        ```bash
        # Computer field mapping fix
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. マシンのセキュリティにネットワーク トラフィックをブロックするような改良 (ホスト ファイアウォールなど) がなされているかどうかを確認します。

1. CEF として識別 (正規表現を使用) されたメッセージを TCP ポート 25226 で Log Analytics エージェントに送信するよう、syslog デーモン (syslog-ng) が適切に構成されていることを確認します。

    - 構成ファイル: `/etc/syslog-ng/conf.d/security-config-omsagent.conf`

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. Syslog デーモンと Log Analytics エージェントを再起動します。

    ```bash
    service syslog-ng restart

    /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
    ```

1. 必要な接続が確立されていることを確認します。データの受信には TCP 514 が、また Syslog デーモンと Log Analytics エージェントとの間の内部通信には TCP 25226 が使用されます。

    ```bash
    netstat -an | grep 514

    netstat -an | grep 25226
    ```

1. Syslog デーモンによりポート 514 でデータが受信されていること、およびエージェントによりポート 25226 でデータが受信されていることを確認します。

    ```bash
    sudo tcpdump -A -ni any port 514 -vv

    sudo tcpdump -A -ni any port 25226 -vv
    ```

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Microsoft Sentinel ワークスペースから次のクエリを実行することによって観察できます。

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="verify-cef-or-syslog-prerequisites"></a>CEF または Syslog の前提条件を確認する

次のセクションを使用して、CEF または Syslog データ コネクタの前提条件を確認します。

### <a name="azure-virtual-machine-as-a-syslog-collector"></a>Syslog コレクターとしての Azure 仮想マシン

Azure 仮想マシンを Syslog コレクターとして使用している場合は、次のことを確認してください。

- Syslog データ コネクタの設定中は、[MMA または OMS エージェント[の ](../security-center/security-center-enable-data-collection.md)Microsoft Defender for Cloud の自動プロビジョニング設定](connect-windows-security-events.md#connector-options)を必ずオフにするようにしてください。

    データ コネクタの設定が完了したら、再びオンにすることができます。

- [Common Event Format データ コネクタの python スクリプト](./connect-log-forwarder.md)をデプロイする前に、仮想マシンが既に既存の Syslog ワークスペースに接続されていないことを確認してください。 この情報は、Syslog ワークスペースに接続されている VM が **[接続済み]** と表示されている、Log Analytics ワークスペースの仮想マシンの一覧にあります。

- Microsoft Sentinel が、**SecurityInsights** ソリューションがインストールされている正しい Syslog ワークスペースに接続されていることを確認します。

    詳細については、[手順 1: ログ フォワーダーを展開する](./connect-log-forwarder.md)方法に関するページを参照してください。

- 少なくとも必要な前提条件を満たすよう、マシンのサイズが正しく設定されていることを確認します。 詳細については、[CEF の前提条件](connect-common-event-format.md#prerequisites)に関するセクションを参照してください。

### <a name="on-premises-or-a-non-azure-virtual-machine"></a>オンプレミスまたは Azure 以外の仮想マシン

データ コネクタにオンプレミスのマシンまたは Azure 以外の仮想マシンを使用している場合は、サポートされている Linux オペレーティング システムの新規インストールでインストールスクリプトを実行していることを確認してください。

> [!TIP]
> このスクリプトは、Microsoft Sentinel の **Common Event Format** のデータ コネクタ ページで取得することもできます。
>

```cli
sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py <WorkspaceId> <Primary Key>
```

### <a name="enable-your-syslog-facility-and-log-severity-collection"></a>Syslog ファシリティとログの重要度の収集を有効にする

Syslog サーバー (rsyslog または syslog-ng) は、関連する構成ファイルで定義されているすべてのデータを転送します。これには、Log Analytics ワークスペースで定義されている設定が自動的に設定されます。

Microsoft Sentinel に取り込むファシリティと重要度のログ レベルに関する詳細を追加するようにしてください。 構成プロセスには約 20 分かかる場合があります。

詳細については、[デプロイ スクリプトの説明](./connect-log-forwarder.md#deployment-script-explained)に関するページと、[Azure portal で Syslog を構成する](../azure-monitor/agents/data-sources-syslog.md)方法に関するページを参照してください。


**たとえば、rsyslog サーバーの場合** は、次のコマンドを実行して、Syslog 転送の現在の設定を表示し、構成ファイルへの変更を確認します。

```bash
cat /etc/rsyslog.d/95-omsagent.conf
```

この場合、rsyslog では、以下のような出力が表示されます。 このファイルの内容は、**Log Analytics ワークスペース クライアントの構成 - Syslog ファシリティ 設定** 画面の Syslog 構成で定義された内容が反映されている必要があります。


```bash
OMS Syslog collection for workspace c69fa733-da2e-4cf9-8d92-eee3bd23fe81
auth.=alert;auth.=crit;auth.=debug;auth.=emerg;auth.=err;auth.=info;auth.=notice;auth.=warning  @127.0.0.1:25224
authpriv.=alert;authpriv.=crit;authpriv.=debug;authpriv.=emerg;authpriv.=err;authpriv.=info;authpriv.=notice;authpriv.=warning  @127.0.0.1:25224
cron.=alert;cron.=crit;cron.=debug;cron.=emerg;cron.=err;cron.=info;cron.=notice;cron.=warning  @127.0.0.1:25224
local0.=alert;local0.=crit;local0.=debug;local0.=emerg;local0.=err;local0.=info;local0.=notice;local0.=warning  @127.0.0.1:25224
local4.=alert;local4.=crit;local4.=debug;local4.=emerg;local4.=err;local4.=info;local4.=notice;local4.=warning  @127.0.0.1:25224
syslog.=alert;syslog.=crit;syslog.=debug;syslog.=emerg;syslog.=err;syslog.=info;syslog.=notice;syslog.=warning  @127.0.0.1:25224
```


**rsyslog サーバー CEF 転送の場合**、次のコマンドを実行して、Syslog 転送の現在の設定を表示し、構成ファイルへの変更を確認します。

```bash
cat /etc/rsyslog.d/security-config-omsagent.conf
```

この場合、rsyslog では、以下のような出力が表示されます。

```bash
if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226
```

## <a name="troubleshoot-operating-system-issues"></a>オペレーティング システムの問題のトラブルシューティング

この手順では、オペレーティング システムの構成に起因する問題をトラブルシューティングする方法について説明します。

**オペレーティング システムの問題のトラブルシューティング方法**:

1. まだの場合は、サポートされているバージョンのオペレーティング システムと Python を使用していることを確認してください。 詳細については、[CEF の前提条件](connect-common-event-format.md#prerequisites)に関するセクションと、「[Linux マシンまたはアプライアンスを構成する](connect-syslog.md#configure-your-linux-machine-or-appliance)」を参照してください。

1. 仮想マシンが Azure にある場合は、ネットワーク セキュリティ グループ (NSG) によって、ポート 514 のログ クライアント (センダー) からの TCP/UDP 接続が許可されていることを確認します。

1. パケットが Syslog コレクターに到着していることを確認します。 Syslog コレクターに到着する Syslog パケットをキャプチャするには、次を実行します。

    ```config
    tcpdump -Ani any port 514 and host <ip_address_of_sender> -vv
    ```

1. 次のいずれかの操作を行います。

    - パケットが到着していない場合は、NSG セキュリティ グループのアクセス許可と Syslog コレクターへのルーティング パスを確認してください。

    - パケットが到着している場合は、これらが拒否されていないことを確認します。

    拒否されているパケットがある場合は、IP テーブルによって接続がブロックされていないか確認します。

    パケットが拒否されていないか確認するには、次を実行します。

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. Syslog サーバーでログが処理されているかどうかを確認します。 次を実行します。

    ```config
    tail -f /var/log/messages or tail -f /var/log/syslog
    ```

    処理されている Syslog または CEF ログは、プレーン テキストで表示されます。

1. rsyslog サーバーが TCP/UDP ポート 514 でリッスンしていることを確認します。 次を実行します。

    ```config
    netstat -anp | grep syslog
    ```

    Syslog コレクターに送信されている CEF または ASA ログがある場合は、TCP ポート 25226 で接続が確立されていることが確認できます。

    次に例を示します。

    ```config
    0 127.0.0.1:36120 127.0.0.1:25226 ESTABLISHED 1055/rsyslogd
    ```

    接続がブロックされている場合、[OMS エージェントへの SELinux 接続がブロックされている](#selinux-blocking-connection-to-the-oms-agent)か、[ファイアウォールのプロセスがブロックされている](#blocked-firewall-policy)可能性があります。 次の一連の手順を使用して、問題を特定してください。

### <a name="selinux-blocking-connection-to-the-oms-agent"></a>SELinux による OMS エージェントへの接続のブロック

この手順では、SELinux が現在 `permissive` 状態にあるかどうか、または OMS エージェントへの接続をブロックしているかどうかを確認する方法について説明します。 この手順は、お使いのオペレーティング システムが RedHat または CentOS からのディストリビューションである場合に関連します。

> [!NOTE]
> Microsoft Sentinel の CEF および Syslog のサポートには、FIPS のセキュリティ強化のみが含まれます。 SELinux や CIS などの他のセキュリティ強化方法は、現在サポートされていません。
>

1. 次を実行します。

    ```config
    sestatus
    ```

    状態は、次のいずれかの値として報告されます。

    - `disabled`. この構成は、Microsoft Sentinel への接続でサポートされています。
    - `permissive`. この構成は、Microsoft Sentinel への接続でサポートされています。
    - `enforced`. この構成はサポートされていないので、状態を無効にするか、`permissive` に設定する必要があります。

1. 現在、状態が `enforced` に設定されている場合は、一時的にオフにして、これがブロックの要因であるかどうかを確認してください。 次を実行します。

    ```config
    setenforce 0
    ```

    > [!NOTE]
    > この手順では、サーバーが再起動するまでの間だけ SELinux をオフにします。 SELinux をオフにするために、構成を変更してください。
    >

1. 正常に変更できたかどうかを確認するには、次を実行します。

    ```
    getenforce
    ```

    状態 `permissive` が返されるはずです。

> [!IMPORTANT]
> この設定の更新は、システムを再起動すると失われます。 この設定を永久に `permissive` に更新するには、`SELINUX` の値を `SELINUX=permissive` に変更して、 **/etc/selinux/config** ファイルを修正してください。
>
> 詳細については、[RedHat のドキュメント](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/using_selinux/changing-selinux-states-and-modes_using-selinux)を参照してください。


### <a name="blocked-firewall-policy"></a>ブロックされたファイアウォール ポリシー

この手順では、ファイアウォール ポリシーによって Rsyslog デーモンから OMS エージェントへの接続がブロックされているかどうかを確認する方法と、必要に応じて無効にする方法について説明します。


1. 次のコマンドを実行して、IP テーブルに拒否があるかどうかを確認します。これは、ファイアウォール ポリシーによってドロップされたトラフィックを示します。

    ```config
    watch -n 2 -d iptables -nvL
    ```

1. ファイアウォール ポリシーを有効にしたままにするには、接続を許可するポリシー規則を作成します。 必要に応じて、アクティブなファイアウォールを介して TCP/UDP ポート 25226 および 25224 を許可する規則を追加します。

    次に例を示します。

    ```config
    Every 2.0s: iptables -nvL                      rsyslog: Wed Jul  7 15:56:13 2021

    Chain INPUT (policy ACCEPT 6185K packets, 2466M bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
     pkts bytes target     prot opt in     out     source               destination


    Chain OUTPUT (policy ACCEPT 6792K packets, 6348M bytes)
     pkts bytes target     prot opt in     out     source               destination
    ```

1. アクティブなファイアウォールを介して TCP/UDP ポート 25226 および 25224 を許可する規則を作成するには、必要に応じて規則を追加します。

    1. ファイアウォール ポリシー エディターをインストールするには、次を実行します。

        ```config
        yum install policycoreutils-python
        ```

    1. ファイアウォール ポリシーにファイアウォール規則を追加します。 次に例を示します。

        ```config
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25226  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p udp --dport 25224  -j ACCEPT
        sudo firewall-cmd --direct --add-rule ipv4 filter INPUT 0 -p tcp --dport 25224  -j ACCEPT
        ```

    1. 例外が追加されていることを確認します。 次を実行します。

        ```config
        sudo firewall-cmd --direct --get-rules ipv4 filter INPUT
        ```

    1. ファイアウォールを再度読み込みます。 次を実行します。

        ```config
        sudo firewall-cmd --reload
        ```

> [!NOTE]
> ファイアウォールを無効にするには、`sudo systemctl disable firewalld` を実行してください。
>

## <a name="linux-and-oms-agent-related-issues"></a>Linux と OMS エージェントに関連する問題

この記事のここまでの手順を実行しても問題が解決しない場合は、OMS エージェントと Microsoft Sentinel ワークスペース間の接続に問題がある場合が考えられます。

このような場合は、次を確認してトラブルシューティングを続行します。

- Syslog コレクターで TCP/UDP ポート 514 にパケットが到着していることを確認します

- ローカル ログ ファイル ( **/var/log/messages** または **/var/log/syslog**) にログが書き込まれていることを確認します

- ポート 25224、25226、または両方で、データ パケットが流れていることを確認します

- 仮想マシンからポート 443 への TCP 経由のアウトバウンド接続があること、または [Log Analytics エンドポイント](../azure-monitor/agents/log-analytics-agent.md#network-requirements)に接続できることを確認します

- ファイアウォール ポリシーを使用して、Syslog コレクターから必要な URL にアクセスできることを確認します。 詳細については、[Log Analytics エージェントのファイアウォール要件](../azure-monitor/agents/log-analytics-agent.md#firewall-requirements)に関するページを参照してください。

- ワークスペースの仮想マシンの一覧に、Azure 仮想マシンが接続済みとして表示されていることを確認します。

次のコマンドを実行して、エージェントが Azure に正常に接続できているのか、または OMS エージェントから Log Analytics ワークスペースへの接続がブロックされているのかを確認します。

```config
Heartbeat
 | where Computer contains "<computername>"
 | sort by TimeGenerated desc
```

エージェントが正常に接続できている場合は、ログ エントリが返されます。 されなかった場合、OMS エージェントがブロックされている可能性があります。

## <a name="next-steps"></a>次のステップ

この記事のトラブルシューティング手順で問題が解決しない場合は、サポート チケットを開くか、Microsoft Sentinel のコミュニティ リソースを使ってください。 詳細については、「[Microsoft Sentinel の操作に便利なリソース](resources.md)」を参照してください。

Microsoft Sentinel の詳細については、次の記事を参照してください。

- [Cef および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](get-visibility.md)する方法についての説明。
- [Microsoft Sentinel を使った脅威の検出](./detect-threats-built-in.md)の概要。
- [ブックを使用](monitor-your-data.md)してデータを監視する。
