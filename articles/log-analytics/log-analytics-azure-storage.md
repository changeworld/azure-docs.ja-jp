<properties
	pageTitle="Azure Storage を Log Analytics に接続する |Microsoft Azure"
	description="Log Analytics は、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。Azure 診断によって生成された場合は、Azure Storage からマシンのデータを収集できます。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/28/2016"
	ms.author="banders"/>

# Azure Storage を Log Analytics に接続する

Log Analytics は、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。Azure 診断によって生成された場合は、Azure Storage からマシンのデータを収集できます。

Azure Storage から収集されたデータを使用して、クラウド サービスや仮想マシンのイベントおよび IIS ログを迅速に検索することができます。また、Microsoft Monitoring Agent をインストールすると仮想マシンから追加のインサイトを取得することもできます。

更新の評価、変更の追跡、SQL 評価の各ソリューションはすべて、Microsoft Monitoring Agent と連携して、仮想マシンに関する詳細なインサイトを提供します。まだソリューションを追加していない場合は、[OMS ポータル](https://www.microsoft.com/oms/)にサインインしたときに、[ソリューション ギャラリーから Log Analytics ソリューションを追加](log-analytics-add-solutions.md)できます。

Azure の仮想マシンの場合は、エージェント ベースのデータ収集を有効にする簡単な方法として、次の 2 つがあります。

- Microsoft Azure の管理ポータル
- PowerShell の使用

ログ データにエージェント ベースの収集を使用する場合、[OMS ポータル](https://www.microsoft.com/oms/)の [ログの管理] 構成ページで収集するログを構成する必要があります。

>[AZURE.NOTE] Azure 診断を使用してログ データにインデックスを作成するように OMS を構成し、ログを収集するようにエージェントを構成すると、同じログに 2 回インデックスが作成されます。両方のデータ ソースに通常のデータ料金が課金されます。エージェントをインストールしている場合はエージェントを使用してログ データを収集し、Azure 診断によって収集されたログにインデックスを作成しないでください。

## Microsoft Azure ポータル:

[Azure ポータル](https://portal.azure.com)を使用して、OMS 用のエージェントをインストールし、そのエージェントを実行する Azure 仮想マシンを接続できます。

### OMS 用のエージェントをインストールして仮想マシンをワークスペースに接続するには

1.	[Azure ポータル](http://portal.azure.com)にサインインします。
2.	**Log Analytics (OMS)** を見つけ、選択します。
3.	OMS ワークスペースの一覧で、Azure VM を接続するワークスペースを選択します。 ![OMS ワークスペース](./media/log-analytics-azure-storage/oms-connect-azure-01.png)
4.	**[Log analytics management]** (Log Analytics 管理) の下にある **[仮想マシン]** をクリックします。![Virtual Machines](./media/log-analytics-azure-storage/oms-connect-azure-02.png)
5.	**[仮想マシン]** の一覧で、エージェントをインストールする仮想マシンを選択します。VM の **OMS 接続の状態**は **[Not connected]** (未接続) になっています。![VM 未接続](./media/log-analytics-azure-storage/oms-connect-azure-03.png)
6.	仮想マシンの詳細で、**[接続]** をクリックします。エージェントが自動的にインストールされ、OMS ワークスペース用に構成されますが、このプロセスが完了するまで数分ほどかかる場合があります。![VM の接続](./media/log-analytics-azure-storage/oms-connect-azure-04.png)
7.	エージェントがインストールされて接続されると、**OMS 接続**の状態が更新され、**[This workspace]** (このワークスペース) と表示されます。![接続中](./media/log-analytics-azure-storage/oms-connect-azure-05.png)

>[AZURE.NOTE] OMS にエージェントが自動的にインストールされるには、[Azure VM エージェント](../virtual-machines/virtual-machines-windows-extensions-features.md)がインストールされている必要があります。Azure Resource Manager 仮想マシンがある場合、一覧には表示されないため、PowerShell を使用するか ARM テンプレートを作成して、エージェントをインストールする必要があります。

Microsoft Azure 管理ポータルに表示されない VM がある場合、次の原因が考えられます。

- 既に別の OMS ワークスペースによって管理されている
- 現時点ではサポートされていない ARM VM である

## PowerShell

スクリプトで Azure 仮想マシンに変更を加える場合は、PowerShell を使用して、Microsoft Monitoring Agent を有効にできます。

Microsoft Monitoring Agent は [Azure 仮想マシンの拡張機能](../virtual-machines/virtual-machines-windows-extensions-features.md)で、次の例のように PowerShell を使用して管理できます。

従来の Azure 仮想マシンの場合は、次の PowerShell の例を使用します。

```
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```


Azure Resource Manager 仮想マシンの場合は、次の PowerShell の例を使用します。

```
Login-AzureRMAccount
Select-AzureSubscription -SubscriptionId "**"


$workspaceId="**"
$workspaceKey="**"

$resourcegroup = "**"
$resourcename = "**"

$vm = Get-AzureRMVM -ResourceGroupName $resourcegroup -Name $resourcename
$location = $vm.Location

Set-AzureRMVMExtension -ResourceGroupName $resourcegroup -VMName $resourcename -Name 'MicrosoftMonitoringAgent' -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionType 'MicrosoftMonitoringAgent' -TypeHandlerVersion '1.0' -Location $location -SettingString "{'workspaceId':  '$workspaceId'}" -ProtectedSettingString "{'workspaceKey': '$workspaceKey' }"


```
PowerShell を使用して構成する場合は、ワークスペース ID とプライマリ キーを指定する必要があります。ワークスペース ID とプライマリ キーは、OMS ポータルにある、**[設定]** ページで見つけることができます。

![ワークスペース ID と主キー](./media/log-analytics-azure-storage/oms-analyze-azure-sources.png)

## Azure 診断を使用したデータの収集

OMS は、Azure 診断によって Azure Storage に書き込まれたデータを分析できます。主に次の 2 つの手順を実行します。

- 診断データを Azure ストレージに収集するように構成する
- ストレージ アカウント内のデータを分析するように OMS を構成する

以下のトピックでは、Azure Storage への診断データの収集を有効にする方法と、Azure Storage のデータを分析するように OMS を構成する方法について説明します。

Azure 診断は、Azure で実行している worker ロール、Web ロール、または仮想マシンから診断データを収集できる Azure の拡張機能です。データは Azure ストレージ アカウントに格納され、OMS で使用できます。

>[AZURE.NOTE] 有料サブスクリプションをお持ちの場合は、ストレージ アカウントに診断を送信する際、および OMS がストレージ アカウントからデータを読み取る際のデータの格納と転送に、通常の料金が課金されます。

Azure 診断では、次の種類のテレメトリを収集できます。

データ ソース|説明
 ---|---
IIS ログ|IIS Web サイトに関する情報。
Azure 診断インフラストラクチャ ログ|診断自体に関する情報。
IIS の失敗した要求ログ |IIS サイトまたはアプリケーションへの失敗した要求に関する情報。
Windows イベント ログ|Windows イベント ログ システムに送信された情報。
パフォーマンス カウンター|オペレーティング システムとカスタム パフォーマンス カウンター
クラッシュ ダンプ|アプリケーションがクラッシュした場合のプロセスの状態に関する情報。
カスタム エラー ログ|アプリケーションまたはサービスで作成されたログ。
NET EventSource|.NET [EventSource クラス](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource%28v=vs.110%29.aspx)を使用してコードで生成されたイベント
マニフェスト ベースの ETW|すべてのプロセスで生成された ETW イベント
Syslog|Syslog または Rsyslog デーモンに送信されるイベント


現時点では、OMS では次の分析が可能です。

- Web ロールと仮想マシンからの IIS ログ
- Windows オペレーティング システムを実行する Web ロール、worker ロール、および Azure の仮想マシンからの Windows イベント ログ
- Linux オペレーティング システムを実行する Azure の仮想マシンからの syslog

ログは、次の場所にある必要があります。

- WADWindowsEventLogsTable (テーブル ストレージ) - Windows イベント ログの情報を含みます。
- wad-iis-logfiles (BLOB ストレージ) - IIS ログに関する情報を含みます。
- LinuxsyslogVer2v0 (テーブル ストレージ) – Linux の syslog イベントを含みます。

    >[AZURE.NOTE] 現在、Azure Websites からの IIS ログはサポートされていません。

仮想マシンについては、[Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) をインストールして追加のインサイトを有効にすることもできます。これにより、IIS ログとイベント ログを分析できるほか、構成の変更の追跡、SQL の評価、更新の評価などの追加の分析を実行することもできます。

OMS で分析できるログを追加する順番を決定するために、[フィードバック ページ](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)で投票にご協力ください。

## IIS ログとイベントの収集のために Web ロールで Azure 診断を有効にする

「[クラウド サービスの診断を有効にする方法](https://msdn.microsoft.com/library/azure/dn482131.aspx)」を参照してください。ここでは、そのトピックから基本的な情報を流用し、OMS を使用するために手順をカスタマイズします。

診断を有効にした場合

- 既定では、IIS ログは scheduledTransferPeriod の転送間隔で転送されたログ データと共に格納されます。

- 既定では、Windows イベント ログは転送されません。

### 診断を有効にするには

Windows イベント ログを有効にする、または scheduledTransferPeriod を変更するには、「クラウド サービスの診断を有効にする方法」のトピック「[手順 2: Visual Studio ソリューションに diagnostics.wadcfg ファイルを追加する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2)」と「[手順 3: 診断機能をアプリケーション向けに構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3)」に示す XML 構成ファイル (diagnostics.wadcfg) を使用して Azure 診断を構成します。次の構成ファイルの例では、IIS ログと、アプリケーション ログとシステム ログからすべてのイベントを収集します。

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

「クラウド サービスの診断を有効にする方法」のトピック「[手順 4: 診断データのストレージを構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4)」で、次の例に示すように、ConfigurationSettings にストレージ アカウントを指定します。

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** および **AccountKey** の値は、Microsoft Azure の管理ポータルのストレージ アカウント ダッシュボードの [アクセス キーの管理] に表示されます。接続文字列のプロトコルは **https** である必要があります。

更新された診断の構成がクラウド サービスに適用され、Azure Storage に診断データが書き込まれていれば、OMS を構成する準備が整っています。

## イベント ログと IIS ログの収集のために仮想マシンで Azure 診断を有効にする

イベント ログと IIS ログを収集するために、Microsoft Azure の管理ポータルを使用して、仮想マシンで Azure 診断を有効にするには、以下の手順を実行します。

### Azure 管理ポータルを使用して仮想マシンで Azure 診断を有効にするには

1. 仮想マシンを作成する際に、VM エージェントをインストールします。仮想マシンが既に存在する場合は、VM エージェントが既にインストールされていることを確認します。
	- 既定の Azure の管理ポータルを使って仮想マシンを作成する場合は、**[カスタム作成]** を実行して **[VM エージェントのインストール]** を選択します。
	- 新しい Azure の管理ポータルを使って仮想マシンを作成する場合は、**[オプションの構成]**、**[診断]** の順に選択し、**[状態]** を **[オン]** に設定します。

	完了すると、診断データを収集する Azure 診断の拡張機能が VM に自動的にインストールされ、実行されます。

2. 既存の VM で監視を有効にし、イベント ログを構成します。診断は VM レベルで有効にすることができます。診断を有効にして、イベント ログを構成するには、次の手順を実行します。
	1. VM を選択します。
	2. **[監視]** をクリックします。
	3. **[診断]** をクリックします。
	4. **[状態]** を **[オン]** に設定します。
	5. 使用する診断指標を選択します。OMS で分析できるのは、Windows イベント システム ログ、Windows イベント アプリケーション ログ、IIS ログです。
	7. **[OK]** をクリックします。

Azure PowerShell を使用すると、Azure Storage に書き込むイベントをより細かく指定できます。構成ファイルのサンプルとスキーマの詳細なドキュメントについては、Azure 診断 1.2 の構成スキーマに関するページを参照してください。[Azure PowerShell のインストールおよび構成方法](../powershell-install-configure.md)に関するページに従って、Azure PowerShell バージョン 0.8.7 以降をインストールして構成してください。バージョン 1.2 より前の Microsoft Azure 診断をインストールしてある場合は、新しいポータルを使って診断を有効にしたり構成したりすることはできません。

次の PowerShell スクリプトを使用して、エージェントを有効にして更新することができます。このスクリプトでカスタム ログ構成を使用することもできます。その場合、スクリプトを変更して、ストレージ アカウント、サービス名、仮想マシンの名前を設定します。

### Azure PowerShell を使用して仮想マシンで診断を有効にするには

次のサンプル スクリプトを確認、コピーして、必要に応じて変更し、PowerShell スクリプト ファイルとして保存します。その後、スクリプトを実行します。


	#Connect to Azure
	Add-AzureAccount

	# settings to change:
	$wad_storage_account_name = "myStorageAccount"
	$service_name = "myService"
	$vm_name = "myVM"

	#Construct Azure Diagnostics public config and convert to config format

	# Collect just system error events:
	$wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

	$wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
	$wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

	#Construct Azure diagnostics private config

	$wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
	$wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

	#Enable Diagnostics Extension for Virtual Machine

	$wad_extension_name = "IaaSDiagnostics"
	$wad_publisher = "Microsoft.Azure.Diagnostics"
	$wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

	(Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM


## OMS による Azure Storage 分析を有効にする

「[Log Analytics のデータ ソース](log-analytics-data-sources.md#collect-data-from-azure-diagnostics)」の情報を使用してストレージ分析を有効にし、Azure 診断で Azure Storage アカウントからデータを読み取るよう OMS を構成します。

約 1 時間で、ストレージ アカウントからのデータが表示され、OMS 内で分析できるようになります。


## 次のステップ

- エージェントが Log Analytics サービスと通信できるように組織がプロキシ サーバーまたはファイアウォールを使用している場合は、「[Configure proxy and firewall settings in Log Analytics](log-analytics-proxy-firewall.md)」 (Log Analytics のプロキシとファイアウォールの設定を構成する) を参照してください。

<!---HONumber=AcomDC_0504_2016-->