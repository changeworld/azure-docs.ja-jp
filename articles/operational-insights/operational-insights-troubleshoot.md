<properties
   pageTitle="Operational Insights での問題のトラブルシューティング"
   description="Operational Insights での問題のトラブルシューティングについて説明します"
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
   ms.date="09/10/2015"
   ms.author="banders" />

# Operational Insights での問題のトラブルシューティング

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

以降のセクションの情報は、問題のトラブルシューティングにお役立てください。該当する問題がこの記事に見つからない場合は、[Operational Insights チームのブログ](http://blogs.technet.com/b/momteam/archive/2014/05/29/advisor-error-3000-unable-to-register-to-the-advisor-service-amp-onboarding-troubleshooting-steps.aspx)で探してください。

## SQL 評価のアクセス許可問題の解決
Operations Management Suite (OMS) は Microsoft Monitoring Agent 管理グループと Operations Manager 管理グループを利用してデータを回収し、OMS サービスに送信します。SQL Server など、特定のワークロードでは、ドメイン アカウントなど、セキュリティ状況が異なる場合、データを回収するためにワークロード固有の特権が必要となります。Microsoft Monitoring Agent が System Center Operations Manager 経由で接続されるとき、データ回収でアクセス許可問題が発生するようであれば、実行アカウントを構成し、資格情報を指定する必要があります。

SQL Server 管理パックを既に使用している場合、その Windows アカウントを実行アカウントとして使用する必要があります。

### オペレーション コンソールで SQL の実行アカウントを構成するには

1. Operations Manager でオペレーション コンソールを開き、**[管理]** をクリックします。

2. **[実行アカウントの構成]** の **[プロファイル]** をクリックし、**[Microsoft System Center Advisor の実行プロファイル]** を開きます。

3. **[実行アカウント]** ページの **[追加]** をクリックします。

4. SQL Server に必要な資格情報を含んだ Windows 実行アカウントを選択するか、**[新規]** をクリックして新たに作成します。

    >[AZURE.NOTE]実行アカウントの種類は Windows であることが必要です。さらに、SQL Server インスタンスをホストするすべての Windows Server 上のローカルの Administrators グループに、その実行アカウントが属している必要があります。

5. **[保存]** をクリックします。

6. 次の T-SQL サンプルに変更を加えて、各 SQL Server インスタンスで実行します。実行アカウントで SQL の評価を行うために必要な最低限の権限が付与されます。ただし、実行アカウントが既に SQL Server インスタンスの sysadmin サーバー ロールに属している場合、この作業は不要です。

```
---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

### To configure the SQL Run As account using Windows PowerShell
Alternatively, you can use the following PowerShell script to set the SQL Run As account. Open a PowerShell window and run the following script after you’ve updated it with your information:

```

    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"
     
    $profile = Get-SCOMRunAsProfile -DisplayName "Operational Insights SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```
After the PowerShell Script finishes executing, perform the T-SQL commands provided above.

## Diagnose connection issues for Operational Insights

Because Microsoft Azure Operational Insights relies on data that is moved to and from the cloud, connection issues can be crippling. Use the following information to understand and solve your connection issues.


**Error message:** The Internet connectivity was verified, but connection to Operational Insights service could not be established. Please try again later.

**Possible causes:**
- The Operational Insights service is under maintenance. Wait until the Operational Insights maintenance is done.
- Your network has blocked Operational Insights. Contact your network administrator and request access to Operational Insights, or use another server as your gateway.

**Error message:** Internet connection could not be established. Please check your proxy settings.

**Possible causes:**
- This server is not connected to the Internet. Check the Internet connectivity status, and connect the server to the Internet.
- The proxy setting is not correct. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) for information about how to set or change your proxy settings.
- The proxy server requires authentication. See [Configure proxy and firewall settings](operational-insights-proxy-firewall.md) to learn about how to configure Operations Manager to use a proxy server.


## Troubleshoot SQL Server discovery

If you are running Microsoft SQL Server 2008 R2, and despite deploying the Operations Manager agent, you do not see alerts for this server, you might have a discovery issue.

To confirm if this is the source of your trouble, check for the following two issues:

- In the Operations Manager event log, you see Event ID 4001. This event indicates that there is an invalid class.

- In SQL Server Configuration Manager, when you view SQL Server Services, you see the error message, “The remote procedure call failed. [0x0800706be]”

If both issues are true, you need to install SQL Server 2008 R2 Service Pack 2. To download this service pack, see [SQL Server 2008 R2 Service Pack 2](http://go.microsoft.com/fwlink/?LinkId=271310) in the Microsoft Download Center.

After you install the service pack, you should see Operational Insights data for the server within 24 hours.

## Troubleshoot agents or Operations Manager data flow to Operational Insights

The following set of procedures is meant as a guide to help you troubleshoot your directly-connected agents or Operations Manager deployments configured to report data to Azure Operational Insights.

### Procedure 1: Validate if the right Management Packs get downloaded to your Operations Manager Environment
>[AZURE.NOTE] If you only use Direct Agent, you can skip to the next procedure.

Depending on which solutions (previously called intelligence packs) you have enabled from the OpInsights Portal will you see more or less of these MPs. Search for keyword ‘Advisor’ or ‘Intelligence’ in their name.
You can check for these MPs using OpsMgr PowerShell:

```Powershell
Get-SCOMManagementPack | where {$\_.DisplayName -match 'Advisor'} | Select Name,Sealed,Version Get-SCOMManagementPack | where {$\_.Name -match 'IntelligencePacks'} | Select Name,Sealed,Version ```

>[AZURE.NOTE]容量ソリューションのトラブルシューティングを行う場合、‘capacity’ を含む名前の管理パックが*いくつあるか* 確認してください。同じ管理パック バンドルに含まれる管理パックで、同じ表示名を持つ (ただし内部 ID は異なる) ものが 2 つあります。この 2 つの管理パックのうち 1 つがインポートされていない場合 (VMM 依存関係の不足による場合が多い)、もう 1 つの管理パックはインポートされず、インポート操作の再試行も行われません。

「容量」に関係する次の 3 つの管理パック (Microsoft System Center Advisor Capacity Intelligence Pack、Microsoft System Center Advisor Capacity Intelligence Pack、Microsoft System Center Advisor Capacity Storage Data) が表示されます。

このうち 3 つではなく 1 つまたは 2 つしか表示されない場合、それらを削除してから、Operations Manager が再度ダウンロードとインポートを実行するまで 5 ～ 10 分待ちます。この間に、イベント ログでエラーを確認します。

### 手順 2: 直接エージェントに正しいインソリューションがダウンロードされたかどうかを検証する
>[AZURE.NOTE]Operations Manager のみを使用している場合、この手順は無視することができます。

直接エージェントでは、ソリューションの収集ポリシーが **C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\Health Service State\\Management Packs** にキャッシュされています。


### 手順 3: Advisor サービスにデータが送信されているかどうか (または、少なくとも送信が試行されているかどうか) を検証する
直接接続のエージェントと Operations Manager のどちらを使用するかに応じて、直接エージェント コンピューターまたは Operations Manager 管理サーバー上で次の手順を実施できます。

1. - [パフォーマンス モニター] を開く
2. - [Health Service Management Groups] を選択する
3. - ‘HTTP’ から始まるすべてのカウンターを追加します。

構成が正しく行われると、イベントやその他のデータ項目が (ポータルに搭載されたソリューションと構成済みのログ収集ポリシーに基づき) アップロードされ、追加したカウンターのアクティビティが表示されます。これらのカウンターは、継続的に「ビジー」になっている必要はありません。アクティビティがほとんど、またはまったく表示されない場合は、それほど多くのソリューションを使用していないか、収集ポリシーが非常に軽量であることを表しています。

### 手順 4: 管理サーバーまたは直接エージェントのイベント ログでエラーを確認する
最後の手順として、これまでの手順がすべて失敗に終わり、サービスによって何のデータも受信されていない場合、[**イベント ビューアー**] でエラーが表示されるかどうかを確認します。

[**イベント ビューアー**] –>[ **Application and Services**]–> [**Operations Manager**] の順で開き、イベント ソース (**Advisor**、**Health Service Modules**、**HealthService** と **Service Connector**) 別にフィルター処理します。この最後のは直接エージェントのみに適用されます。

これらのイベントのほとんどは、Operations Manager と直接エージェントのどちらでも同様であり、トラブルシューティングの手順についても違いはありません。Operations Manager と直接エージェントの間で唯一異なる部分は、登録プロセス (Operations Manager では GUI、直接エージェントではワークスペース ID とキーの組み合わせ) ですが、初期登録後は証明書が交換されて使用されるので、サービスとの通信に関連するもの以外はすべて同様になります。

そのため、これらのイベントの多くは両方のレポート インフラストラクチャに適用されます。以下は、表示される共通のイベントの一覧です。

### ソース ’Health Service Modules’ のイベント
##### EventID 2138
これは、プロキシに認証が必要なことを意味します。[プロキシ サーバーの構成](https://msdn.microsoft.com/library/azure/dn884643.aspx)のための手順に従ってください

##### EventID 2137
Operations Manager で認証証明書を読み取ることができません。Advisor 登録ウィザードを再度実行することで、証明書および RunAs アカウントが修復されます

##### EventID 2132
これは、**承認されていない**ことを意味します。証明書かサービスへの登録、またはその両方に問題がある可能性があります。登録ウィザードを再度実行すると、証明書および RunAs アカウントが修復されます。さらに、プロキシが除外を許可するように設定されていることを確認します。詳細については、[プロキシ サーバーの構成](https://msdn.microsoft.com/library/azure/dn884643.aspx)に関するページを参照してください

##### EventID 2129
これは、SSL ネゴシエーションの失敗による接続失敗です。SSL ではなく、TLS を使用するようにシステムが構成されていることを確認します。Internet Explorer の**[詳細設定]**オプションか、Windows レジストリの以下のキーで、暗号化に関するこのサーバーの SSL 設定の一部が不適切である可能性があります。

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL

##### EventID 2127
データの送信に失敗し、エラー コードを受信しました。たまに発生するだけという場合は、単なる誤作動の可能性があります。注意を続け、どのくらいの頻度で発生するか把握してください。高頻度で発生 (長時間にわたり、10 分ほどの間隔で発生) する場合、本当の問題である可能性があります。ネットワーク構成と上記のプロキシ設定を確認し、登録ウィザードを再度実行してください。ただし、散発的にしか発生 (1 日に 2 ～ 3 回など) しない場合、データはキューに登録されて再送信されるので問題ありません。発生頻度がこの程度であれば、単なるネットワーク タイムアウトの可能性が高いと考えられます。

一部の HTTP エラー コードには、特別な意味を持つものがあります。

- たとえば、MMA 直接エージェントまたは管理サーバーが初めてサービスにデータを送信しようとすると、見つからないという意味の内部 404 エラー コードを持つ 500 エラーが発生します。これは、新しいワークスペースに使用するストレージがプロビジョニングされている途中であり、まだ準備ができていないことを示しています。ただし、次の送信時には準備が完了し、通常の条件でフローが開始されます。
- 403 は、アクセス許可や資格情報の問題などを示している場合があります。

##### EventID 2128
DNS 名前解決に失敗しました。サーバーが、データの送信先としてサポートされているインターネット アドレスを解決できません。この原因としては、コンピューターの DNS リゾルバー設定、プロキシ設定の間違い、プロバイダー側での (一時的な) DNS の問題などが考えられます。前のイベントと同様に、常に発生するか、たまに発生するかによって、本当の問題かそうでないかが決まります。

##### EventID 2130
タイムアウト。前のイベントと同様に、常に発生するか、たまに発生するかによって、問題かそうでないかが決まります。

### ソース 'HealthService' からのイベント
##### EventID 4511
モジュール "System.PublishDataToEndPoint" を読み込むことはできません。– ファイルが見つかりません。タイプ "System.PublishDataToEndPoint」(CLSID"{D407D659-65E4-4476-BF40-924E56841465}") のモジュールの初期化が失敗し、エラー コードが表示されました。指定されたファイルが見つかりません。このエラーは、コンピューター内の DLL が古く、必要なモジュールが含まれていないことを示しています。これを修正するには、管理サーバーを最新の更新プログラムのロールアップに更新します。

##### EventID 4502
モジュールがクラッシュしました。**CollectInstanceSpace** や **CollectTypeSpace** などの名前を持つワークフローに対してこのイベントが表示された場合、サーバーによる一部の構成データの送信に問題があることを意味する場合があります。常に発生するか、たまに発生するかによって、これが問題かそうでないかが決まります。1 時間に 1 回を超える頻度で発生する場合は、明らかに問題です。この操作が 1 日に 1 ～ 2 回だけ失敗するという場合は正常であり、回復できます。モジュールがどのように失敗したか (詳細を含む説明が表示されます) によって、これはオンプレミスの問題である可能性があります。つまり、データベースへのデータ収集や、クラウドへの送信の問題などです。ネットワークとプロキシの設定を確認し、それでも解決しない場合は HealthService の再起動を試してみてください。

##### EventID 4501
"System.PublishDataToEndPoint" モジュールがクラッシュしました。管理グループ "SCOMTEST" のインスタンス "Operations Manager Management Group" (ID "{6B1D1BE8-EBB4-B425-08DC-2385C5930B04}") で実行中のルール "Microsoft.SystemCenter.CollectAlertChangeDataToCloud" の一部として実行中だったタイプ "System.PublishDataToEndPoint" のモジュールでエラー 87L が報告されました。今後、このワークフロー、モジュール、エラーの組み合わせのイベントが表示されることは*ありません*。これは、[*修正済みの*バグ](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6714689-alert-management-intelligence-pack-not-sending-ale)です。これがまた表示された場合は、お好みの Microsoft サポート窓口を通じて報告してください。


### ソース ' Service Connector' からのイベント
##### EventID 4002
クエリに対してサービスから HTTP 状態コード 403 が返されました。サービスの正常性についてサービス管理者に確認してください。クエリは後で再試行されます。エージェントの初期登録フェーズ中にエラー コード 403 が発生する場合があります。その場合、https://<YourWorkspaceID>.oms.opinsights.azure.com/AgentService.svc/AgentTopologyRequest のような URL が表示されます。エラー コード 403 は "禁止" を意味します。– これは通常、誤ってコピーされたワークスペース ID、ワークスペース キー、またはクロックがコンピューター上で同期していないということです。信頼できるタイム ソースで同期を試し、Microsoft Monitoring Agent のコントロール パネル アプレットで接続チェックを使用して、正しいワークスペース ID とキーがあることを確認してください。


### 手順 5: エージェントを見つけてそのデータを送信し、ポータルでインデックスを作成する
オペレーション インサイト ポータルにログインし、[概要] ページから小さなタイル **[サーバーと使用量]** に移動します。これには、管理グループ (とそのエージェント) および直接エージェントがデータをログ検索にレポートしているかどうかが表示されます。タイルのエージェント数はデータから算出されるため、コンピューターが 2 週間にわたりレポートを実行しないと、この数は不明になります。

ドリル ダウンによってログ検索のページまで移動すると、コンピューターごとに、最後にインデックスが作成されたデータのタイムスタンプが表示されます。そこから、データの詳細について調査することができます。データ収集量の構成や、どのソリューションを選ぶかによって、データのアップロード スケジュールとスピードが異なります。

このページには、ソリューションごとに分類されたサービスへのデータ送信量に関する情報の測定機能も備えています (これには、ログ検索インデックスではなく課金システムが使用され、2 ～ 3 時間おきに更新されます)。

<!---HONumber=Sept15_HO3-->