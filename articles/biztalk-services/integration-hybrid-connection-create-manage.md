<properties 
	pageTitle="ハイブリッド接続の作成と管理 | Azure" 
	description="ハイブリッド接続の作成、接続の管理、Hybrid Connection Manager のインストールの方法について説明します。MABS、WABS" 
	services="biztalk-services" 
	documentationCenter="" 
	authors="MandiOhlinger" 
	manager="dwrede" 
	editor="cgronlun"/>

<tags 
	ms.service="biztalk-services" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="mandia"/>


# ハイブリッド接続の作成と管理


## 手順の概要
1. プライベート ネットワークの内部設置型リソースのホスト名または IP アドレスを入力してハイブリッド接続を作成する。
2. ハイブリッド接続に Azure Web アプリまたは Azure モバイル アプリを関連付ける。
3. 内部設置型リソースに Hybrid Connection Manager をインストールし、特定のハイブリッド接続に接続する。Azure ポータルでは 1 回のクリックでインストールと接続を実行できます。
4. ハイブリッド接続を管理し、その接続キーを管理します。

このトピックでは次の手順を示します。


## <a name="CreateHybridConnection"></a>ハイブリッド接続の作成

ハイブリッド接続は、Azure 管理ポータルで Web アプリと BizTalk サービスの**いずれか**を使用して作成できます。

**Web サイトを使用してハイブリッド接続を作成する**には、「[Azure Web アプリを内部設置型リソースに接続する](../web-sites-hybrid-connection-get-started.md)」を参照してください。

**BizTalk サービスでハイブリッド接続を作成する**には、以下の手順に従います。

1. [Azure の管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。<br/>既存の BizTalk サービスがない場合は、[BizTalk サービスを作成できます](biztalk-provision-services.md)。
3. [ハイブリッド接続] タブを選択します。<br/> ![[ハイブリッド接続] タブ][HybridConnectionTab]

4. **[ハイブリッド接続の作成]** を選択するか、タスク バーの **[追加]** ボタンを選択します。次のように入力します。

	プロパティ | 説明
--- | ---
名前 | ハイブリッド接続名は一意である必要があり、BizTalk サービスと同じ名前にすることはできません。任意の名前を入力できますが、目的を具体的に示した名前にしてください。たとえば、次のようになります。<br/><br/>Payroll*SQLServer*<br/>SupplyList*SharepointServer*<br/>Customers*OracleServer*
ホスト名 | 内部設置型リソースの完全修飾ホスト名、ホスト名のみ、または、IPv4 アドレスを入力します。たとえば、次のようになります。<br/><br/>mySQLServer<br/>*mySQLServer*.*Domain*.corp.*yourCompany*.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*.*yourCompany*.com<br/>10.100.10.10
Port | 内部設置型リソースのポート番号を入力します。たとえば、Web アプリを使用している場合、ポート 80 またはポート 443 を入力します。SQL Server を使用している場合、ポート 1433 を入力します。

5. チェック マークをオンにしてセットアップを完了します。

#### その他

- 複数のハイブリッド接続を作成することができます。許可されている接続数については「[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](biztalk-editions-feature-chart.md)」を参照してください。 
- それぞれのハイブリッド接続は、送信用のアプリケーション キーとリッスン用の内部設置型キーで構成される接続文字列のペアと共に作成されます。各ペアにはプライマリ キーとセカンダリ キーが含まれています。 


## <a name="LinkWebSite"></a>Azure Web アプリまたは Azure モバイル アプリの関連付け

既存のハイブリッド接続に Azure Web アプリを関連付けるには、ハイブリッド接続ブレードで **[既存のハイブリッド接続を使用する]** を選択します。「[Azure Web アプリを内部設置型リソースに接続する](../web-sites-hybrid-connection-get-started.md)」を参照してください。

既存のハイブリッド接続に Azure モバイル アプリを関連付けるには、モバイル サービスの変更時または作成時に、**[ハイブリッド接続の追加]** を選択します。「[Connect to an on-premises SQL Server from Azure Mobile Services and Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)」を参照してください。


## <a name="InstallHCM"></a>内部設置型への Hybrid Connection Manager のインストール

ハイブリッド接続の作成後、内部設置型リソースに Hybrid Connection Manager をインストールします。Hybrid Connection Manager は Azure Web アプリまたは BizTalk サービスからダウンロードできます。BizTalk サービスの手順は以下のとおりです。

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。 
3. **[ハイブリッド接続]** タブを選択します。<br/> ![[ハイブリッド接続] タブ][HybridConnectionTab]
4. タスク バーで **[内部設置型設定]** をクリックします。<br/> ![On-Premises Setup][HCOnPremSetup]
5. **[インストールと構成]** を選択して、内部設置型システムで Hybrid Connection Manager を実行またはダウンロードします。 
6. チェック マークを選択して、インストールを開始します。 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
-->

#### その他
- ハイブリッド接続では、以下のオペレーティング システムにインストールされている内部設置型リソースがサポートされます。

	- Windows Server 2008 R2
	- Windows Server 2012
	- Windows Server 2012 R2


- Hybrid Connection Manager をインストールすると、以下のことが行われます。

	- プライマリ アプリケーション接続文字列が使用されるように、Azure がホストするハイブリッド接続が自動的に構成されます。 
	- プライマリ内部設置型接続文字列が使用されるように、内部設置型リソースが自動的に構成されます。

- Hybrid Connection Manager は、承認のために有効な内部設置型文字列を使用する必要があります。Azure Web アプリまたはモバイル アプリは、承認のために有効なアプリケーション接続文字列を使用する必要があります。


## <a name="ManageHybridConnection"></a>ハイブリッド接続の管理
ハイブリッド接続を管理するために、以下のことを実行できます。

- Azure ポータルを使用して BizTalk サービスに移動する。 
- [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) を使用する。

#### ハイブリッド接続文字列をコピー、再生成する

1. [Azure 管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。 
3. **[ハイブリッド接続]** タブを選択します。<br/> ![[ハイブリッド接続] タブ][HybridConnectionTab]
4. [ハイブリッド接続] を選択します。タスク バーで **[接続の管理]** を選択します。<br/> ![Manage Options][HCManageConnection] <br/> **[接続の管理]** でアプリケーション接続文字列と内部設置型接続文字列が一覧表示されます。接続文字列をコピーすることも、接続文字列で使用するアクセス キーを再生成することもできます。<br/> <br/> **[再生成] を選択すると**、接続文字列内で使用される共有アクセス キーが変更されます。以下の手順を実行します。
- Azure 管理ポータルで、Azure アプリケーションの **[キーの同期]** を選択します。
- **[内部設置型設定]** を再実行します。[内部設置型設定] を再実行すると、更新されたプライマリ接続文字列が内部設置型リソースによって使用されるように自動的に構成されます。


#### ハイブリッド接続で使用する内部設置型リソースを管理するグループ ポリシーの使用

1. [Hybrid Connection Manager の管理用テンプレート](http://www.microsoft.com/download/details.aspx?id=42963)をダウンロードします。
2. ファイルを解凍します。
3. グループ ポリシーを変更するコンピューターで、以下の手順を実行します。 

	- .ADMX ファイルを *%WINROOT%\PolicyDefinitions* フォルダーにコピーします。
	- .ADML ファイルを *%WINROOT%\PolicyDefinitions\ja-jp* フォルダーにコピーします。

コピーが終了すると、グループ ポリシー エディターを使用してポリシーを変更できます。




## 次へ

[Azure Web アプリを内部設置型リソースに接続する](../web-sites-hybrid-connection-get-started.md)<br/> [Azure Web アプリからオンプレミス SQL Server に接続する](../web-sites-hybrid-connection-connect-on-premises-sql-server.md)<br/> [Azure モバイル サービスとハイブリッド接続](../mobile-services-dotnet-backend-hybrid-connections-get-started.md)<br/> [ハイブリッド接続の概要](integration-hybrid-connection-overview.md)


## 関連項目

[Microsoft Azure の BizTalk サービスを管理するための REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx)<br/> [BizTalk サービス: エディションのチャート](biztalk-editions-feature-chart.md)<br/> [Azure 管理ポータルを利用した BizTalk サービスの作成](biztalk-provision-services.md)<br/> [BizTalk サービス: ダッシュボード タブ、モニター タブ、スケール タブ](biztalk-dashboard-monitor-scale-tabs.md)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png

<!---HONumber=62-->