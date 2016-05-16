<properties
	pageTitle="Windows コンピューターを Log Analytics に接続する | Microsoft Azure"
	description="この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを OMS に直接接続する手順について説明します。"
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


# Windows コンピューターを Log Analytics に接続する

この記事では、Microsoft Monitoring Agent (MMA) のカスタマイズされたバージョンを使用して、オンプレミスのインフラストラクチャ内の Windows コンピューターを OMS に直接接続する手順について説明します。OMS に追加するすべてのコンピューターに対してエージェントをインストールおよび接続して、それらのエージェントが OMS にデータを送信し、OMS ポータルにそのデータを表示し対処できるようにする必要があります。

エージェントのインストールには、セットアップ コマンド ラインを使用するか、Azure Automation の Desired State Configuration (DSC) を使用します。

コンピューターがインターネットに接続されている場合、エージェントはインターネットへの接続を介して OMS にデータを送信します。インターネットに接続されていないコンピューターの場合は、プロキシまたは OMS Log Analytics フォワーダーを使用できます。

OMS への Windows コンピューターの接続は、次の 3 つの手順を使用して簡単に行うことができます。

1. エージェントのセットアップ ファイルをダウンロードする
2. 選択した方法を使用してエージェントをインストールする
3. 必要に応じて、エージェントを構成する

次の図は、エージェントをインストールし構成した後の Windows コンピューターと OMS との関係を示したものです。

![oms-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)


## システム要件と必要な構成
エージェントをインストールまたはデプロイする前に、次の詳細を検証して、要件が満たされていることを確認してください。

- OMS MMA は、Windows Server 2008 SP 1 以上または Windows 7 SP1 以上を搭載したコンピューターにのみインストールできます。
- OMS サブスクリプションが必要です。詳細については、「[Get started with Log Analytics (Log Analytics の概要)](log-analytics-get-started.md)」を参照してください。
- 各 Windows コンピューターは、インターネットに接続できる必要があります。インターネット接続には、直接接続、プロキシを経由した接続、OMS Log Analytics を介した接続を使用できます。
- OMS MMA は、スタンドアロン コンピューター、サーバー、および仮想マシンにインストールできます。Azure でホストされる仮想マシンを OMS に接続する場合は、「[Connect Azure storage to Log Analytics (Log Analytics に Azure Storage を接続する)](log-analytics-azure-storage.md)」を参照してください。
- エージェントは、TCP ポート 443 を使用してさまざまなリソースに対応する必要があります。詳細については、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

## OMS からエージェントのセットアップ ファイルをダウンロードする
1. OMS ポータルにある **[概要]** ページで、**[設定]** タイルをクリックします。上部の **[接続されたソース]** タブをクリックします。![[接続されたソース] タブ](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. **[コンピューターの直接接続]** で、ご使用のコンピューターのプロセッサの種類に適用できる **[Windows エージェントのダウンロード]** をクリックしてセットアップ ファイルをダウンロードします。
3. **[ワークスペース ID]** の右側で、コピー アイコンをクリックし、ID をメモ帳に貼り付けます。
4. **[主キー]** の右側で、コピー アイコンをクリックし、キーをメモ帳に貼り付けます。![ワークスペース ID と主キーのコピー](./media/log-analytics-windows-agents/oms-direct-agent-primary-key.png)

## セットアップを使用してエージェントをインストールする
1. セットアップを実行して、管理対象のコンピューターにエージェントをインストールします。
2. [ようこそ] ページで **[次へ]** をクリックします。
3. [ライセンス条項] ページの記述内容を確認し、**[同意する]** をクリックします。
4. [インストール先フォルダー] ページで、既定のインストール フォルダーを変更するか、そのまま使用して、**[次へ]** をクリックします。
5. [エージェントのセットアップ オプション] ページでは、Operational Insights (OMS) または Operations Manager へのエージェントの接続を選択できます。後でエージェントを構成する場合は、空白にしておいてかまいません。**[次へ]** をクリックします。   
    - Operational Insights (OMS) への接続を選択した場合は、前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けて、** [次へ]** をクリックします。![ワークスペース ID と主キーの貼り付け](./media/log-analytics-windows-agents/oms-mma-aoi-setup.png)
    - Operations Manager への接続を選択した場合は、**管理グループ名**、**管理サーバー**名、および **管理サーバー ポート**を入力して、**[次へ]** をクリックします。[エージェント アクション アカウント] ページで、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択し、**[次へ]** をクリックします。![管理グループの構成](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![エージェント アクション アカウント](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. [インストールの準備完了] ページで、設定内容を確認し、**[インストール]** をクリックします。
7. [構成は正常に終了しました] ページで **[完了]** をクリックします。
8. 完了すると、**コントロール パネル**に **Microsoft Monitoring Agent** が表示されます。そこでは構成を検証して、エージェントが Operational Insights (OMS) に接続されていることを確認できます。OMS に接続されると、エージェントにより **Microsoft Monitoring Agent は Azure Operational Insights (OMS) サービスに正常に接続しました** という内容のメッセージが表示されます。

## コマンド ラインを使用してエージェントをインストールする
- コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。

    ```
    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

## Azure Automation の DSC を使用してエージェントをインストールする
1. [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) から xPSDesiredStateConfiguration DSC Module を Azure Automation にインポートします。  

2.	*OPSINSIGHTS\_WS\_ID* と *OPSINSIGHTS\_WS\_KEY* に対して Azure Automation 変数アセットを作成します。*OPSINSIGHTS\_WS\_ID* を OMS Log Analytics ワークスペース ID に設定し、*OPSINSIGHTS\_WS\_KEY* をワークスペースの主キーに設定します。
3.	次のスクリプトを使用し、MMAgent.ps1 として保存します。
4.	Azure Automation の DSC を使用してエージェントをインストールする場合は、次の例を変更してから使用してください。Azure Automation インターフェイスまたはコマンドレットを使用して、Azure Automation に MMAgent.ps1 をインポートします。
5.	構成にノードを割り当てます。15 分以内に、ノードはその構成を確認し、MMA がノードにプッシュされます。

```
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
        }

        xRemoteFile OIPackage {
            Uri = "https://opsinsight.blob.core.windows.net/publicfiles/MMASetup-AMD64.exe"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "E854571C-3C01-4128-99B8-52512F44E5E9"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}  


```


## エージェントを手動で構成する
エージェントのインストールは完了しているが、その構成がまだである場合、または構成に変更を加えた場合は、次の情報を使用してエージェントを有効にしたり再構成したりできます。エージェントを構成すると、エージェントはエージェント サービスに登録され、必要な構成情報と、ソリューションの情報が含まれている管理パックを取得します。

1. Microsoft Monitoring Agent をインストールした後、**コントロール パネル**を開きます。
2. **Microsoft Monitoring Agent** を開いてから、**[Azure Operational Insights (OMS) に接続する]** をクリックします。   
3. 前の手順でメモ帳にコピーしておいた**ワークスペース ID** と**ワークスペース キー (主キー)** を貼り付けて、**[OK]** をクリックします。![Operational Insights の構成](./media/log-analytics-windows-agents/oms-mma-aoi.png)

エージェントで管理されているコンピューターからデータが収集されると、OMS で監視されているコンピューターの数が OMS ポータルの **[設定]** の **[接続されたソース]** タブに **[接続されたサーバー]** として表示されます。


### エージェントを無効にするには
1. エージェントをインストールした後、**コントロール パネル**を開きます。
2. Microsoft Monitoring Agent を開いてから、**[Azure オペレーション インサイト (OMS)]** タブをクリックします。
3. **[Azure オペレーション インサイトに接続する]** チェック ボックスをオフにします。

## コマンド ラインを使用してエージェントを構成する

- 次の例では Windows PowerShell を使用できます。
    ```
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableAzureOperationalInsights('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    ```

## 必要に応じて、Operations Manager 管理グループへの報告を行うようエージェントを構成します。

IT インフラストラクチャ内で Operations Manager を使用する場合は、Operations Manager エージェントとして MMA エージェントを使用することもできます。

### Operations Manager 管理グループに報告するように MMA エージェントを構成するには
1.	エージェントのインストール先であるコンピューターで、**コントロール パネル**を開きます。
2.	**Microsoft Monitoring Agent** を開いてから、**[Operations Manager]** タブをクリックします。![[Microsoft Monitoring Agent Operations Manager] タブ](./media/log-analytics-windows-agents/oms-mma-om01.png)
3.	Operations Manager サーバーが Active Directory と統合されている場合は、[管理グループの割り当てを AD DS から自動的に更新する] をクリックします。
4.	**[追加]** をクリックして、**[管理グループを追加する]** ダイアログ ボックスを開きます。![Microsoft Monitoring Agent で管理グループを追加する](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.	**[管理グループ名]** ボックスに、管理グループの名前を入力します。
6.	**[プライマリ管理サーバー]** ボックスに、プライマリ管理サーバーのコンピューター名を入力します。
7.	**[管理サーバー ポート]** ボックスに、TCP ポート番号を入力します。
8.	**[エージェント アクション アカウント]** で、ローカル システム アカウントまたはローカル ドメイン アカウントのいずれかを選択します。
9.	**[OK]** をクリックして **[管理グループを追加する]** ダイアログ ボックスを閉じて、さらに **[OK]** をクリックして **[Microsoft Monitoring Agent のプロパティ]** ダイアログ ボックスを閉じます。

## 必要に応じて、OMS Log Analytics フォワーダーを使用するようにエージェントを構成します。

インターネットに接続されていないサーバーまたはクライアントがある場合は、OMS Log Analytics フォワーダーを使用することで、該当するサーバーまたはクライアントから OMS にデータを送信することができます。フォワーダーを使用した場合、エージェントからのデータはすべて、インターネットにアクセスできる単一のサーバーを介して送信されます。フォワーダーは、エージェントから OMS にデータを直接転送し、転送するデータについては分析を行いません。

セットアップや構成などのフォワーダーの詳細については、「[OMS Log Analytics Forwarder (OMS Log Analytics フォワーダー)](https://blogs.technet.microsoft.com/msoms/2016/03/17/oms-log-analytics-forwarder)」を参照してください。

プロキシ サーバー (この場合は、OMS フォワーダー) を使用するようにエージェントを構成する方法については、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

## 必要に応じてプロキシとファイアウォール設定を構成する
インターネットへのアクセスを制限しているプロキシ サーバーまたはファイアウォールが環境内に存在する場合、エージェントが OMS サービスと通信できるようにするには、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

## 次のステップ

- 機能を追加し、データを収集する方法については、「[Add Log Analytics solutions from the Solutions Gallery (ソリューションギャラリーから Log Analytics ソリューションを追加する)](log-analytics-add-solutions.md)」を参照してください。
- エージェントが Log Analytics サービスと通信できるように組織がプロキシ サーバーまたはファイアウォールを使用している場合、「[Configure proxy and firewall settings in Log Analytics (Log Analytics のプロキシとファイアウォールの設定を構成する)](log-analytics-proxy-firewall.md)」を参照してください。

<!---HONumber=AcomDC_0504_2016-->