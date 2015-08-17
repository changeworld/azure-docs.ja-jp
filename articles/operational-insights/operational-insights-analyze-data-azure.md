<properties
   pageTitle="Microsoft Azure でサーバーからデータを分析する"
   description="Azure 診断を有効にしてクラウド サービスや仮想マシンのイベントと IIS ログを検索します。"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/05/2015"
   ms.author="banders"/>
# Microsoft Azure でサーバーからデータを分析する

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operational Insights は、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。Azure 診断によって生成された場合は、Azure Storage からマシンのデータを収集できます。

Azure Storage から収集されたデータを使用して、クラウド サービスや仮想マシンのイベントおよび IIS ログを迅速に検索することができます。また、Microsoft Monitoring Agent をインストールすると仮想マシンから追加のインサイトを取得することもできます。

更新の評価、変更の追跡、SQL 評価の各ソリューションはすべて、Microsoft Monitoring Agent と連携して、仮想マシンに関する詳細なインサイトを提供します。まだ有効にしていない場合は、[Operational Insights ポータル](https://www.microsoft.com/oms/)にサインインしたときに[ソリューションを追加](operational-insights-setup-workspace.md)することができます。

Azure の仮想マシンの場合は、エージェント ベースのデータ収集を有効にする簡単な方法として、次の 2 つがあります。

- Microsoft Azure の管理ポータル

- PowerShell の使用

ログ データにエージェント ベースの収集を使用する場合、[Operational Insights ポータル](https://www.microsoft.com/oms/)の [ログの管理] 構成ページで収集するログを構成する必要があります。

 >[AZURE.NOTE]Azure 診断を使用してログ データにインデックスを作成するように Operational Insights を構成し、ログを収集するようにエージェントを構成すると、同じログに 2 回インデックスが作成されます。両方のデータ ソースに通常のデータ料金が課金されます。エージェントをインストールしている場合はエージェントを使用してログ データを収集し、Azure 診断によって収集されたログにインデックスを作成しないでください。

## Microsoft Azure の管理ポータル

Operational Insights のエージェントは [Azure ポータル](https://manage.windowsazure.com/#Workspaces/OperationalInsightExtension/Workspaces)からインストールできます。

### Operational Insights のエージェントをインストールするには
1. Azure ポータルで、Operational Insights のワークスペースに移動し、**[サーバー]** タブを選択します。
2. 仮想マシンの一覧がタブに表示されます。エージェントをインストールする仮想マシンを選択し、**[Op Insights の有効化]** をクリックします。

エージェントが自動的にインストールされ、Operational Insights のワークスペース用に構成されます。

![オペレーション インサイトの [サーバー] ページの画像](./media/operational-insights-analyze-data-azure/servers.png)

 >[AZURE.NOTE]Operational Insights にエージェントが自動的にインストールされるには、[Azure VM エージェント](https://msdn.microsoft.com/library/azure/dn832621.aspx)がインストールされている必要があります。



## PowerShell

スクリプトで Azure 仮想マシンに変更を加える場合は、PowerShell を使用して、Microsoft Monitoring Agent を有効にできます。

Microsoft Monitoring Agent は [Azure 仮想マシンの拡張機能](https://msdn.microsoft.com/library/azure/dn832621.aspx)で、次の例のように PowerShell を使用して管理できます。

```powershell
Add-AzureAccount

$workspaceId="enter workspace here"
$workspaceKey="enter workspace key here"
$hostedService="enter hosted service here"

$vm = Get-AzureVM –ServiceName $hostedService
Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose
```

PowerShell を使用して構成する場合は、ワークスペース ID とプライマリ キーを指定する必要があります。ワークスペース ID とプライマリ キーは、Operational Insights ポータルにある、**[設定]** ページで見つけることができます。

![ソース](./media/operational-insights-analyze-data-azure/sources01.png)

## Azure 診断を使用したデータの収集

Operational Insights は、Azure 診断によって Azure Storage に書き込まれたデータを分析できます。主に次の 2 つの手順を実行します。

- 診断データを Azure ストレージに収集するように構成する
- ストレージ アカウント内のデータを分析するようにオペレーション インサイトを構成する

以下のトピックでは、Azure Storage への診断データの収集を有効にする方法と、Azure Storage のデータを分析するようにオペレーション インサイトを構成する方法について説明します。

Azure 診断は、Azure で実行している worker ロール、Web ロール、または仮想マシンから診断データを収集できる Azure の拡張機能です。データは Azure ストレージ アカウントに格納され、Operational Insights で使用できます。

>[AZURE.NOTE]ストレージ アカウントに診断を送信する際、および Operational Insights がストレージ アカウントからデータを読み取る際のデータの格納と転送には通常の料金が課金されます。

Azure 診断では、次の種類のテレメトリを収集できます。

データ ソース|説明
 ---|---
IIS Logs|IIS Web サイトに関する情報。
Azure 診断インフラストラクチャ ログ|診断自体に関する情報。
IIS の失敗した要求ログ |IIS サイトまたはアプリケーションへの失敗した要求に関する情報。
Windows イベント ログ|Windows イベント ログ システムに送信された情報。
パフォーマンス カウンター|オペレーティング システムとカスタム パフォーマンス カウンター
クラッシュ ダンプ|アプリケーションがクラッシュした場合のプロセスの状態に関する情報。
カスタム エラー ログ|アプリケーションまたはサービスで作成されたログ。
NET EventSource|.NET [EventSource クラス](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx) を使用してコードで生成されたイベント
マニフェスト ベースの ETW|すべてのプロセスで生成された ETW イベント
Syslog|Syslog または Rsyslog デーモンに送信されるイベント


現時点では、Operational Insights は次のログを分析できません。

- Web ロールと仮想マシンからの IIS ログ
- Windows オペレーティング システムを実行する Web ロール、worker ロール、および Azure の仮想マシンからの Windows イベント ログ
- Linux オペレーティング システムを実行する Azure の仮想マシンからの syslog

ログは、次の場所にある必要があります。

- WADWindowsEventLogsTable (テーブル ストレージ) - Windows イベント ログの情報を含みます。
- wad-iis-logfiles (BLOB ストレージ) - IIS ログに関する情報を含みます。
- LinuxsyslogVer2v0 (テーブル ストレージ) – Linux の syslog イベントを含みます。

 >[AZURE.NOTE]現在、Azure Websites からの IIS ログはサポートされていません。

仮想マシンについては、[Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) をインストールして追加のインサイトを有効にすることもできます。これにより、IIS ログとイベント ログを分析できるほか、構成の変更の追跡、SQL の評価、更新の評価などの追加の分析を実行することもできます。

Operational Insights で分析できるログを追加する順番を決定するために、[フィードバック ページ](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)で投票にご協力ください。

## IIS ログとイベントの収集のために Web ロールで Azure 診断を有効にする

「[クラウド サービスの診断を有効にする方法](https://msdn.microsoft.com/library/azure/dn482131.aspx)」を参照してください。ここでは、そのトピックから基本的な情報を流用し、Microsoft Azure オペレーション インサイトを使用するために手順をカスタマイズします。

診断を有効にした場合

- 既定では、IIS ログは scheduledTransferPeriod の転送間隔で転送されたログ データと共に格納されます。

- 既定では、Windows イベント ログは転送されません。

### 診断を有効にするには

Windows イベント ログを有効にする、または scheduledTransferPeriod を変更するには、「クラウド サービスの診断を有効にする方法」のトピック「[手順 2: Visual Studio ソリューションに diagnostics.wadcfg ファイルを追加する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2)」と「[手順 3: 診断機能をアプリケーション向けに構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3)」に示す XML 構成ファイル (diagnostics.wadcfg) を使用して Azure 診断を構成します。

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


「クラウド サービスの診断を有効にする方法」のトピック「[手順 4: 診断データのストレージを構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4)」で、次の例に示すように、ConfigurationSettings にストレージ アカウントを指定します。


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** および **AccountKey** の値は、Microsoft Azure の管理ポータルのストレージ アカウント ダッシュボードの [アクセス キーの管理] に表示されます。接続文字列のプロトコルは **https** である必要があります。

更新された診断の構成がクラウド サービスに適用され、Azure Storage に診断データが書き込まれていれば、オペレーション インサイトを構成する準備が整っています。

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
	5. 使用する診断指標を選択します。オペレーション インサイトで分析できるのは、Windows イベント システム ログ、Windows イベント アプリケーション ログ、IIS ログです。
	7. **[OK]** をクリックします。

Azure PowerShell を使用すると、Azure Storage に書き込むイベントをより細かく指定できます。構成ファイルのサンプルとスキーマの詳細なドキュメントについては、Azure 診断 1.2 の構成スキーマに関するページを参照してください。[Azure PowerShell のインストールおよび構成方法](powershell-install-configure)に関するページに従って、Azure PowerShell バージョン 0.8.7 以降をインストールして構成してください。バージョン 1.2 より前の Microsoft Azure 診断をインストールしてある場合は、新しいポータルを使って診断を有効にしたり構成したりすることはできません。

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


## オペレーション インサイトによる Azure Storage 分析を有効にする

次の手順に従ってストレージの分析を有効にし、Azure 診断で Azure ストレージ アカウントからデータを読み取るように Operational Insights を構成します。

### オペレーション インサイトによる分析を有効にするには

1. 既定の Azure ポータルで、Operational Insights ワークスペースに移動し、**[ストレージ]** タブをクリックします。![ワークスペースの [ストレージ] タブ](./media/operational-insights-analyze-data-azure/workspace-storage-tab.png)
2. **[ストレージ アカウントの追加]** をクリックして **[ストレージ アカウントの追加]** ボックスを開きます。
3. 使用するストレージ アカウントを選択します。
4. **[データの種類]** の一覧で、データの種類 (**[イベント]**、**[IIS ログ]**、または **[Syslog (Linux)]**) を選択します。
5. **[OK]** イメージをクリックします。<br> ![[ストレージ アカウント] ボックス](./media/operational-insights-analyze-data-azure/storage-account.png)
6. 収集するデータの種類とストレージ アカウントの組み合わせごとに前の手順を繰り返します。

約 1 時間で、ストレージ アカウントからのデータが表示され、オペレーション インサイト内で分析できるようになります。

## 関連コンテンツ

- [Operational Insights にコンピューターを直接接続する](operational-insights-direct-agent)
- [ブログの投稿: Azure の仮想マシンで Operational Insights を有効にする](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 次のステップ

[プロキシとファイアウォール設定の構成 (省略可能)](../operational-insights-proxy-filewall.md)

<!---HONumber=August15_HO6-->