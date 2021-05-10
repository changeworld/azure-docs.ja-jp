---
title: Azure Sentinel への接続を検証する | Microsoft Docs
description: セキュリティ ソリューションの接続を検証して、CEF メッセージが Azure Sentinel に転送されていることを確認します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: 3ce83de7f876bbd67120bf511d29860b71cd2227
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104771279"
---
# <a name="step-3-validate-connectivity"></a>手順 3:接続の検証

ログ フォワーダーをデプロイし (手順 1)、CEF メッセージを送信するようにセキュリティ ソリューションを構成したら (手順 2)、以下の手順に従って、セキュリティ ソリューションと Azure Sentinel との間の接続を検証します。 

## <a name="prerequisites"></a>前提条件

- ログ フォワーダー マシンに対する昇格されたアクセス許可 (sudo) が必要です。

- ログ フォワーダー マシンに **python 2.7** または **3** がインストールされている必要があります。<br>
`python –version` コマンドを使用して確認してください。

- 場合によっては、プロセスの間にワークスペース ID とワークスペースの主キーが必要になることがあります。 これらは、ワークスペース リソースの **[エージェント管理]** で確認できます。

## <a name="how-to-validate-connectivity"></a>接続を検証する方法

1. Azure Sentinel ナビゲーション メニューから **[ログ]** を開きます。 **CommonSecurityLog** スキーマを使用して、セキュリティ ソリューションからログが届いているかどうかを確認するクエリを実行します。<br>
ログが **Log Analytics** に表示され始めるまで、約 20 分かかる場合があります。 

1. クエリの結果がまったく表示されない場合は、セキュリティ ソリューションからイベントが生成されていることを確認するか、なんらかのイベントを生成してみてください。それらのイベントが、指定した Syslog フォワーダー マシンに転送されていることを確認します。 

1. ログ フォワーダーで次のスクリプトを実行し (プレースホルダーをワークスペース ID に置き換えます)、セキュリティ ソリューション、ログ フォワーダー、および Azure Sentinel 間の接続を確認します。 デーモンが適切なポートでリッスンしていること、転送が適切に構成されていること、デーモンと Log Analytics エージェントとの間の通信がブロックされていないことが、このスクリプトによって確認されます。 また、モック メッセージ "TestCommonEventFormat" を送信することで、エンドツーエンドの接続が確認されます。 <br>

    ```bash
    sudo wget -O cef_troubleshoot.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_troubleshoot.py&&sudo python cef_troubleshoot.py [WorkspaceID]
    ```

   - **"*コンピューター*" フィールドのマッピング** に関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[検証スクリプトの説明](#mapping-command)を参照してください。

    - **Cisco ASA ファイアウォール ログの解析** に関する問題を修正するためのコマンドを実行するよう指示するメッセージが表示される場合があります。 詳細については、[検証スクリプトの説明](#parsing-command)を参照してください。

## <a name="validation-script-explained"></a>検証スクリプトの説明

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

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Azure Sentinel ワークスペースから次のクエリを実行することによって観察できます。

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

1. MOCK データを localhost のポート 514 に送信します。 このデータは、Azure Sentinel ワークスペースから次のクエリを実行することによって観察できます。

    ```kusto
    CommonSecurityLog
    | where DeviceProduct == "MOCK"
    ```
---

## <a name="next-steps"></a>次のステップ

このドキュメントでは、CEF アプライアンスを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [Cef および CommonSecurityLog フィールド マッピング](cef-name-mapping.md)についての説明。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
