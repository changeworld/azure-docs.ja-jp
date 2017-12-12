---
title: "Linux コンピューターを Azure Log Analytics に接続する | Microsoft Docs"
description: "この記事では、Azure、他のクラウド、またはオンプレミスでホストされている Linux コンピューターを、OMS エージェント for Linux を使用して Log Analytics に接続する方法について説明します。"
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2017
ms.author: magoedte
ms.openlocfilehash: 1f6e0f2a638d9ce9b06f19feba1c9e216812862c
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/01/2017
---
# <a name="connect-your-linux-computers-to-log-analytics"></a>Linux コンピューターを Log Analytics に接続する 

Log Analytics を使用すれば、Linux コンピューターやコンテナー ソリューション (Docker など) から生成されたデータを収集して操作することができます。こうしたデータの生成元は、オンプレミスのデータセンターに物理サーバーや仮想マシンとして存在することもあれば、アマゾン ウェブ サービス (AWS) や Microsoft Azure などのクラウドでホストされるサービス内の仮想マシンとして存在することもあります。 また、Change Tracking など、[Azure Automation](../automation/automation-intro.md) で使用できる管理ソリューションを使用して構成変更を識別したり、Update Management を使用してソフトウェア更新を管理し、Linux VM のライフサイクルを積極的に管理することもできます。 

OMS エージェント for Linux は TCP ポート 443 を介して Log Analytics および Azure Automation サービスとアウトバウンド通信を行います。コンピューターがファイアウォールまたはプロキシ サーバーに接続してインターネット経由で通信している場合は、適用する必要がある構成変更について、「[プロキシ サーバーまたは OMS ゲートウェイで使用するためのエージェントの構成](#configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway)」を確認してください。  System Center 2016 - Operations Manager または Operations Manager 2012 R2 でコンピューターを監視している場合は、Log Analytics サービスとマルチホームしてデータを収集し、サービスに転送して、Operations Manager で引き続き監視することができます。  Log Analytics (現在、Operations Manager オペレーション コンソールでは Operations Management Suite と呼ばれています) と統合されている Operations Manager 管理グループで監視されている Linux コンピューターは、データ ソースの構成を受信して、管理グループを介して収集されたデータを転送しません。  OMS エージェントは、複数の Log Analytics ワークスペースにレポートするように構成することはできません。  

IT セキュリティ ポリシーで、ネットワーク上のコンピューターによるインターネットへの接続が許可されていない場合、有効にしたソリューションに応じて、エージェントが OMS ゲートウェイに接続して構成情報を受信し、収集されたデータを送信するように構成できます。 OMS Linux Agent が OMS ゲートウェイ経由でサービスと通信するように構成する方法の詳細と手順については、[OMS ゲートウェイを使用した OMS への接続](log-analytics-oms-gateway.md)に関する記事を参照してください。  

次の図は、エージェントで管理されている Linux コンピューターと Log Analytics 間の接続およびその方向とポートを示しています。

![エージェントと Azure Services の直接通信の図](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>システム要件
始める前に、次の詳細を確認し、前提条件が満たされていることを確認してください。

### <a name="supported-linux-operating-systems"></a>サポートされている Linux オペレーティング システム
次の Linux ディストリビューションは公式にサポートされています。  ただし OMS Agent for Linux は、ここに記載された以外のディストリビューションでも動作します。

* Amazon Linux 2012.09 ～ 2015.09 (x86/x64)
* CentOS Linux 5、6、および 7 (x86/x64)
* Oracle Linux 5、6、および 7 (x86/x64)
* Red Hat Enterprise Linux Server 5、6、および 7 (x86/x64)
* Debian GNU/Linux 6、7、および 8 (x86/x64)
* Ubuntu 12.04 LTS、14.04 LTS, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 および 12 (x86/x64)

### <a name="network"></a>ネットワーク
以下に、Linux エージェントが Log Analytics および Azure Automation と通信するために必要なプロキシとファイアウォールの構成情報の一覧を示します。 トラフィックはネットワークからサービスへの送信です。 

|エージェントのリソース| ポート |  
|------|---------|  
|*.ods.opinsights.azure.com | ポート 443|   
|*.oms.opinsights.azure.com | ポート 443|   
|*.blob.core.windows.net/ | ポート 443|   
|*.azure-automation.net | ポート 443|  

### <a name="package-requirements"></a>パッケージの要件

 **必須パッケージ**   | **説明**   | **最小バージョン**
--------------------- | --------------------- | -------------------
Glibc | GNU C ライブラリ   | 2.5-12 
Openssl | OpenSSL ライブラリ | 0.9.8e または 1.0
Curl | cURL Web クライアント | 7.15.5
Python-ctypes | | 
PAM | Pluggable Authentication Module (プラグ可能な認証モジュール) | 

> [!NOTE]
>  syslog メッセージを収集するには、rsyslog または syslog-ng が必要となります。 syslog イベントの収集に関して、バージョン 5 の Red Hat Enterprise Linux、CentOS、Oracle Linux 版の既定の syslog デーモン (sysklog) はサポートされません。 このバージョンの各種ディストリビューションから syslog データを収集するには、rsyslog デーモンをインストールし、sysklog を置き換えるように構成する必要があります。 

エージェントは複数のパッケージで構成されます。 リリース ファイルには、次のパッケージが含まれており、シェル バンドルを `--extract` で実行することによって抽出できます。

**パッケージ** | **バージョン** | **説明**
----------- | ----------- | --------------
omsagent | 1.4.0 | Operations Management Suite Agent for Linux
omsconfig | 1.1.1 | OMS Agent 用の構成エージェント
omi | 1.2.0 | Open Management Infrastructure (OMI) - 軽量の CIM サーバー
scx | 1.6.3 | オペレーティング システムのパフォーマンス メトリックの OMI CIM プロバイダー
apache-cimprov | 1.0.1 | OMI の Apache HTTP Server パフォーマンス監視プロバイダー。 Apache HTTP Server が検出された場合にインストールされます。
mysql-cimprov | 1.0.1 | OMI の MySQL Server パフォーマンス監視プロバイダー。 MySQL/MariaDB サーバーが検出された場合にインストールされます。
docker-cimprov | 1.0.0 | OMI の Docker プロバイダー。 Docker が検出された場合にインストールされます。

### <a name="compatibility-with-system-center-operations-manager"></a>System Center Operations Manager との共存
OMS Agent for Linux は、エージェントのバイナリを System Center Operations Manager エージェントと共有します。 現在 Operations Manager で管理されているシステムに OMS エージェント for Linux をインストールすると、コンピューター上の OMI パッケージと SCX パッケージが、より新しいバージョンにアップグレードされます。 このリリースでは、OMS と System Center 2016 - Linux 用の Operations Manager/Operations Manager 2012 R2 エージェントに互換性があります。 

> [!NOTE]
> System Center 2012 SP1 以前のバージョンは現在、OMS Agent for Linux と互換性がなく、サポートされていません。<br>
> OMS Agent for Linux のインストール先となるコンピューターが現在は Operations Manager で監視されておらず、後で Operations Manager で監視する場合、そのコンピューターを検出する前に [OMI の構成](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager)を変更する必要があります。 **Operations Manager エージェントが OMS Agent for Linux より先にインストールされている場合、この手順は*不要*です。**

### <a name="system-configuration-changes"></a>システム構成の変更
OMS Agent for Linux のパッケージをインストールした後、システム全体に関連した構成の変更が別途適用されます。 omsagent パッケージをアンインストールすると、これらのアーティファクトは削除されます。

* `omsagent` という名前の非特権ユーザーが作成されます。 omsagent デーモンはこのアカウントで実行されます。
* sudoers の "include" ファイルは /etc/sudoers.d/omsagent に作成されます。 これにより、syslog および omsagent デーモンを再起動する権限が omsagent に与えられます。 sudo の "include" ディレクティブが、インストールされているバージョンの sudo でサポートされていない場合、そのエントリが /etc/sudoers に書き込まれます。
* イベントの一部をエージェントに転送するよう syslog の構成が変更されます。 詳細については、以下の「 **データ収集の構成** 」を参照してください。

### <a name="upgrade-from-a-previous-release"></a>以前のリリースからのアップグレード
このリリースでは、1.0.0-47 より前のバージョンからのアップグレードがサポートされます。 `--upgrade` コマンドを使用してインストールを実行すると、エージェントのすべてのコンポーネントが最新バージョンにアップグレードされます。

## <a name="installing-the-agent"></a>エージェントのインストール

このセクションでは、バンドルを使用して手動で OMS エージェント for Linux をインストールする方法について説明します。これには、エージェント コンポーネントごとに Debian パッケージと RPM パッケージが含まれており、  直接インストールすることも、抽出して個々のパッケージを取得することもできます。  Azure Linux VM にエージェントをインストールする場合は、トピック「[Azure Virtual Machines に関するデータの収集](log-analytics-quick-collect-azurevm.md)」を参照して、Log Analytics VM 拡張機能を使用してエージェントをインストールする方法を学習してください。  「*Log Analytics VM 拡張機能を有効にする*」セクションの手順に従います。  環境内でホストされている Linux コンピューターの場合は、記事「[環境内でホストされている Linux コンピューターからデータを収集する](log-analytics-quick-collect-linux-computer.md)」で説明されているスクリプト化された手法を使用してインストール プロセスを簡略化できます。  

> [!NOTE]
> 上に示した 2 つの記事は、Log Analytics をはじめて使用される方が迅速にサービスの使用を開始できることを目的としており、コンピューターの構成手順について説明しています。  既にワークスペースがあり、Linux コンピューターに接続したい場合は、既存のワークスペースを選択する (Azure Linux VM の場合) か、ワークスペース ID とキーをコピーしてスクリプトに渡します (Azure の外側でホストされているコンピューターの場合)。  

OMS エージェント for Linux をインストールする前に、Log Analytics ワークスペースのワークスペース ID とキーが必要です。  

1. Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインします。 
2. Azure Portal で、左下隅にある **[その他のサービス]** をクリックします。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics]** を選択します。
3. Log Analytics ワークスペースの一覧で、レポートの送信先にするコンピューターを選択します。
3. **[詳細設定]** を選択します。<br><br> ![Log Analytics の詳細設定](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. **[接続されたソース]**、**[Linux サーバー]** の順に選択します。   
5. **[ワークスペース ID]** と **[主キー]** の右側に値が表示されます。 両方をコピーしてお使いのエディターに貼り付けます。  
6. 最新の [OMS Agent for Linux (x64)](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x64.sh) または [OMS Agent for Linux x86](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.0-45/omsagent-1.4.0-45.universal.x86.sh) をGitHub からダウンロードします。  
7. 該当するバンドル (x86 または x64) を、scp/sftp を使用して Linux コンピューターに転送します。
8. `--install` または `--upgrade` 引数を使用して、バンドルをインストールします。 

    > [!NOTE]
    > 既存のパッケージがインストールされている場合 (Linux 用の System Center Operations Manager エージェントが既にインストールされている場合など) は、`--upgrade` 引数を使用します。 インストール中に Operations Management Suite に接続するには、`-w <WorkspaceID>` および `-s <Shared Key>` パラメーターを指定します。


#### <a name="to-install-and-onboard-directly"></a>直接インストールしてオンボードする場合
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>エージェント パッケージをアップグレードするには
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>インストールして、米国政府クラウド内のワークスペースにオンボードする場合
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>プロキシ サーバーまたは OMS ゲートウェイで使用するためのエージェントの構成
OMS エージェント for Linux では、HTTPS プロトコルを使用する、プロキシ サーバーまたは OMS ゲートウェイを介した Log Analytics サービスとの通信がサポートされています。  匿名と基本の両方の認証 (ユーザー名/パスワード) がサポートされています。  

### <a name="proxy-configuration"></a>[プロキシ構成]
プロキシ構成の値には次の構文があります。

`[protocol://][user:password@]proxyhost[:port]`

プロパティ|Description
-|-
プロトコル|https
user|プロキシ認証のオプションのユーザー名
パスワード|プロキシ認証のオプションのパスワード
proxyhost|プロキシ サーバー/OMS ゲートウェイのアドレスまたは FQDN
ポート|プロキシ サーバー/OMS ゲートウェイのオプションのポート番号

次に例を示します。`https://user01:password@proxy01.contoso.com:30443`

プロキシ サーバーは、インストール中、またはインストール後に proxy.conf 構成ファイルを変更して指定することができます。   

### <a name="specify-proxy-configuration-during-installation"></a>インストール中にプロキシ構成を指定する
omsagent インストール バンドルの `-p` または `--proxy` 引数で、使用するプロキシ構成を指定します。 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>ファイルでプロキシ構成を定義する
プロキシ構成は、`/etc/opt/microsoft/omsagent/proxy.conf` および `/etc/opt/microsoft/omsagent/conf/proxy.conf ` ファイルで設定できます。 これらのファイルは直接作成または編集できますが、そのアクセス許可を更新して、ファイルの読み取りアクセス許可を omiuser ユーザーに付与する必要があります。 For example:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>プロキシ構成の削除
以前に定義されたプロキシ構成を削除し、直接接続に戻すには、次のように proxy.conf ファイルを削除します。
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-log-analytics"></a>Log Analytics へのオンボード
ワークスペース ID とキーがバンドルのインストール時に指定されなかった場合、エージェントを後で Log Analytics に登録する必要があります。

### <a name="onboarding-using-the-command-line"></a>コマンド ラインを使用したオンボード
ワークスペースの ID とキーを指定して、omsadmin.sh コマンドを実行します。 このコマンドはルートとして実行 (sudo 昇格を使用) する必要があります。
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="register-using-a-file"></a>ファイルを使用した登録
1.  ファイル `/etc/omsagent-onboard.conf` を作成します。 このファイルはルートが読み取り/書き込み可能である必要があります。
`sudo vi /etc/omsagent-onboard.conf`
2.  ファイルに、ワークスペース ID と共有キーを示す次の行を挿入します。

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  次のコマンドを実行して Log Analytics に登録します。`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  正常にオンボードされると、ファイルは削除されます。

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>OMS Agent for Linux から System Center Operations Manager にレポートできるようにする
System Center Operations Manager 管理グループにレポートするように OMS Agent for Linux を構成するには、次の手順を実行します。  

1. `/etc/opt/omi/conf/omiserver.conf`
2. **httpsport=** で始まる行にポート 1270 が定義されていることを確認します  (例: `httpsport=1270`)。
3. `sudo /opt/omi/bin/service_control restart` で OMI サーバーを再起動します。

## <a name="agent-logs"></a>エージェントのログ
OMS Agent for Linux のログは `/var/opt/microsoft/omsagent/<workspace id>/log/` にあります。omsconfig (エージェント構成) プログラムのログは `/var/opt/microsoft/omsconfig/log/` にあります。OMI および SCX コンポーネント (パフォーマンス メトリック データを提供) のログは `/var/opt/omi/log/ and /var/opt/microsoft/scx/log` にあります。

### <a name="log-rotation-configuration"></a>ログ ローテーション構成##
omsagent のログ ローテーション構成は `/etc/logrotate.d/omsagent-<workspace id>` で確認できます。

既定の設定は次のとおりです。 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>OMS Agent for Linux のアンインストール
エージェント パッケージは、`--purge` 引数を指定してバンドル .sh ファイルを実行することでアンインストールできます。この操作を実行すると、エージェントとその構成がコンピューターから完全に削除されます。   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>問題: プロキシ経由で Log Analytics に接続できない

#### <a name="probable-causes"></a>考えられる原因
* オンボード中に指定されたプロキシが正しくありません
* Log Analytics と Azure Automation サービスのエンドポイントが、データセンターの許可リストに載っていません 

#### <a name="resolutions"></a>解決策
1. オプション `-v` を有効にして以下のコマンドを使用して、OMS エージェント for Linux で Log Analytics サービスに再オンボードします。 そうすると、プロキシ経由で OMS サービスに接続しているエージェントで詳細出力ができるようになります。 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. 「[プロキシ サーバーまたは OMS ゲートウェイで使用するためのエージェントの構成](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway)」を参照し、プロキシ サーバー経由で通信するようにエージェントを正しく構成したことを確認します。    
* 以下の Log Analytics サービス エンドポイントが許可リストに載っていることを再確認します。

    |エージェントのリソース| ポート |  
    |------|---------|  
    |*.ods.opinsights.azure.com | ポート 443|   
    |*.oms.opinsights.azure.com | ポート 443|   
    |ods.systemcenteradvisor.com | ポート 443|   
    |*.blob.core.windows.net/ | ポート 443|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>問題: オンボードしようとすると 403 エラーが発生する

#### <a name="probable-causes"></a>考えられる原因
* Linux サーバーの日付と時刻が正しくありません 
* 使用されているワークスペース ID とワークスペース キーが正しくありません

#### <a name="resolution"></a>解決策

1. date コマンドを使用して、Linux サーバーの時刻を確認します。 時刻が現在の時刻より 15 分後または前である場合、オンボードは失敗します。 これを修正するには、Linux サーバーの日付やタイムゾーンを更新します。 
2. 最新バージョンの OMS Agent for Linux をインストールしたことを確認します。  最新バージョンでは、時間の差によってオンボードが失敗する場合に通知するようになりました。
3. このトピックの前述のインストール手順に従って、正しいワークスペース ID とワークスペース キーを使用して再オンボードします。

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>問題: オンボードの直後にログ ファイルに 500 および 404 エラーが表示される
これは、Linux データを Log Analytics ワークスペースに最初にアップロードするときに発生する既知の問題です。 送信されているデータやサービス エクスペリエンスには影響しません。

### <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>問題: Azure Portal にデータが表示されない

#### <a name="probable-causes"></a>考えられる原因

- Log Analytics サービスへのオンボードが失敗しました
- Log Analytics サービスへの接続がブロックされています
- OMS Agent for Linux のデータがバックアップされています

#### <a name="resolutions"></a>解決策
1. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` というファイルが存在するかどうかを確認し、Log Analytics サービスのオンボードに成功したかどうかを確認します。
2. `omsadmin.sh` コマンドライン命令を使用して再オンボードします。
3. プロキシを使用する場合は、上記のプロキシの解決手順を参照してください。
4. 場合によっては、OMS エージェント for Linux がサービスと通信できないときに、エージェントのデータが最大バッファー サイズ (50 MB) までキューイングされます。 `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]` コマンドを実行して、OMS Agent for Linux を再起動する必要があります。 

    >[!NOTE]
    >この問題は、エージェント バージョン 1.1.0-28 以降で修正されます。

### <a name="issue-omsagent-creates-excessive-number-of-user-process-on-computer-and-never-terminates-them"></a>問題: OMSAgent によって過剰な数のユーザー プロセスがコンピューター上に作成され、いつまでも終了しない
Linux VM の管理をサポートする管理ソリューションを有効にすると、Linux エージェントで多数のプロセスが開始されます。 しかし、既知の問題により、そのプロセスが終了する前に別のプロセスが開始されました。 

#### <a name="resolutions"></a>解決策
OMSAgent によって生成されるユーザー プロセスの数を変更するには、omsadmin.sh を使用してエージェントを構成します。既定で生成されるプロセスの数は 75 です。制限を変更する前に、まず次のコマンドを実行して、現在実行されている OMSAgent プロセスの数を確認します。`ps aux | grep -E '^omsagent' | wc -l`  
次のコマンドを実行すると、現在どのような制限が設定されているかを確認できます。`cat /etc/security/limits.conf | grep -E '^omsagent'`

以下のコマンドを使用して、カスタムのプロセス制限を構成するか、プロセス制限の設定を既定値に戻します。

1. OMSAgent のプロセス制限を設定する場合: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -n <specific number limit>`<br>設定できる制限の最小値は 5 です。  

2. OMSAgent のプロセス制限を既定値に戻す場合: `sudo /opt/microsoft/omsagent/bin/omsadmin.sh -N`

次のコマンドを実行して、新しい設定が適用されたことを確認します。`cat /etc/security/limits.conf | grep -E '^omsagent'`  新しい設定が適用されていない場合は、設定したプロセス制限の値が小さすぎる可能性があります。  
