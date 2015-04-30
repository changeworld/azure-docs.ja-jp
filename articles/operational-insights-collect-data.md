<properties 
   pageTitle="コンピューター データの収集" 
   description="オンプレミスまたはクラウド インフラストラクチャのサーバーからコンピューター データを収集します" 
   services="operational-insights" 
   documentationCenter="" 
   authors="bandersmsft" 
   manager="jwhit" 
   editor="bandersmsft"/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/20/2015"
   ms.author="banders"/>
#コンピューター データの収集

オペレーション インサイトは、オンプレミスまたはクラウド インフラストラクチャのサーバーのデータを使用します。コンピューター データは次のソースから収集することができます。

- Operations Manager エージェント
- オペレーション インサイトに直接接続されているコンピューター
- Azure Storage サービスの仮想マシン診断データ

データが収集されると、オペレーション インサイト サービスに送信されます。

## System Center Operations Manager からオペレーション インサイトに接続する 

オペレーション インサイトを既存の System Center Operations Manager 環境に接続することができます。これにより、既存の Operations Manager エージェントをオペレーション インサイト エージェントとして使用できます。オペレーション インサイトでの Operations Manager の使用の詳細については、「[Operations Manager とオペレーション インサイトの連携に関する考慮事項](operational-insights-operations-manager.md)」を参照してください。.

Operations Manager を使用して、次のワークロードのいずれかを監視する場合、Operations Manager をそのワークロードの実行アカウントとして設定する必要があります。アカウントの設定の詳細については、「[オペレーション インサイトで使用される Operations Manager の実行アカウント](operational-insights-run-as.md)」を参照してください。

- SQL の評価
- Virtual Machine Manager
- Lync Server
- SharePoint

 >[AZURE.NOTE] オペレーション インサイトのサポートは、Operations Manager 2012 SP1 UR6 および Operations Manager 2012 R2 UR2 以降で使用できます。プロキシ サポートは System Center Operations Manager 2012 SP1 UR7 および System Center Operations Manager 2012 R2 UR3 に追加されました。

#### オペレーション インサイトに Operations Manager を直接接続してエージェントを追加するには

1. Operations Manager コンソールで、**[管理]** をクリックします。

2. **[オペレーション インサイト]** ノードを展開して、**[オペレーション インサイトの接続]** をクリックします。

3. **[オペレーション インサイトに登録する]** リンクをクリックして画面の指示に従います。 

4. 登録ウィザードの完了後、**[コンピューター/グループの追加]** をクリックします。

5. **[コンピューターの検索]** ダイアログ ボックスで、Operations Manager で監視されるコンピューターまたはグループを検索できます。オペレーション インサイトに追加するコンピューターまたはグループを選択し、**[追加]** をクリックします。**[OK]** をクリックします。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## オペレーション インサイトにコンピューターを直接接続する 

追加する各コンピューターにオペレーション インサイト エージェントをインストールすると、オペレーション インサイトにコンピューターを直接接続できます。 


###エージェントのダウンロードとインストール

####エージェントのセットアップ ファイルをダウンロードするには
1. **オペレーション インサイト ポータル**にある **[概要]** ページで、**[サーバーと利用状況]** をクリックします。
1. **[直接接続されているサーバー]** の **[構成]** をクリックします。
1. **[エージェントの追加]** の横で、エージェントのリンクをクリックしてセットアップ ファイルをダウンロードします。
1. **[プライマリ ワークスペース キー]** ボックスでキーを選択してコピーします (Ctrl キー + C)。


#### セットアップを使用してエージェントをインストールするには
1. セットアップを実行して、管理対象のコンピューターにエージェントをインストールします。
1. **[Microsoft Azure オペレーション インサイトにエージェントを接続]** を選択してから **[次へ]** をクリックします。
1. プロンプトが表示されたら、手順 4. でコピーした情報を入力します。
1. 完了すると、**コントロール パネル**に **Microsoft 管理エージェント**が表示されます。

#### コマンド ラインを使用してエージェントをインストールするには
コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

### Microsoft Monitoring Agent の構成 (省略可能)
次の情報を使用して、エージェントが Microsoft Azure オペレーション インサイト サービスと直接通信できるようにします。エージェントを構成すると、エージェントはエージェント サービスに登録され、必要な構成情報と、インテリジェンス パックの情報が含まれている管理パックを取得します。

エージェントで管理されているコンピューターからデータが収集されると、監視されているコンピューターの数がオペレーション インサイト ポータルの **[直接接続されたエージェント]** の [使用状況] ページに表示されます。オペレーション インサイト ポータルでは、データを送信するすべてのコンピューターのデータと評価情報を確認できます。

エージェントは、必要に応じて無効にすることや、コマンド ラインまたはスクリプトを使用して有効にすることもできます。

#### エージェントを構成するには
- **Microsoft Monitoring Agent** をインストールした後、**コントロール パネル**を開きます。
- Microsoft Monitoring Agent を開いてから、[Azure オペレーション インサイト] タブをクリックします。
- **[Azure オペレーション インサイトに接続する]** チェック ボックスをオンにします。
- **[ワークスペース ID]** ボックスに、オペレーション インサイト ポータルで提供されたワークスペース ID を入力します。
- [アカウント キー] ボックスに、エージェントをインストールするときにコピーした**プライマリ ワークスペース キー**を貼り付けて、**[OK]** をクリックします。

#### エージェントを無効にするには
- エージェントをインストールした後、**コントロール パネル**を開きます。
- Microsoft Monitoring Agent を開いてから、**[Azure オペレーション インサイト]** タブをクリックします。
- **[Azure オペレーション インサイトに接続する]** チェック ボックスをオフにします。

#### コマンド ラインまたはスクリプトを使用してエージェントを有効にするには
次の例の Windows PowerShell または VB スクリプトを使用できます。

    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### プロキシとファイアウォール設定の構成 (省略可能)
環境内のプロキシ サーバーまたはファイアウォールによってインターネットへのアクセスが制限されている場合、次の手順に従って、Operations Manager またはエージェントがオペレーション インサイト サービスと通信できるようにすることが必要になる場合があります。 



- [プロキシとファイアウォール設定の構成 (省略可能)](operational-insights-proxy-filewall.md) 

## Microsoft Azure でサーバーからデータを分析する

オペレーション インサイトでは、[Azure Cloud Services の診断](operational-insights-log-collection.md)を有効にすることで、クラウド サービスや仮想マシンのイベントおよび IIS ログを迅速に検索することができます。 また、Microsoft Monitoring Agent をインストールすると仮想マシンから追加のインサイトを取得することもできます。

更新の評価、変更の追跡、SQL 評価の各インテリジェンス パックはすべて、Microsoft Monitoring Agent と連携して、仮想マシンに関する詳細なインサイトを提供します。まだ有効にしていない場合は、[オペレーション インサイト ポータル](https://preview.opinsights.azure.com/)にサインインしたときにこれらのインテリジェンス パックを有効にすることができます。

Azure の仮想マシンの場合は、エージェント ベースのデータ収集を有効にする簡単な方法として、次の 2 つがあります。

- Microsoft Azure の管理ポータル

- PowerShell の使用

エージェント ベースのデータ収集を使用する場合は、[オペレーション インサイト ポータル](https://preview.opinsights.azure.com/)の [ログ管理の構成] ページでどのログを収集するかを構成する必要があります。

Azure Cloud Services の診断を使用してログ データのインデックスを作成するようにオペレーション インサイトを構成している場合、ログを収集するようにエージェントを構成すると、同じイベント ログに対してインデックスが作成されたデータのコピーが 2 つ作成されます。エージェントをインストールしている場合は、エージェントを使用してログ データを収集する必要があります。Azure Cloud Services の診断によって収集されたログにインデックスを作成しないでください。

### Microsoft Azure の管理ポータル

[オペレーション インサイト ポータル](https://preview.opinsights.azure.com/), で、オペレーション インサイト ワークスペースに移動し、[サーバー] タブをクリックします。仮想マシンの一覧がタブに表示されます。エージェントをインストールする仮想マシンを選択し、**[オペレーション インサイトを有効にする]** をクリックします。

エージェントがインストールされ、オペレーション インサイト ワークスペース用に構成されます。

![オペレーション インサイトの [サーバー] ページの画像](./media/operational-insights-collect-data/servers.png)

### PowerShell

スクリプトで Azure 仮想マシンに変更を加える場合は、PowerShell を使用して、Microsoft Monitoring Agent を有効にできます。

Microsoft Monitoring Agent は [Azure の仮想マシンの拡張機能](https://msdn.microsoft.com/library/azure/dn832621.aspx) で、以下の例のように、PowerShell を使用して管理することができます。

    Add-AzureAccount
    
    $workspaceId="enter workspace here"
    $workspaceKey="enter workspace key here"
    $hostedService="enter hosted service here"
    
    $vm = Get-AzureVM -ServiceName $hostedService
    Set-AzureVMExtension -VM $vm -Publisher 'Microsoft.EnterpriseCloud.Monitoring' -ExtensionName 'MicrosoftMonitoringAgent' -Version '1.*' -PublicConfiguration "{'workspaceId':  '$workspaceId'}" -PrivateConfiguration "{'workspaceKey': '$workspaceKey' }" | Update-AzureVM -Verbose

PowerShell を使用して構成する場合は、ワークスペース ID とワークスペース キーを指定する必要があります。ワークスペース ID とワークスペース キーは、オペレーション インサイト ポータルにある、[サーバーと利用状況] ページで見つけることができます。

![オペレーション インサイトの [直接エージェントの構成] の画像](./media/operational-insights-collect-data/direct-agent-cfg.png)

### Azure Cloud Services の診断を使用してデータを収集する

オペレーション インサイトでは、Azure Cloud Services の診断によって Azure Storage サービスに書き込まれたデータを分析できます。主に次の 2 つの手順を実行します。

- 診断データを Azure ストレージに収集するように構成する
- ストレージ アカウント内のデータを分析するようにオペレーション インサイトを構成する

以下のトピックでは、Azure ストレージへの診断データの収集を有効にする方法と、Azure ストレージのデータを分析するようにオペレーション インサイトを構成する方法について説明します。

Azure 診断は、Azure で実行している Worker ロール、Web ロール、または仮想マシンから診断利用統計情報を収集できる Azure 拡張機能です。利用統計情報は、Azure ストレージ アカウントに格納され、オペレーション インサイトで使用できます。

>[AZURE.NOTE] 診断データをストレージ アカウントに送信する際には、ストレージおよびトランザクションの通常のデータ料金が発生します。

Azure 診断では、次の種類の利用統計情報を収集できます。

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>データ ソース</b></td>
		<td><b>

説明</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>IIS Logs</td>
		<td>IIS Web サイトに関する情報。</td>
    </tr>
    <tr align="left" valign="top">
		<td>Azure 診断インフラストラクチャ ログ</td>
		<td>診断自体に関する情報。</td>
    </tr>
	<tr align="left" valign="top">
		<td>IIS の失敗した要求ログ </td>
		<td>IIS サイトまたはアプリケーションへの失敗した要求に関する情報。</td>
    </tr>
	    <tr align="left" valign="top">
		<td>Windows イベント ログ</td>
		<td>Windows イベント ログ システムに送信された情報。</td>
    </tr>
    <tr align="left" valign="top">
		<td>パフォーマンス カウンター</td>
		<td>オペレーティング システムとカスタム パフォーマンス カウンター</td>
    </tr>
    <tr align="left" valign="top">
		<td>クラッシュ ダンプ</td>
		<td>アプリケーションがクラッシュした場合のプロセスの状態に関する情報。</td>
    </tr>
    <tr align="left" valign="top">
		<td>カスタム エラー ログ</td>
		<td>アプリケーションまたはサービスで作成されたログ。</td>
    </tr>
    <tr align="left" valign="top">
		<td>NET EventSource</td>
		<td>コードで .NET <a href="https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx">EventSource クラス</a>を使用して生成されたイベント。</td>
    </tr>
    <tr align="left" valign="top">
		<td>マニフェスト ベースの ETW</td>
		<td>すべてのプロセスで生成された ETW イベント。</td>
    </tr>
    </tbody>
    </table>


現在、オペレーション インサイトで分析できるのは、Web ロールからの IIS ログと、Web ロール、Worker ロール、および Azure 仮想マシンからの Windows イベント ログです。ログは、次の場所にある必要があります。

- WADWindowsEventLogsTable (テーブル ストレージ) - Windows イベント ログの情報を含みます。

- wad-iis-logfiles (BLOB ストレージ) - IIS ログに関する情報を含みます。

仮想マシンについては、[Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) を仮想マシンにインストールすることもできます。こうすることで、IIS ログとイベント ログを分析できるだけでなく、構成の変更の追跡などの追加の分析や更新の評価を実行することもできるようになります。[Microsoft Monitoring Agent](http://go.microsoft.com/fwlink/?LinkId=517269) をインストールすると追加のインサイトが有効になります。

>[AZURE.NOTE] 現在、Azure Websites からの IIS ログはサポートされていません。

オペレーション インサイトで分析できるログを追加する順番を決定するために、[フィードバック ページ](http://feedback.azure.com/forums/267889-azure-operational-insights/category/88086-log-management-and-log-collection-policy)で投票にご協力ください。

### IIS ログとイベントの収集のために Web ロールで Azure 診断を有効にする

「[クラウド サービスの診断を有効にする方法](https://msdn.microsoft.com/library/azure/dn482131.aspx)」を参照してください。ここでは、そのトピックから基本的な情報を流用し、Microsoft Azure オペレーション インサイトを使用するために手順をカスタマイズします。

診断を有効にした場合

- 既定では、IIS ログは scheduledTransferPeriod の転送間隔で転送されたログ データと共に格納されます。

- 既定では、Windows イベント ログは転送されません。

#### 診断を有効にするには

Windows イベント ログを有効にするか、または scheduledTransferPeriod を変更するには、XML 構成ファイル (diagnostics.wadcfg) を使用して Azure 診断を構成します。手順については、「クラウド サービスの診断を有効にする方法」の「[手順 2. Visual Studio ソリューションに diagnostics.wadcfg ファイルを追加する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step2) 」と「[手順 3. 診断機能をアプリケーション向けに構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step3) 」を参照してください。次の構成ファイルの例では、IIS ログと、アプリケーション ログとシステム ログからすべてのイベントを収集します。

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


「クラウド サービスの診断を有効にする方法」の「[手順 4. 診断データのストレージを構成する](https://msdn.microsoft.com/library/azure/dn482131.aspx#BKMK_step4) 」で、次の例に示すように、ConfigurationSettings にストレージ アカウントを指定していることを確認します。


    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>


**AccountName** および **AccountKey** の値は、Microsoft Azure の管理ポータルのストレージ アカウント ダッシュボードの [アクセス キーの管理] に表示されます。接続文字列のプロトコルは **https** である必要があります。

更新された診断の構成がクラウド サービスに適用され、Azure ストレージに診断データが書き込まれていれば、オペレーション インサイトを構成する準備が整っています。

### イベント ログと IIS ログの収集のために仮想マシンで Azure Cloud Services の診断を有効にする

イベント ログと IIS ログを収集するために、Microsoft Azure の管理ポータルを使用して、仮想マシンで Azure Cloud Services の診断を有効にするには、以下の手順を実行します。

#### Azure の管理ポータルを使用して仮想マシンで Azure Cloud Services の診断を有効にするには

1. 仮想マシンを作成する際に、VM エージェントをインストールします。仮想マシンが既に存在する場合は、VM エージェントが既にインストールされていることを確認します。
	- 既定の Azure の管理ポータルを使って仮想マシンを作成する場合は、**[カスタム作成]** を実行して **[VM エージェントのインストール]** を選択します。
	- 新しい Azure の管理ポータルを使って仮想マシンを作成する場合は、**[オプションの構成]**、**[診断]** の順に選択し、**[状態]** を **[オン]** に設定します。
	
	完了すると、診断データを収集する Azure 診断の拡張機能が VM に自動的にインストールされ、実行されます。

2. 既存の VM で監視を有効にし、イベント ログを構成します。診断は VM レベルで有効にすることができます。診断を有効にして、イベント ログを構成するには、次の手順を実行します。
	1.VM を選択します。
	2.**[監視]** をクリックします。
	3.**[診断]** をクリックします。
	4.**[状態]** を **[オン]** に設定します。
	5.使用する診断指標を選択します。オペレーション インサイトで分析できるのは、Windows イベント システム ログ、Windows イベント アプリケーション ログ、IIS ログです。
	7.**[OK]** をクリックします。

Azure PowerShell を使用すると、Azure ストレージに書き込むイベントをより細かく指定できます。構成ファイルのサンプルとスキーマの詳細なドキュメントについては、Azure 診断 1.2 の構成スキーマに関するページを参照してください。[Azure PowerShell のインストールおよび構成方法に関するページ](install-configure-powershell/)に従って、Azure PowerShell バージョン 0.8.7 以降をインストールして構成してください。 バージョン 1.2 より前の Microsoft Azure 診断をインストールしてある場合は、新しいポータルを使って診断を有効にしたり構成したりすることはできません。

次の PowerShell スクリプトを使用して、エージェントを有効にして更新することができます。このスクリプトでカスタム ログ構成を使用することもできます。その場合、スクリプトを変更して、ストレージ アカウント、サービス名、仮想マシンの名前を設定します。

#### Azure PowerShell を使用して仮想マシンで診断を有効にするには

次のサンプル スクリプトを確認、コピーして、必要に応じて変更し、PowerShell スクリプト ファイルとして保存します。その後、スクリプトを実行します。

    # Connect to Azure
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
    

### オペレーション インサイトによる Azure Storage 分析を有効にする

既定の Azure ポータルで、オペレーション インサイト ワークスペースに移動し、**[ストレージ]** タブをクリックします。次の情報を使用して、Azure 診断で Azure ストレージ アカウントからデータを読み取るようにオペレーション インサイトを構成します。

#### オペレーション インサイトによる分析を有効にするには

1. **[追加]** をクリックして **[ストレージ アカウントの追加]** ボックスを開きます。

2. **ストレージ アカウント**を選択します。

3. データの種類として、**[イベント]** または **[IIS ログ]** を選択します。

4. **[OK]** をクリックします。

5. 収集するデータの種類とストレージ アカウントの組み合わせごとに前の手順を繰り返します。

約 1 時間で、ストレージ アカウントからのデータが表示され、オペレーション インサイト内で分析できるようになります。




## 関連コンテンツ

- [ブログの投稿: オペレーション インサイトにサーバーを直接接続する](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [ブログの投稿: Azure の仮想マシンでオペレーション インサイトを有効にする](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)

## 次のステップ

[プロキシとファイアウォール設定の構成 (省略可能)](operational-insights-proxy-filewall.md)



<!--HONumber=52-->