<properties 
   pageTitle="connect-scom" 
   description="オペレーション インサイトにコンピューターを直接接続する" 
   services="operational-insights" 
   documentationCenter="" 
   authors="lauracr" 
   manager="jwhit" 
   editor=""/>

<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="02/20/2015"
   ms.author="lauracr"/>

# オペレーション インサイトにコンピューターを直接接続する 

追加する各コンピューターにオペレーション インサイト エージェントをインストールすると、オペレーション インサイトにコンピューターを直接接続できます。 


##エージェントのダウンロード、インストール、および構成
###エージェントのセットアップ ファイルをダウンロードするには
1. **オペレーション インサイト ポータル**にある **[概要]** ページで、**[サーバーと利用状況]** をクリックします。
1. **[直接接続されているサーバー]** の **[構成]** をクリックします。
1. **[エージェントの追加]** の横で、エージェントのリンクをクリックしてセットアップ ファイルをダウンロードします。
1. **[プライマリ ワークスペース キー]** ボックスでキーを選択してコピーします (Ctrl キー + C)。


### セットアップを使用してエージェントをインストールするには
1. セットアップを実行して、管理対象のコンピューターにエージェントをインストールします。
1. **[Microsoft Azure オペレーション インサイトにエージェントを接続]** を選択してから **[次へ]** をクリックします。
1. プロンプトが表示されたら、手順 4. でコピーした情報を入力します。
1. 完了すると、**コントロール パネル**に **Microsoft 管理エージェント**が表示されます。

### コマンド ラインを使用してエージェントをインストールするには
コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。

    MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"

## Microsoft Monitoring Agent の再構成 (省略可能)
次の情報を使用して、エージェントが Microsoft Azure オペレーション インサイト サービスと直接通信できるようにします。エージェントを構成すると、エージェントはエージェント サービスに登録され、必要な構成情報と、インテリジェンス パックの情報が含まれている管理パックを取得します。

エージェントで管理されているコンピューターからデータが収集されると、監視されているコンピューターの数がオペレーション インサイト ポータルの **[直接接続されたエージェント]** の [使用状況] ページに表示されます。オペレーション インサイト ポータルでは、データを送信するすべてのコンピューターのデータと評価情報を確認できます。

エージェントは、必要に応じて無効にすることや、コマンド ラインまたはスクリプトを使用して有効にすることもできます。

### エージェントを構成するには
- **Microsoft Monitoring Agent** をインストールした後、**コントロール パネル**を開きます。
- Microsoft Monitoring Agent を開いてから、[Azure オペレーション インサイト] タブをクリックします。
- **[Azure オペレーション インサイトに接続する]** チェック ボックスをオンにします。
- **[ワークスペース ID]** ボックスに、オペレーション インサイト ポータルで提供されたワークスペース ID を入力します。
- [アカウント キー] ボックスに、エージェントをインストールするときにコピーした**プライマリ ワークスペース キー**を貼り付けて、**[OK]** をクリックします。

### エージェントを無効にするには
- エージェントをインストールした後、**コントロール パネル**を開きます。
- Microsoft Monitoring Agent を開いてから、**[Azure オペレーション インサイト]** タブをクリックします。
- **[Azure オペレーション インサイトに接続する]** チェック ボックスをオフにします。

### コマンド ラインまたはスクリプトを使用してエージェントを有効にするには
次の例の Windows PowerShell または VB スクリプトを使用できます。
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
    $healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
    $healthServiceSettings.ReloadConfiguration()
    



### プロキシとファイアウォール設定の構成 (省略可能)
環境内のプロキシ サーバーまたはファイアウォールによってインターネットへのアクセスが制限されている場合、次の手順に従って、Operations Manager またはエージェントがオペレーション インサイト サービスと通信できるようにすることが必要になる場合があります。 



- [プロキシとファイアウォール設定の構成 (省略可能)](https://msdn.microsoft.com/library/azure/dn884643.aspx) 


## 関連コンテンツ

- [ブログの投稿: オペレーション インサイトにサーバーを直接接続する](http://blogs.technet.com/b/momteam/archive/2015/01/20/connect-servers-directly-to-operational-insights.aspx)
- [ブログの投稿: Azure の仮想マシンでオペレーション インサイトを有効にする](http://azure.microsoft.com/updates/easily-enable-operational-insights-for-azure-virtual-machines/)



<!--HONumber=52-->