<properties
   pageTitle="Operational Insights のプロキシとファイアウォール設定の構成"
   description="Operational Insights で使用するエージェントの種類を構成するために必要なプロキシとファイアウォールの設定について説明します"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/04/2015"
   ms.author="banders" />

# Operational Insights のプロキシとファイアウォール設定の構成

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Operations Manager やそのエージェントと使用する場合と、サーバーに直接接続する Microsoft Monitoring Agent を使用する場合では、Operational Insights のプロキシとファイアウォールの設定に必要な操作が異なります。使用するエージェントの種類については、以下のセクションを確認してください。

## Microsoft Monitoring Agent でプロキシとファイアウォールの設定を構成する

Operational Insights サービスに Microsoft Monitoring Agent を接続して登録するには、ドメインのポート番号と URL へのアクセスが必要になります。エージェントと Operational Insights サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。インターネットへのアクセスを制限するためにファイアウォールを使用する場合は、Operational Insights へのアクセスを許可するようにファイアウォールを構成する必要があります。次の表には、Operational Insights で必要なポートの一覧を示しています。

|**エージェントのリソース**|**ポート**|
|--------------|-----|
|*.ods.opinsights.azure.com|ポート 443|
|*.oms.opinsights.azure.com|ポート 443|
|ods.systemcenteradvisor.com|ポート 443|
|*.blob.core.windows.net/*|ポート 443|

コントロール パネルから Microsoft Monitoring Agent のプロキシ設定を構成する際には、以下の手順を使用してください。この手順は、各サーバーに対して行う必要があります。構成が必要なサーバーの数が多い場合には、このプロセスを自動化するスクリプトを使った方が作業が簡単に済むことも考えられます。そのような場合は、1 つ先の手順の「*スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには*」をご覧ください。

### コントロール パネルを使って Microsoft Monitoring Agent のプロキシ設定を構成するには

1. **[コントロール パネル]** を開きます。

2. **[Microsoft Monitoring Agent]** を開きます。

3. **[プロキシ設定]** タブをクリックします。![proxy settings tab](./media/operational-insights-proxy-firewall/direct-agent-proxy.png)

4. 例に示したように、**[プロキシ サーバーを使用する]** をオンにして、URL と (必要に応じて) ポート番号を入力します。プロキシ サーバーで認証が必要な場合には、プロキシ サーバーにアクセスするためのユーザー名とパスワードを入力します。

次の手順に従って PowerShell スクリプトを作成し、プロキシ設定を設定して各エージェントがサーバーに直接接続できるようにします。

### スクリプトを使って Microsoft Monitoring Agent のプロキシ設定を構成するには


- 次の例をコピーして、使用する環境にあった情報を使って更新します。PS1 ファイル名の拡張子で保存して、Operational Insights サービスに直接接続する各コンピューターでスクリプトを実行します。


```
param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

# First we get the health service configuration object.  We need to determine if we
# have the right update rollup with the API we need.  If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}


Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username;


Write-Output "Setting Proxy to ${ProxyDomainName} with proxy username of (${ProxyUserName})."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

## Operations Manager でプロキシとファイアウォールの設定を構成する

Operational Insights サービスに Operations Manager 管理グループを接続し登録するには、ドメインのポート番号と URL へのアクセスが必要になります。Operations Manager 管理サーバーと Operational Insights サービス間の通信にプロキシ サーバーを使用する場合、適切なリソースにアクセスできることを確認する必要があります。インターネットへのアクセスを制限するためにファイアウォールを使用する場合は、Operational Insights へのアクセスを許可するようにファイアウォールを構成する必要があります。次の表では、これらのタスクに関連するポートを一覧を示します。

>[AZURE.NOTE]次のリソースの一部は Advisor について説明していますが、表示されたリソースは、今後変更される予定です。

|**管理サーバーのリソース**|**ポート**|
|--------------|-----|
|*.ods.opinsights.azure.com|ポート 443|
|service.systemcenteradvisor.com|ポート 443|
|scadvisor.accesscontrol.windows.net|ポート 443|
|scadvisorservice.accesscontrol.windows.net|ポート 443|
|*.blob.core.windows.net/*|ポート 443|
|data.systemcenteradvisor.com|ポート 443|
|ods.systemcenteradvisor.com|ポート 443|
|*.systemcenteradvisor.com|ポート 443|


|**Operational Insights と Operations Manager のコンソール リソース**|**ポート**|
|---|---|
|*.systemcenteradvisor.com|ポート 80 および 443|
|*.live.com|ポート 80 と 443|
|*.microsoftonline.com|ポート 80 および 443|
|login.windows.net|ポート 80 および 443|


次の手順に従って Operations Manager 管理グループを Operational Insights サービスに登録します。管理グループと Operational Insights サービス間で通信の問題が発生する場合、検証手順を使用して Operational Insights サービスへのデータ送信のトラブルシューティングを行います。

### Operational Insights サービスのエンドポイントの例外を要求するには

1. 前述の最初のテーブルからの情報を使用して、Operations Manager 管理サーバーに必要なリソースが、ファイアウォール経由でアクセスできることを確認します。

2. 前述の最初のテーブルからの情報を使用して、Operations Manager と Operations コンソールと Operational Insights に必要なリソースが、ファイアウォール経由でアクセスできることを確認します。

3. Internet Explorer でプロキシ サーバーを使用する場合は、正しく構成され、機能していることを確認します。確認するには、セキュリティで保護された Web 接続 (https) を開きます ([https://bing.com](https://bing.com) など)。セキュリティで保護された Web 接続がブラウザーで動作しない場合は、クラウドに Web サービスがある Operations Manager 管理コンソールでもおそらく作動しません。

### Operations Manager コンソールでプロキシ サーバーを構成するには

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。

2. **[Operational Insights]** を展開して、**[Operational Insights の接続]** をクリックします。![Operations Manager Operational Insights Connection](./media/operational-insights-proxy-firewall/proxy-om01.png)

3. [Operational Insights の接続] ビューで、**[プロキシ サーバーの構成]** をクリックします。![Operations Manager Operational Insights Connection Configure Proxy Server](./media/operational-insights-proxy-firewall/proxy-om02.png)

4. [Operational Insights 設定ウィザード: プロキシ サーバー] で **[プロキシ サーバーを使用して Operational Insights Web サービスにアクセスする]** を選択して、ポート番号と URL を入力します (例: **http://myproxy:80**)。![Operations Manager Operational Insights proxy address](./media/operational-insights-proxy-firewall/proxy-om03.png)


### プロキシ サーバーで認証が必要な場合の資格情報を指定するには

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。

2. **[RunAs Configuration (RunAs の構成)]** で **[Profiles (プロファイル)]** を選択します。

3. **System Center Advisor Run As Profile Proxy** というプロファイルを開きます。![image of the System Center Advisor Run As Proxy profile](./media/operational-insights-proxy-firewall/proxyacct1.png)
4. Run As Profile (Run As プロファイル) ウィザードで **[追加]** をクリックして Run As アカウント (実行アカウント) を使用します。新しい Run As アカウント(実行アカウント) を作成するか、既存のアカウントを使用できます。このアカウントには、プロキシ サーバーを通過するための十分な権限を持たせる必要があります。![image of the Run As Profile Wizard](./media/operational-insights-proxy-firewall/proxyacct2.png)

5. 管理するアカウントを設定するには、**[選択したクラス、グループ、またはオブジェクト]** をクリックして、[オブジェクトの検索] ボックスを開きます。![image of the Run As Profile Wizard](./media/operational-insights-proxy-firewall/proxyacct2-1.png)
6. 「**Operations Manager Management Servers**」を検索し、選択します。![image of the Object Search boxc](./media/operational-insights-proxy-firewall/proxyacct3.png)
7. **[OK]** をクリックして、[実行アカウントの追加] ボックスを閉じます。![image of the Run As Profile Wizard](./media/operational-insights-proxy-firewall/proxyacct4.png)
8. ウィザードを完了し、変更を保存します。![image of the Run As Profile Wizard](./media/operational-insights-proxy-firewall/proxyacct5.png)


### WinHTTP の各管理サーバーにプロキシ サーバーを構成するには

1. Operations Manager が Operations Manager 2012 R2 Update Rollup 3 や Operations Manager 2012 SP1 Update Rollup 7 以降に更新されていない場合、Operations Manager 管理サーバーで管理者としてコマンド プロンプト ウィンドウを開きます。それ以外の場合はこの手順を使用する必要はありません。

2. **netsh winhttp set proxy myproxy:80** と入力します。

3. コマンド プロンプト ウィンドウを閉じ、System Center Management サービス (HealthService) を再起動します。

4. 管理グループの各管理サーバーで、手順 2 を実行します。

### 各管理サーバーにプロキシ サーバーを構成するには

1. Operations Manager コンソールを開き、**[Administration (管理)]** ワークスペースを選択します。

2. **[Device Management (デバイスの管理)]** をクリックし、**[Management Servers (管理サーバー)]** をクリックします。

3. 各管理サーバーの名前を右クリックし、**[プロパティ]** をクリックして、**[プロキシの設定]** タブで情報を設定します。![Proxy Settings tab](./media/operational-insights-proxy-firewall/proxyms.png)

### Operational Insights の管理パックがダウンロードされることを確認するには

- Operational Insights を使用してソリューションを追加した場合、ソリューションは Operations Manager コンソールの **[Administration (管理)]** で管理パックとして表示できます。「*System Center Advisor*」と入力して検索すると、早く見つけることができます。![management packs downloaded](./media/operational-insights-proxy-firewall/mpdownloaded.png)
- また、Operations Manager 管理サーバーで次の Windows PowerShell コマンドを使用する方法でも、Operational Insights の管理パックを確認できます。

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken

        get-scommanagementpack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version | ft

### Operations Manager が Operational Insights サービスにデータを送信していることを確認するには

1. Operations Manager 管理サーバーで、パフォーマンス モニター (perfmon.exe) を開き、**[Performance Monitor (パフォーマンス モニター)]** を選択します。

2. **[追加]** をクリックして、**[Health Service Management Groups]** を選択します。

3. **HTTP** から始まるすべてのカウンターを追加します。![add counters](./media/operational-insights-proxy-firewall/sendingdata1.png)
4. Operations Manager の構成が適切であれば、Operational Insights と構成済みのログ収集ポリシーで追加した、管理パックに基づく Health Service Management カウンターのイベントやその他のデータ アイテムにおけるアクティビティが表示されます。![Performance Monitor showing activity](./media/operational-insights-proxy-firewall/sendingdata2.png)
 
<!--BugFixing Comment-->

<!---HONumber=July15_HO1-->