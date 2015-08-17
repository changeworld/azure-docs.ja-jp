<properties
	pageTitle="オペレーション インサイトにコンピューターを直接接続する"
	description="追加する各コンピューターに Operational Insights エージェントをインストールすると、Operational Insights にコンピューターを直接接続できます。"
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/02/2015" 
	ms.author="banders"/>
# オペレーション インサイトにコンピューターを直接接続する

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

追加する各コンピューターに Operational Insights エージェントをインストールすると、Operational Insights にコンピューターを直接接続できます。

> [AZURE.TIP]仮想マシンを Azure で実行する場合、「[Microsoft Azure でサーバーのデータを分析する](operational-insights-analyze-data-azure.md)」に記載された手順に従ってエージェントをインストールします。

## エージェントのダウンロードとインストール
次の手順に従って、Operational Insights エージェントをダウンロードしてインストールします。

### エージェントのセットアップ ファイルをダウンロードするには
1. Operational Insights ポータルにある **[概要]** ページで、**[設定]** タイルをクリックします。上部の **[接続されたソース]** タブをクリックします。![設定ページ](./media/operational-insights-direct-agent/direct-agent01.png)
2. **[サーバーの直接接続 (64 ビット)]** で、[エージェントのダウンロード] ボタンをクリックし、セットアップ ファイルをダウンロードします。
3. **[ワークスペース ID]** の右で、コピー アイコンをクリックし、ID をメモ帳に貼り付けます。
4. **[ワークスペース ID]** の右側で、コピー アイコンをクリックし、ID をメモ帳に貼り付けます。![設定ページ](./media/operational-insights-direct-agent/direct-agent02.png)

### セットアップを使用してエージェントをインストールするには
1. セットアップを実行して、管理対象のコンピューターにエージェントをインストールします。
2. **[Microsoft Azure オペレーション インサイトにエージェントを接続]** を選択してから **[次へ]** をクリックします。
3. メッセージが表示されたら、前の手順でメモ帳にコピーした**ワークスペース ID** と**プライマリ キー**を入力します。

4. **[次へ]** をクリックします。エージェントが Operational Insights に接続できることが検証されます。
5. 完了すると、**コントロール パネル**に **Microsoft 管理エージェント**が表示されます。

### コマンド ラインを使用してエージェントをインストールするには
- コマンド ラインを使用してエージェントをインストールする場合は、次の例を変更してから使用してください。```MMASetup-AMD64.exe /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"```

## Microsoft Monitoring Agent の構成 (省略可能)
次の情報を使用して、エージェントが Microsoft Azure オペレーション インサイト サービスと直接通信できるようにします。エージェントを構成すると、エージェントはエージェント サービスに登録され、必要な構成情報と、ソリューションの情報が含まれている管理パックを取得します。

エージェントで管理されているコンピューターからデータが収集されると、監視されているコンピューターの数が Operational Insights ポータルの **[サーバーの直接接続 (64 ビット)]** の **[設定]** の**[接続されたソース]** タブに表示されます。オペレーション インサイト ポータルでは、データを送信するすべてのコンピューターのデータと評価情報を確認できます。

エージェントは、必要に応じて無効にすることや、コマンド ラインまたはスクリプトを使用して有効にすることもできます。

### エージェントを構成するには
1. **Microsoft Monitoring Agent** をインストールした後、**コントロール パネル**を開きます。
2. Microsoft Monitoring Agent を開いてから、[Azure オペレーション インサイト] タブをクリックします。
3. **[Azure オペレーション インサイトに接続する]** チェック ボックスをオンにします。
4. **[ワークスペース ID]** ボックスに、Operational Insights ポータルからのワークスペース ID を貼り付けます。
5. **[アカウント キー]** ボックスに Operational Insights ポータルの**プライマリ キー**を貼り付けてから、**[OK]** をクリックします。

### エージェントを無効にするには
1. エージェントをインストールした後、**コントロール パネル**を開きます。
2. Microsoft Monitoring Agent を開いてから、**[Azure オペレーション インサイト]** タブをクリックします。
3. **[Azure オペレーション インサイトに接続する]** チェック ボックスをオフにします。

### コマンド ラインまたはスクリプトを使用してエージェントを有効にするには
- 次の例の Windows PowerShell または VB スクリプトを使用できます。

```powershell
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$healthServiceSettings.EnableOnlineMonitoring('workspacename', 'workspacekey')
$healthServiceSettings.ReloadConfiguration()
```

## プロキシとファイアウォール設定の構成 (省略可能)
環境内のプロキシ サーバーまたはファイアウォールによってインターネットへのアクセスが制限されている場合、次の手順に従って、Operations Manager またはエージェントが Operational Insights サービスと通信できるようにすることが必要になる場合があります。

- [プロキシとファイアウォール設定の構成 (省略可能)](operational-insights-proxy-firewall.md)

<!---HONumber=August15_HO6-->