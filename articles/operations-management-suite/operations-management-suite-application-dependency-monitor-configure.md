<properties
   pageTitle="Operations Management Suite (OMS) での Application Dependency Monitor (ADM) の構成 | Microsoft Azure"
   description="Application Dependency Monitor (ADM) は、Windows や Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする Operations Management Suite (OMS) ソリューションです。  この記事では、ADM を環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。"
   services="operations-management-suite"
   documentationCenter=""
   authors="daseidma"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operations-management-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2016"
   ms.author="daseidma;bwren" />


# <a name="configuring-application-dependency-monitor-solution-in-operations-management-suite-(oms)"></a>Operations Management Suite (OMS) での Application Dependency Monitor ソリューションの構成
![Alert Management icon](media/operations-management-suite-application-dependency-monitor/icon.png) Application Dependency Monitor (ADM) は、Windows や Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すると、サーバーを重要なサービスを提供する相互接続されたシステムとして表示すできます。  Application Dependency Monitor は、TCP 接続アーキテクチャ全体のサーバー、プロセス、ポート間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、Application Dependency Monitor の構成とエージェントのオンボードの詳細について説明します。  ADM の使用方法については、「[Operations Management Suite (OMS) での Application Dependency Monitor ソリューションの使用](operations-management-suite-application-dependency-monitor.md)」をご覧ください。

>[AZURE.NOTE]Application Dependency Monitor は、現在プライベート プレビューになっています。  ADM プライベート プレビューへのアクセスは、[https://aka.ms/getadm](https://aka.ms/getadm) から要求できます。
>
>プライベート プレビューになっている間は、すべての OMS アカウントが ADM に無制限にアクセスできます。  ADM ノードは無料ですが、AdmComputer_CL や AdmProcess_CL タイプの Log Analytics データは他のソリューションと同様に課金されます。
>
>ADM がパブリック プレビューになった後は、OMS 価格プランに登録している Insight & Analytics の無料ユーザーと有料のユーザーしか使用できなくなります。  Free レベルのアカウントは、5 つの ADM ノードに制限されます。  プライベート プレビューに参加していて、ADM がパブリック プレビューになったときに OMS 価格プランに登録していない場合は、その時点で ADM を使用できなくなります。 



## <a name="connected-sources"></a>接続されたソース
次の表は、ADM ソリューションの接続先としてサポートされているソースとその説明です。

| 接続されているソース | サポートされています | 説明 |
|:--|:--|:--|
| [Windows エージェント](../log-analytics/log-analytics-windows-agents.md) | はい | ADM は、Windows エージェント コンピューターからのデータを分析して収集します。  <br><br>OMS エージェントに加えて、Windows エージェントには Microsoft Dependency Agent が必要です。  サポートされる OS バージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| [Linux エージェント](../log-analytics/log-analytics-linux-agents.md) | はい | ADM は、Linux エージェント コンピューターからのデータを分析して収集します。  <br><br>OMS エージェントに加えて、Linux エージェントには Microsoft Dependency Agent が必要です。  サポートされる OS バージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」をご覧ください。 |
| [SCOM 管理グループ](../log-analytics/log-analytics-om-agents.md) | はい | ADM は、接続された SCOM 管理グループ内の Windows と Linux のエージェントからのデータを分析して収集します。 <br><br>SCOM エージェント コンピューターを OMS に直接接続する必要はありません。 データは管理グループから OMS リポジトリに直接送信されます。|
| [Azure Storage アカウント](../log-analytics/log-analytics-azure-storage.md) | いいえ | ADM はエージェント コンピューターからデータを収集するため、Azure Storage から収集するデータはありません。 |

ADM は 64 ビット プラットフォームのみをサポートしています。

Windows では、SCOM と OMS の両方で Microsoft Monitoring Agent (MMA) が使用され、監視データを収集して送信します。  (このエージェントは、コンテキストに応じて、SCOM エージェント、OMS エージェント、MMA、または直接エージェントと呼ばれます)。SCOM と OMS では、すぐに使用できるさまざまなバージョンの MMA を提供していますが、これらのバージョンは SCOM、OMS、または両方にレポートすることができます。  Linux では、OMS Agent for Linux が監視データを収集して OMS に送信します。  ADM は、OMS 直接エージェントを含むサーバー、または SCOM 管理グループによって OMS にアタッチされているサーバーで使用できます。  このドキュメントでは、これらのエージェントのすべて (Linux または Windows で SCOM 管理グループに接続しているか、OMS に直接接続しているかに関係なく) を「OMS エージェント」と呼びます。ただし、エージェントの特定のデプロイ名がコンテキストで必要な場合を除きます。

ADM エージェントがデータを送信することはないため、ファイアウォールやポートを変更する必要はありません。  ADM のデータは、常に OMS エージェントによって、直接または OMS ゲートウェイ経由で OMS に送信されます。

![ADM エージェント](media/operations-management-suite-application-dependency-monitor/agents.png)

OMS に接続している管理グループの SCOM ユーザーの場合。

- SCOM エージェントがインターネット経由で OMS にアクセスできる場合は、追加の構成は必要ありません。  
- SCOM エージェントがインターネット経由で OMS にアクセスできない場合は、SCOM で動作するように OMS ゲートウェイを構成する必要があります。
  
OMS 直接エージェントを使用している場合は、OMS エージェント自体を OMS または OMS ゲートウェイに接続するように構成する必要があります。  OMS ゲートウェイは [https://www.microsoft.com/en-us/download/details.aspx?id=52666](https://www.microsoft.com/en-us/download/details.aspx?id=52666) からダウンロードできます。


### <a name="avoiding-duplicate-data"></a>重複データの回避

SCOM ユーザーの場合、SCOM エージェントを OMS と直接通信するように構成しないでください。データが 2 回報告される可能性があります。  この結果、ADM ではコンピューター一覧にコンピューターが 2 回表示されます。

OMS の構成は、次の場所のいずれか一方で行ってください。

- 管理対象コンピューターの SCOM コンソールの Operations Management Suite パネル
- MMA プロパティの Azure Operational Insights 構成

同じワークスペースで両方の構成を使用すると、データの重複が発生します。 別のワークスペースで両方とも使用すると、構成の競合 (一方は ADM ソリューションが有効、他方は無効) が発生し、データが ADM に完全に流れなくなる可能性があります。  

コンピューター自体が SCOM コンソールの OMS 構成で指定されていない場合でも、「Windows Server インスタンス グループ」などのインスタンス グループがアクティブになっていれば、SCOM 経由で OMS 構成を受信できる場合があります。



## <a name="management-packs"></a>管理パック
ADM が OMS ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Microsoft Monitoring Agent に送信されます。  SCOM エージェントを[接続された管理グループ](../log-analytics/log-analytics-om-agents.md)で使用している場合は、ADM 管理パックが SCOM からデプロイされます。  エージェントが直接接続されている場合は、OMS によって管理パックが配布されます。

この管理パックの名前は、Microsoft.IntelligencePacks.ApplicationDependencyMonitor* で、*%Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\* に書き込まれます。  管理パックで使用されるデータ ソースは、*%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll* です。



## <a name="configuration"></a>構成
Windows コンピューターと Linux コンピューターのほかにエージェントをインストールし、OMS に接続している場合は、Dependency Agent インストーラーを ADM ソリューションからダウンロードし、root または Admin として各管理対象サーバーにインストールする必要があります。  ADM エージェントが OMS にレポートするサーバーにインストールされると、ADM の依存関係マップが 10 分以内に表示されます。  何か問題がある場合は、メール ([oms-adm-support@microsoft.com](mailto:oms-adm-support@microsoft.com)) でお問い合わせください。


### <a name="migrating-from-bluestripe-factfinder"></a>BlueStripe FactFinder からの移行
Application Dependency Monitor は、BlueStripe テクノロジを段階的に OMS に移行します。 FactFinder は、既存の顧客に対してまだサポートされていますが、個別に購入できなくなります。  この Dependency Agent のプレビュー バージョンのみ、OMS と通信できます。  現在 FactFinder ユーザーである場合は、FactFinder の管理対象外の ADM のテスト サーバー セットを指定してください。 

### <a name="download-the-dependency-agent"></a>Dependency Agent のダウンロード
コンピューターと OMS 間の接続を提供する Microsoft Management Agent (MMA) と OMS Linux Agent に加えて、Application Dependency Monitor によって分析されるすべてのコンピューターに Dependency Agent をインストールする必要があります。  Linux では、Dependency Agent の前に OMS Agent for Linux をインストールする必要があります。 

![Application Dependency Monitor タイル](media/operations-management-suite-application-dependency-monitor/tile.png)

Dependency Agent をダウンロードするには、**[Application Dependency Monitor]** タイルの **[ソリューションの構成]** をクリックして、**[Dependency Agent]** ブレードを開きます。  Dependency Agent ブレードには、Windows と Linux エージェントのリンクがあります。 適切なリンクをクリックしてエージェントをダウンロードします。 エージェントを異なるシステムにインストールする方法の詳細については、次のセクションをご覧ください。

### <a name="install-the-dependency-agent"></a>Dependency Agent のインストール

#### <a name="microsoft-windows"></a>Microsoft Windows
エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

Dependency Agent は、ADM-Agent-Windows.exe によって Windows コンピューターにインストールされます。 オプションを指定せずにこの実行可能ファイルを実行すると、ウィザードが起動し、ユーザーはこれに従って対話形式でインストールを実行できます。  

各 Windows コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.  OMS エージェントが OMS にコンピューターを直接接続する手順に従ってインストールされていることを確認します。
2.  Windows エージェントをダウンロードし、次のコマンドを使用して実行します。<br>*ADM-Agent-Windows.exe*
3.  ウィザードに従ってエージェントをインストールします。
4.  Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Windows エージェントのログ ディレクトリは、*C:\Program Files\BlueStripe\Collector\logs* にあります。 

Dependency Agent for Windows は、管理者によってコントロール パネルからアンインストールできます。


#### <a name="linux"></a>Linux
エージェントをインストールまたは構成するには、ルート アクセスが必要です。

Dependency Agent は、ADM-Agent-Linux64.bin (自己解凍バイナリファイルを含むシェル スクリプト) によって Linux コンピューターにインストールされます。 sh を使用してファイルを実行するか、実行アクセス許可をファイルに追加します。
 
各 Linux コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.  OMS エージェントが [Linux コンピューターからのデータを分析して収集する手順に従ってインストールされていることを確認します。これは、Linux Dependency Agent の前にインストールされている必要があります](https://technet.microsoft.com/library/mt622052.aspx)。
2.  次のコマンドを使用して、Linux Dependency Agent をルートとしてインストールします。<br>*sh ADM-Agent-Linux64.bin*
3.  Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、*/var/opt/microsoft/dependency-agent/log* にあります。

### <a name="uninstalling-the-dependency-agent-on-linux"></a>Linux の Dependency Agent をアンインストールする
Linux から Dependency Agent を完全にアンインストールするには、エージェント自体と、自動的にエージェントにインストールされたプロキシを削除する必要があります。  次の 1 つのコマンドで、両方ともアンインストールできます。

    rpm -e dependency-agent dependency-agent-connector


### <a name="installing-from-a-command-line"></a>コマンドラインからインストールする
前のセクションでは、規定のオプションを使用して Dependency Monitor Agent をインストールする方法について説明しました。  以下のセクションでは、カスタム オプションを使用してコマンド ラインからエージェントをインストールする方法について説明します。

#### <a name="windows"></a>Windows
以下の表のオプションを使用して、コマンド ラインからインストールを実行します。 インストール フラグの一覧を表示するには、 次のように /? フラグを使ってインストーラーを実行します。

    ADM-Agent-Windows.exe /?

| フラグ | 説明 |
|:--|:--|
| /S | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |

Windows Dependency Agent のファイルは、既定で *C:\Program Files\BlueStripe\Collector* に保存されます。


#### <a name="linux"></a>Linux
以下の表のオプションを使用して、インストールを実行します。 インストール フラグの一覧を表示するには、次のように -help フラグを使ってインストール プログラムを実行します。

    ADM-Agent-Linux64.bin -help

| フラグ  説明
|:--|:--|
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールされません。 |

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | 場所 |
|:--|:--|
| コア ファイル | /usr/lib/bluestripe-collector |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /sbin/bluestripe-collector<br>/sbin/bluestripe-collector-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |



## <a name="troubleshooting"></a>トラブルシューティング
Application Dependency Monitor で問題が発生した場合は、次の情報を使用して複数のコンポーネントからトラブルシューティング情報を収集できます。

### <a name="windows-agents"></a>Windows エージェント

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Dependency Agent からトラブルシューティング データを生成するには、管理者としてコマンド プロンプトを開き、次のコマンドを使用して CollectBluestripeData.vbs スクリプトを実行します。  --help フラグを追加すると、追加のオプションを表示できます。

    cd C:\Program Files\Bluestripe\Collector\scripts
    cscript CollectDependencyData.vbs

サポート データ パッケージが現在のユーザーの %USERPROFILE% ディレクトリに保存されます。  --file <filename> オプションを使用すると、これを別の場所に保存できます。

#### <a name="microsoft-dependency-agent-management-pack-for-mma"></a>MMA 用の Microsoft Dependency Agent 管理パック
Dependency Agent 管理パックは、Microsoft Management Agent 内で実行します。  Dependency Agent からデータを受信し、ADM クラウド サービスに転送します。
  
管理パックがダウンロードされたことを確認するには、次の手順を実行します。

1.  C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs で、Microsoft.IntelligencePacks.ApplicationDependencyMonitor.mp という名前のファイルを探します。  
2.  ファイルが見つからず、エージェントが SCOM 管理グループに接続されている場合は、オペレーション コンソールの [管理] ワークスペースで管理パックをチェックし、SCOM にファイルがインポートされていることを確認します。

ADM MP は、Operations Manager Windows イベント ログにイベントを書き込みます。  このログはシステム ログ ソリューション経由で [OMS 内で検索](../log-analytics/log-analytics-log-searches.md)でき、どのログ ファイルをアップロードするかを構成できます。  デバッグ イベントが有効になっている場合は、イベント ソース *AdmProxy* とともにアプリケーション イベント ログに書き込まれます。

#### <a name="microsoft-monitoring-agent"></a>Microsoft Monitoring Agent
診断トレースを収集するには、管理者としてコマンド プロンプトを開き、次のコマンドを実行します。 

    cd \Program Files\Microsoft Monitoring Agent\Agent\Tools
    net stop healthservice 
    StartTracing.cmd ERR
    net start healthservice

トレースは、c:\Windows\Logs\OpsMgrTrace に書き込まれます。  StopTracing.cmd でトレースを停止できます。


### <a name="linux-agents"></a>Linux エージェント

#### <a name="microsoft-dependency-agent"></a>Microsoft Dependency Agent
Dependency Agent からトラブルシューティング データを生成するには、sudo または root 権限を持つアカウントでログインし、次のコマンドを実行します。  --help フラグを追加すると、追加のオプションを表示できます。

    /usr/lib/bluestripe-collector/scripts/collect-dependency-agent-data.sh

サポート データ パッケージがエージェントのインストール ディレクトリの下の /var/opt/microsoft/dependency-agent/log (ルートの場合)、またはスクリプトを実行しているユーザーのホーム ディレクトリ (ルート以外の場合) に保存されます。  --file <filename> オプションを使用すると、これを別の場所に保存できます。

#### <a name="microsoft-dependency-agent-fluentd-plug-in-for-linux"></a>Linux 用の Microsoftt Dependency Agent Fluentd プラグイン
Dependency Agent Fluentd プラグインは、OMS Linux Agent 内で実行します。  Dependency Agent からデータを受信し、ADM クラウド サービスに転送します。  

次の 2 つのファイルにログが書き込まれます。

- /var/opt/microsoft/omsagent/log/omsagent.log
- /var/log/messages

#### <a name="oms-agent-for-linux"></a>OMS Agent for Linux
Linux サーバーを OMS に接続する際のトラブルシューティング リソースは次の場所にあります。[https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md) 

OMS Agent for Linux のログは次の場所ににあります。*var/opt/microsoft/omsagent/log/*  

omsconfig (エージェント構成) のログは次の場所にあります。*/var/opt/microsoft/omsconfig/log/*
 
パフォーマンス メトリック データを提供する OMI および SCX コンポーネントのログは次の場所にあります。*/var/opt/omi/log/* と */var/opt/microsoft/scx/log*


## <a name="data-collection"></a>データ収集
システムの依存関係の複雑さに応じて、各エージェントは 1 日あたり約 25 MB を送信すると予想されます。  ADM 依存関係のデータは、15 秒ごとに各エージェントによって送信されます。  

ADM エージェントは、通常、システム メモリの 0.1% とシステム CPU の 0.1% を消費します。


## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
次のセクションでは、Dependency Agent でサポートされているオペレーティング システムを一覧します。   32 ビット アーキテクチャは、どのオペレーティング システムでもサポートされていません。

### <a name="windows-server"></a>Windows Server
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows デスクトップ
- 注: Windows 10 はまだサポートされていません
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux,-centos-linux-and-oracle-linux-(with-rhel-kernel)"></a>Red Hat Enterprise Linux、CentOS Linux と Oracle Linux (RHEL カーネル搭載)
- 既定と SMP Linux のカーネル リリースのみがサポートされています。
- PAE、Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が「2.6.16.21-0.8-xen」であるシステムはサポートされていません。
- カスタム カーネル (標準カーネルの再コンパイルを含む) は、サポートされていません。
- Centos Plus カーネルは、サポートされていません。
- Oracle Unbreakable Kernel (UEK) については、以下の別のセクションで説明します。


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |

#### <a name="red-hat-linux-6"></a>Red Hat Linux 6
| OS バージョン | カーネル バージョン |
|:--|:--|
| 6.0 | 2.6.32-71 |
| 6.1 | 2.6.32-131 |
| 6.2 | 2.6.32-220 |
| 6.3 | 2.6.32-279 |
| 6.4. | 2.6.32-358 |
| 6.5 | 2.6.32-431 |
| 6.6 | 2.6.32-504 |
| 6.7 | 2.6.32-573 |
| 6.8 | 2.6.32-642 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS バージョン | カーネル バージョン |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411 |

#### <a name="oracle-enterprise-linux-w/-unbreakable-kernel-(uek)"></a>Unbreakable Kernel (UEK) を搭載した Oracle Enterprise Linux

#### <a name="oracle-linux-6"></a>Oracle Linux 6
| OS バージョン | カーネル バージョン
|:--|:--|
| 6.2 | Oracle 2.6.32-300 (UEK R1) |
| 6.3 | Oracle 2.6.39-200 (UEK R2) |
| 6.4. | Oracle 2.6.39-400 (UEK R2) |
| 6.5 | Oracle 2.6.39-400 (UEK R2 i386) |
| 6.6 | Oracle 2.6.39-400 (UEK R2 i386) |

#### <a name="oracle-linux-5"></a>Oracle Linux 5

| OS バージョン | カーネル バージョン
|:--|:--|
| 5.8 | Oracle 2.6.32-300 (UEK R1) |
| 5.9 | Oracle 2.6.39-300 (UEK R2) |
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS バージョン | カーネル バージョン
|:--|:--|
| 11 | 2.6.27 |
| 11 SP1 | 2.6.32 |
| 11 SP2 | 3.0.13 |
| 11 SP3 | 3.0.76 |
| 11 SP4 | 3.0.101 |

#### <a name="suse-linux-10"></a>SUSE Linux 10
| OS バージョン | カーネル バージョン
|:--|:--|
| 10 SP4 | 2.6.16.60 |

## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様による Application Dependency Monitor サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用することで、Application Dependency Monitor サービスの品質、セキュリティ、整合性を提供し続け、向上していきます。 データには、オペレーティング システムとバージョンのような、ソフトウェアの構成に関する情報が含まれています。また、正確で効果的なトラブルシューティングの機能を提供するために、IP アドレス、DNS 名、およびワークステーション名も含まれています。 名前や住所などの連絡先情報は収集されません。

データの収集と使用状況の詳細については、[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)をご覧ください。



## <a name="next-steps"></a>次のステップ
- デプロイと構成が完了したら、[Application Dependency Monitor を使用する](operations-management-suite-application-dependency-monitor.md)方法をご覧ください。



<!--HONumber=Oct16_HO2-->


