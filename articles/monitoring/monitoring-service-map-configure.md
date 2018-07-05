---
title: Azure での Service Map の構成 | Microsoft Docs
description: Service Map は、Windows および Linux システム上のアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップする、Azure のソリューションです。 この記事では、サービス マップを環境に展開して、さまざまなシナリオで使用する場合の詳細について説明します。
services: monitoring
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: d3d66b45-9874-4aad-9c00-124734944b2e
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/22/2018
ms.author: daseidma;bwren
ms.openlocfilehash: 872d5f05e4d607c9445d1af5cc9b9cb984c19e11
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752576"
---
# <a name="configure-service-map-in-azure"></a>Azure で Service Map を構成する
サービス マップは、Windows および Linux システムのアプリケーション コンポーネントを自動的に検出し、サービス間の通信をマップします。 これを使用すれば、サーバーを重要なサービスを提供する相互接続されたシステムとして表示することができます。 Service Map は、TCP 接続アーキテクチャ全体におけるサーバー、プロセス、ポートの間の接続を表示します。エージェントのインストール以外の構成は必要ありません。

この記事では、サービス マップの構成とエージェントのオンボードの詳細について説明します。 Service Map の使用方法については、「[Operations Management Suite の Service Map ソリューションの使用]( monitoring-service-map.md)」をご覧ください。

## <a name="dependency-agent-downloads"></a>Dependency Agent のダウンロード
| ファイル | OS | バージョン | SHA-256 |
|:--|:--|:--|:--|
| [InstallDependencyAgent-Windows.exe](https://aka.ms/dependencyagentwindows) | Windows | 9.5.0 | 8B8FE0F6B0A9F589C4B7B52945C2C25DF008058EB4D4866DC45EE2485062C9D7 |
| [InstallDependencyAgent-Linux64.bin](https://aka.ms/dependencyagentlinux) | Linux | 9.5.1 | 09D56EF43703A350FF586B774900E1F48E72FE3671144B5C99BB1A494C201E9E |


## <a name="connected-sources"></a>接続先ソース
サービス マップは、Microsoft Dependency Agent からデータを取得します。 Dependency Agent は、Log Analytics への接続に関して OMS エージェントに依存しています。 つまり、サーバーには OMS エージェントを先にインストールして構成する必要があり、Dependency Agent はその操作の後でインストールできます。 次の表では、Service Map ソリューションでサポートされている接続先ソースについて説明します。

| 接続先ソース | サポートされています | 説明 |
|:--|:--|:--|
| Windows エージェント | [はい] | サービス マップは、Windows エージェント コンピューターからのデータを分析して収集します。 <br><br>[OMS エージェント](../log-analytics/log-analytics-windows-agent.md)に加えて、Windows エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」を参照してください。 |
| Linux エージェント | [はい] | サービス マップは、Linux エージェント コンピューターからのデータを分析して収集します。 <br><br>[OMS エージェント](../log-analytics/log-analytics-linux-agents.md)に加えて、Linux エージェントには Microsoft Dependency Agent が必要です。 オペレーティング システムのバージョンの一覧については、「[サポートされているオペレーティング システム](#supported-operating-systems)」を参照してください。 |
| System Center Operations Manager 管理グループ | [はい] | Service Map は、接続された [System Center Operations Manager 管理グループ](../log-analytics/log-analytics-om-agents.md)内の Windows エージェントと Linux エージェントからのデータを分析して収集します。 <br><br>System Center Operations Manager エージェント コンピューターから Log Analytics への直接接続が必要です。 データは管理グループから Log Analytics ワークスペースに転送されます。|
| Azure ストレージ アカウント | いいえ  | Service Map はエージェント コンピューターからデータを収集するため、Azure Storage から収集するデータはありません。 |

Service Map でサポートされるのは 64 ビット プラットフォームのみです。

Windows では、監視データの収集と送信のために System Center Operations Manager と Log Analytics の両方で Microsoft Monitoring Agent (MMA) が使用されます。 (このエージェントは、状況に応じて、System Center Operations Manager エージェント、OMS エージェント、Log Analytics エージェント、MMA、またはダイレクト エージェントと呼ばれます)。System Center Operations Manager と Log Analytics では、MMA のすぐに使用できるバージョンが異なります。 これらのバージョンはそれぞれ、System Center Operations Manager、Log Analytics、またはその両方にレポートできます。  

Linux では、OMS エージェント for Linux が監視データを収集して Log Analytics に送信します。 Service Map は、OMS ダイレクト エージェントがインストールされているサーバーまたは System Center Operations Manager 管理グループ経由で Log Analytics にアタッチされているサーバーで使用できます。  

Linux 用であるか Windows 用であるか、System Center Operations Manager 管理グループに接続されているか Log Analytics に直接接続されているかに関係なく、この記事ではすべてのエージェントを "*OMS エージェント*" と呼びます。 状況に応じて必要な場合にのみ、エージェントの特定のデプロイ名を使用します。

サービス マップ エージェントがデータを送信することはないため、ファイアウォールやポートを変更する必要はありません。 Service Map 内のデータは、常に OMS エージェントによって、直接または OMS ゲートウェイを使用して Log Analytics に送信されます。

![Service Map エージェント](media/monitoring-service-map/agents.png)

Log Analytics に管理グループが接続されている System Center Operations Manager ユーザーである場合は次のとおりです。

- System Center Operations Manager エージェントがインターネット経由で Log Analytics にアクセスできる場合は、追加の構成は必要ありません。  
- System Center Operations Manager エージェントがインターネット経由で Log Analytics にアクセスできない場合は、System Center Operations Manager を操作するために OMS ゲートウェイを構成する必要があります。
  
OMS のダイレクト エージェントを使用している場合は、OMS エージェント自体が Log Analytics または OMS ゲートウェイに接続するように構成する必要があります。 OMS ゲートウェイは、[Microsoft ダウンロード センター](https://www.microsoft.com/download/details.aspx?id=52666)からダウンロードできます。 OMS ゲートウェイをデプロイおよび構成する方法の詳細については、「[インターネットにアクセスできないコンピューターを OMS ゲートウェイを使って接続する](../log-analytics/log-analytics-oms-gateway.md)」を参照してください。  

### <a name="management-packs"></a>管理パック
Service Map が Log Analytics ワークスペースでアクティブになると、300 KB の管理パックがそのワークスペース内のすべての Windows サーバーに送信されます。 System Center Operations Manager エージェントを[接続された管理グループ](../log-analytics/log-analytics-om-agents.md)で使用している場合は、Service Map 管理パックが System Center Operations Manager からデプロイされます。 エージェントが直接接続されている場合、管理パックは Log Analytics によって配布されます。

この管理パックは、Microsoft.IntelligencePacks.ApplicationDependencyMonitor という名前で、 %Programfiles%\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs\ に書き込まれます。 管理パックで使用されるデータ ソースは、%Program files%\Microsoft Monitoring Agent\Agent\Health Service State\Resources\<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll です。

## <a name="installation"></a>インストール
### <a name="install-the-dependency-agent-on-microsoft-windows"></a>Microsoft Windows に Dependency Agent をインストールする
エージェントをインストールまたはアンインストールするには、管理者特権が必要です。

Dependency Agent は、InstallDependencyAgent-Windows.exe によって Windows コンピューターにインストールされます。 オプションを指定せずにこの実行可能ファイルを実行すると、ウィザードが起動します。ここで、指示に従って対話形式でインストールを実行できます。  

各 Windows コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.  「[Azure Log Analytics を使用して環境内のコンピューターからデータを収集する](../log-analytics/log-analytics-concept-hybrid.md)」に説明されているいずれかの方法に従って、OMS エージェントをインストールします。
2.  Windows エージェントをダウンロードし、次のコマンドを使用して実行します。 <br>`InstallDependencyAgent-Windows.exe`
3.  ウィザードに従ってエージェントをインストールします。
4.  Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Windows エージェントのログ ディレクトリは、%Programfiles%\Microsoft Dependency Agent\logs にあります。 

#### <a name="windows-command-line"></a>Windows コマンド ライン
次の表のオプションを使用して、コマンドラインからインストールします。 インストール フラグの一覧を表示するには、 次のように /? フラグを使ってインストーラーを実行します。

    InstallDependencyAgent-Windows.exe /?

| フラグ | 説明 |
|:--|:--|
| /? | コマンド ライン オプションの一覧を取得します。 |
| /S | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |

Windows Dependency Agent のファイルは、既定で C:\Program Files\Microsoft Dependency Agent に配置されます。

### <a name="install-the-dependency-agent-on-linux"></a>Linux に Dependency Agent をインストールする
エージェントをインストールまたは構成するには、ルート アクセスが必要です。

Dependency Agent は、InstallDependencyAgent-Linux64.bin (自己解凍バイナリ ファイルを含むシェル スクリプト) によって Linux コンピューターにインストールされます。 sh を使用してファイルを実行するか、実行アクセス許可をファイル自体に追加します。
 
各 Linux コンピューターに Dependency Agent をインストールするには、次の手順を使用します。

1.  「[Azure Log Analytics を使用して環境内のコンピューターからデータを収集する](../log-analytics/log-analytics-concept-hybrid.md)」に説明されているいずれかの方法に従って、OMS エージェントをインストールします。
2.  次のコマンドを使用して、Linux Dependency Agent を root としてインストールします。<br>`sh InstallDependencyAgent-Linux64.bin`
3.  Dependency Agent が起動しない場合は、詳細なエラー情報のログを確認します。 Linux エージェントのログ ディレクトリは、/var/opt/microsoft/dependency-agent/log です。

インストール フラグの一覧を表示するには、次のように -help フラグを使ってインストール プログラムを実行します。

    InstallDependencyAgent-Linux64.bin -help

| フラグ | 説明 |
|:--|:--|
| -help | コマンド ライン オプションの一覧を取得します。 |
| -s | ユーザー プロンプトを表示せずにサイレント インストールを実行します。 |
| --check | アクセス許可とオペレーティング システムを確認しますが、エージェントはインストールしません。 |

Dependency Agent のファイルは、次のディレクトリに保存されます。

| ファイル | リージョン |
|:--|:--|
| コア ファイル | /opt/microsoft/dependency-agent |
| ログ ファイル | /var/opt/microsoft/dependency-agent/log |
| 構成ファイル | /etc/opt/microsoft/dependency-agent/config |
| サービス実行可能ファイル | /opt/microsoft/dependency-agent/bin/microsoft-dependency-agent<br>/opt/microsoft/dependency-agent/bin/microsoft-dependency-agent-manager |
| バイナリ ストレージ ファイル | /var/opt/microsoft/dependency-agent/storage |

## <a name="installation-script-examples"></a>インストール スクリプトの例
Dependency Agent を同時に多数のサーバーに対して簡単にデプロイするには、スクリプトの使用が便利です。 次のスクリプトの例を使用すると、Dependency Agent をダウンロードして Windows または Linux にインストールできます。

### <a name="powershell-script-for-windows"></a>Windows 用 PowerShell スクリプト
```PowerShell
Invoke-WebRequest "https://aka.ms/dependencyagentwindows" -OutFile InstallDependencyAgent-Windows.exe

.\InstallDependencyAgent-Windows.exe /S
```

### <a name="shell-script-for-linux"></a>Linux 用シェル スクリプト
```
wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin
sudo sh InstallDependencyAgent-Linux64.bin -s
```

## <a name="azure-vm-extension"></a>Azure VM 拡張機能
[Azure VM 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features)を使用すると、Azure VM に Dependency Agent を簡単に展開できます。  Azure VM 拡張機能を使用することにより、PowerShell スクリプトを使用して、または、VM の Azure Resource Manager テンプレートから直接、VM に Dependency Agent を展開することができます。  Windows 用 (DependencyAgentWindows) と Linux 用 (DependencyAgentLinux) の拡張機能が用意されています。  Azure VM 拡張機能を使用して展開した場合、エージェントは最新バージョンに自動的に更新されます。

PowerShell を使用して Azure VM 拡張機能を展開するには、次の例を使用してください。

```PowerShell
#
# Deploy the Dependency Agent to every VM in a Resource Group
#

$version = "9.4"
$ExtPublisher = "Microsoft.Azure.Monitoring.DependencyAgent"
$OsExtensionMap = @{ "Windows" = "DependencyAgentWindows"; "Linux" = "DependencyAgentLinux" }
$rmgroup = "<Your Resource Group Here>"

Get-AzureRmVM -ResourceGroupName $rmgroup |
ForEach-Object {
    ""
    $name = $_.Name
    $os = $_.StorageProfile.OsDisk.OsType
    $location = $_.Location
    $vmRmGroup = $_.ResourceGroupName
    "${name}: ${os} (${location})"
    Date -Format o
    $ext = $OsExtensionMap.($os.ToString())
    $result = Set-AzureRmVMExtension -ResourceGroupName $vmRmGroup -VMName $name -Location $location `
    -Publisher $ExtPublisher -ExtensionType $ext -Name "DependencyAgent" -TypeHandlerVersion $version
    $result.IsSuccessStatusCode
}
```

Dependency Agent を各 VM で確実に展開する、さらに簡単な方法は、Azure Resource Manager テンプレートにエージェントを追加することです。  Dependency Agent は OMS Agent に依存し続けるため、[OMSAgent VM 拡張機能](../virtual-machines/extensions/oms-linux.md)を最初に展開する必要があることに注意してください。  テンプレートの *resources* セクションに次の JSON スニペットを追加できます。

```JSON
"type": "Microsoft.Compute/virtualMachines/extensions",
"name": "[concat(parameters('vmName'), '/DependencyAgent')]",
"apiVersion": "2017-03-30",
"location": "[resourceGroup().location]",
"dependsOn": [
"[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
],
"properties": {
    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
    "type": "DependencyAgentWindows",
    "typeHandlerVersion": "9.4",
    "autoUpgradeMinorVersion": true
}

```


## <a name="desired-state-configuration"></a>Desired State Configuration
Desired State Configuration 経由で Dependency Agent をデプロイするには、次のように xPSDesiredStateConfiguration モジュールと簡単なコードを使用できます。

```
configuration ServiceMap {

Import-DscResource -ModuleName xPSDesiredStateConfiguration

$DAPackageLocalPath = "C:\InstallDependencyAgent-Windows.exe"

Node localhost
{ 
    # Download and install the Dependency Agent
    xRemoteFile DAPackage 
    {
        Uri = "https://aka.ms/dependencyagentwindows"
        DestinationPath = $DAPackageLocalPath
    }

    xPackage DA
    {
        Ensure="Present"
        Name = "Dependency Agent"
        Path = $DAPackageLocalPath
        Arguments = '/S'
        ProductId = ""
        InstalledCheckRegKey = "HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Microsoft\Windows\CurrentVersion\Uninstall\DependencyAgent"
        InstalledCheckRegValueName = "DisplayName"
        InstalledCheckRegValueData = "Dependency Agent"
        DependsOn = "[xRemoteFile]DAPackage"
    }
  }
}
```

## <a name="uninstallation"></a>アンインストール
### <a name="uninstall-the-dependency-agent-on-windows"></a>Windows で Dependency Agent をアンインストールする
管理者は、コントロール パネルを使用して Dependency Agent for Windows をアンインストールできます。

また、%Programfiles%\Microsoft Dependency Agent\Uninstall.exe を実行して Dependency Agent をアンインストールすることもできます。

### <a name="uninstall-the-dependency-agent-on-linux"></a>Linux で Dependency Agent をアンインストールする
次のコマンドで、Linux から Dependency Agent をアンインストールすることができます。
<br>RHEL、CentOs、または Oracle:

```
sudo rpm -e dependency-agent
```

Ubuntu:

```
sudo apt -y purge dependency-agent
```
## <a name="troubleshooting"></a>トラブルシューティング
Service Map のインストールまたは実行で問題が発生した場合は、こちらのセクションを参照してください。 それでも問題が解決しない場合は、Microsoft サポートにお問い合わせください。

### <a name="dependency-agent-installation-problems"></a>Dependency Agent のインストールに関する問題
#### <a name="installer-prompts-for-a-reboot"></a>インストーラーが再起動を要求する
*通常*、Dependency Agent がインストールまたはアンインストール時に再起動を要求することはありません。 ただし、まれに、インストールを続行するために Windows Server が再起動を要求することがあります。 これは、依存関係 (通常は Microsoft Visual C++ 再頒布可能ファイル) のあるファイルがロックされているため、再起動が要求される場合に生じます。

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--codenumber-appears"></a>"Unable to install Dependency Agent: Visual Studio Runtime libraries failed to install (code = [code_number]) (Dependency Agent をインストールできません: Visual Studio ランタイム ライブラリが (code = [code_number]) をインストールできませんでした)" というメッセージが表示される

Microsoft Dependency Agent は、Microsoft Visual Studio ランタイム ライブラリ上にビルドされます。 ライブラリのインストール中に問題が発生した場合は、メッセージが表示されます。 

ランタイム ライブラリのインストーラーは、%LOCALAPPDATA%\temp フォルダー内にログを作成します。 このファイルは dd_vcredist_arch_yyyymmddhhmmss.log で、*arch* は "x86" または "amd64" に、*yyyymmddhhmmss* はログが作成された日時 (24 時間形式) になります。 このログでは、インストールをブロックしている問題の詳細が示されます。

最初に[最新のランタイム ライブラリ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)をインストールしておくことをお勧めします。

次の表に、コード番号と推奨される解決策を示します。

| コード | 説明 | 解決策 |
|:--|:--|:--|
| 0x17 | ライブラリのインストーラーは、まだインストールされていない Windows Update を要求しています。 | 最新のライブラリ インストーラー ログを確認してください。<br><br>"Windows8.1-KB2999226-x64.msu" の次の行に "Error 0x80240017: Failed to execute MSU package (エラー 0x80240017: MSU パッケージを実行できませんでした)" と表示される場合は、KB2999226 のインストールに必要な前提条件が満たされていません。 [Windows での汎用の C ランタイム](https://support.microsoft.com/kb/2999226)に関するページの前提条件セクションに記載の手順に従ってください。 前提条件をインストールするためには、Windows Update の実行と再起動が複数回必要になることがあります。<br><br>Microsoft Dependency Agent インストーラーをもう一度実行します。 |

### <a name="post-installation-issues"></a>インストール後の問題
#### <a name="server-doesnt-appear-in-service-map"></a>サーバーが Service Map に表示されない
Dependency Agent のインストールに成功しても、サービス マップ ソリューションにはサーバーが表示されません。
* Dependency Agent は正しくインストールされているのでしょうか?  これについては、サービスがインストールされているか、実行中かを確認することで検証できます。<br><br>
**Windows**: "Microsoft Dependency Agent" というサービスを探します。<br>
**Linux**: "microsoft-dependency-agent" という実行中のプロセスを探します。

* 現在ご利用されているのは、[無料の価格レベルの Operations Management Suite または Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions#offers-and-pricing-tiers) ですか。 無料のプランでは、一意のサービス マップ サーバーを最大 5 台まで使用できます。 以前使用していた 5 台がデータを送信していない場合でも、6 台目以降のサーバーはサービス マップに表示されません。

* サーバーはログとパフォーマンス データを Log Analytics に送信していますか?  ログ検索に移動し、お使いのコンピューターに対して次のクエリを実行します。 

        * Computer="<your computer name here>" | measure count() by Type
        
  結果としてさまざまなイベントを取得しましたか?  そのデータは最近のものですか?  そうであれば、OMS エージェントは正しく動作しており、Log Analytics と通信しています。 そうでなければ、お使いのサーバーの OMS エージェントを確認してください ([Windows 用 OMS エージェントのトラブルシューティング](https://support.microsoft.com/help/3126513/how-to-troubleshoot-monitoring-onboarding-issues)または [Linux 用 OMS エージェントのトラブルシューティング](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/Troubleshooting.md)に関するページを参照)。

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>Service Map にサーバーは表示されるがプロセスが表示されない
サービス マップにサーバーは表示されるがプロセスまたは接続データは表示されないという場合は、Dependency Agent がインストールされて実行されているがカーネル ドライバーがロードされなかったということを意味しています。 

C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log ファイル (Windows) または /var/opt/microsoft/dependency-agent/log/service.log ファイル (Linux) を確認します。 ファイルの最後の行に、カーネルがロードされなかった原因が示されています。 たとえば、カーネルを更新した場合にそのカーネルが Linux でサポートされないことがあります。

## <a name="data-collection"></a>データ収集
システムの依存関係の複雑さに応じて、各エージェントは 1 日あたり約 25 MB を送信すると予想されます。 各エージェントは、Service Map の依存関係データを 15 秒ごとに送信します。  

Dependency Agent は、通常、システム メモリの 0.1%、システム CPU の 0.1% を消費します。

## <a name="supported-azure-regions"></a>サポート対象の Azure リージョン
Service Map は現在、次の Azure リージョンでご利用いただけます。
- 米国東部
- 西ヨーロッパ
- 米国中西部
- 東南アジア


## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム
次のセクションでは、Dependency Agent でサポートされているオペレーティング システムを一覧します。 Service Map では、すべてのオペレーティング システムで 32 ビット アーキテクチャがサポートされていません。

### <a name="windows-server"></a>Windows Server
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1

### <a name="windows-desktop"></a>Windows デスクトップ
- Windows 10
- Windows 8.1
- Windows 8
- Windows 7

### <a name="red-hat-enterprise-linux-centos-linux-and-oracle-linux-with-rhel-kernel"></a>Red Hat Enterprise Linux、CentOS Linux 、および Oracle Linux (RHEL カーネル搭載)
- 既定と SMP Linux のカーネル リリースのみがサポートされています。
- PAE、Xen などの非標準のカーネル リリースは、どの Linux ディストリビューションでもサポートされていません。 たとえば、リリースの文字列が「2.6.16.21-0.8-xen」であるシステムはサポートされていません。
- カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。
- CentOS Plus カーネルはサポートされていません。
- Oracle Unbreakable Enterprise Kernel (UEK) については、この記事の後のセクションで説明します。


#### <a name="red-hat-linux-7"></a>Red Hat Linux 7
| OS バージョン | カーネル バージョン |
|:--|:--|
| 7.0 | 3.10.0-123 |
| 7.1 | 3.10.0-229 |
| 7.2 | 3.10.0-327 |
| 7.3 | 3.10.0-514 |
| 7.4 | 3.10.0-693 |
| 7.5 | 3.10.0-862 |

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
| 6.9 | 2.6.32-696 |

#### <a name="red-hat-linux-5"></a>Red Hat Linux 5
| OS バージョン | カーネル バージョン |
|:--|:--|
| 5.8 | 2.6.18-308 |
| 5.9 | 2.6.18-348 |
| 5.10 | 2.6.18-371 |
| 5.11 | 2.6.18-398<br>2.6.18-400<br>2.6.18-402<br>2.6.18-404<br>2.6.18-406<br>2.6.18-407<br>2.6.18-408<br>2.6.18-409<br>2.6.18-410<br>2.6.18-411<br>2.6.18-412<br>2.6.18-416<br>2.6.18-417<br>2.6.18-419<br>2.6.18-420 |

### <a name="ubuntu-server"></a>Ubuntu Server
- カスタム カーネル (標準カーネルの再コンパイルを含む) はサポートされていません。

| OS バージョン | カーネル バージョン |
|:--|:--|
| 16.04 | 4.4.\*<br>4.8.\*<br>4.10.\*<br>4.11.\*<br>4.13.\* |
| 14.04 | 3.13.\*<br>4.4.\* |

### <a name="oracle-enterprise-linux-with-unbreakable-enterprise-kernel"></a>Unbreakable Enterprise Kernel を搭載した Oracle Enterprise Linux
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
| 5.10 | Oracle 2.6.39-400 (UEK R2) |
| 5.11 | Oracle 2.6.39-400 (UEK R2) |

#### <a name="suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server

#### <a name="suse-linux-11"></a>SUSE Linux 11
| OS バージョン | カーネル バージョン
|:--|:--|
| 11 SP2 | 3.0.101-0.7 |
| 11 SP3 | 3.0.101-0.47 |
| 11 SP4 | 3.0.101-65 |


## <a name="diagnostic-and-usage-data"></a>診断と使用状況データ
マイクロソフトは、お客様によるサービス マップ サービスの使用を通して、使用状況とパフォーマンス データを自動的に収集します。 Microsoft はこのデータを使用して、提供する Service Map サービスの品質、セキュリティ、整合性の向上に努めています。 データには、オペレーティング システムやバージョンなど、ソフトウェアの構成に関する情報が含まれています。 また、正確で効果的なトラブルシューティングの機能を提供するために、IP アドレス、DNS 名、ワークステーション名も含まれています。 名前や住所などの連絡先情報は収集されません。

データの収集と使用の詳細については、「[Microsoft オンライン サービスのプライバシーに関する声明](https://go.microsoft.com/fwlink/?LinkId=512132)」を参照してください。



## <a name="next-steps"></a>次の手順
- Service Map をデプロイして構成したら、[Service Map を使用する]( monitoring-service-map.md)方法を確認します。
