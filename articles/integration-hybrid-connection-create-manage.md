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
	ms.date="02/17/2015" 
	ms.author="mandia"/>


#ハイブリッド接続の作成と管理

このトピックでは、Azure ハイブリッド接続の作成および管理の手順を示します。 

内部設置型リソースに接続するには、以下の手順に従います。

1. [ハイブリッド接続を作成する](#CreateHybridConnection)ために、プライベート ネットワークの内部設置型リソースのホスト名または IP アドレスを指定します。

2.	[Azure Web サイトまたは Azure モバイル サービスをハイブリッド接続に関連付ける](#LinkWebSite)。

内部設置型リソースに 3. [Hybrid Connection Manager をインストール](#InstallHCM)し、特定のハイブリッド接続に接続する。Azure ポータルでは 1 回のクリックでインストールと接続を実行できます。

4. [ハイブリッド接続を管理](#ManageHybridConnection)し、その接続キーを管理します。


##<a name="CreateHybridConnection"></a>ハイブリッド接続を作成する

ハイブリッド接続は、Azure 管理ポータルで Web サイトと BizTalk サービスの**いずれか**を使用して作成できます。 

**Web サイトを使用してハイブリッド接続を作成する**には、「[ハイブリッド接続を使用して Azure の Web サイトを内部設置型のリソースに接続する](http://go.microsoft.com/fwlink/p/?LinkId=397538)」を参照してください。

**BizTalk サービスでハイブリッド接続を作成する**には、以下の手順に従います。

1. [Azure の管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。 
<br/>既存の BizTalk サービスがない場合は、[BizTalk サービスを作成](http://go.microsoft.com/fwlink/p/?LinkID=329870)できます。
3. [ハイブリッド接続] タブを選択します。
<br/>
![Hybrid Connections Tab][HybridConnectionTab]

4. **[ハイブリッド接続の作成]** を選択するか、タスク バーの **[追加]** ボタンを選択します。次のように入力します。

	<table border="1">
    <tr>
       <td><strong>名前</strong></td>
        <td>ハイブリッド接続名は一意である必要があり、BizTalk サービスと同じ名前にすることはできません。任意の名前を入力できますが、目的を具体的に示した名前にしてください。たとえば、以下のような名前です。<br/><br/>
		Payroll<em>SQLServer</em><br/>
		SupplyList<em>SharepointServer</em><br/>
		Customers<em>OracleServer</em>
        </td>
    </tr>
    <tr>
        <td><strong>ホスト名</strong></td>
        <td>内部設置型リソースの完全修飾ホスト名、ホスト名のみ、または、IPv4 アドレスを入力します。たとえば、以下のような名前です。
        <br/><br/>
<em>mySQLServer</em>
<br/>
<em>mySQLServer</em>.<em>Domain</em>.corp.<em>yourCompany</em>.com
<br/>
<em>myHTTPSharePointServer</em>
<br/>
<em>myHTTPSharePointServer</em>.<em>yourCompany</em>.com
<br/>
10.100.10.10
       </td>
    </tr>
	<tr>
        <td><strong>Port</strong></td>
        <td>内部設置型リソースのポート番号を入力します。たとえば、Web サイトを使用している場合、ポート 80 またはポート 443 を入力します。SQL Server を使用している場合は、ポート 1433 を入力します。</td>
	</tr>
	</table>


5. チェック マークを選択します。 

####その他

- 複数のハイブリッド接続を作成することができます。使用できる接続の数については、「[BizTalk サービス: エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)」を参照してください。 
- 各ハイブリッド接続は、接続文字列のペアと共に作成されます。送信 (SEND) 用のアプリケーション キーと、リッスン (LISTEN) 用の内部設置型キーです。各ペアにはプライマリ キーとセカンダリ キーが含まれています。 


##<a name="LinkWebSite"></a>Azure Web サイトまたは Azure モバイル サービスの関連付け

既存のハイブリッド接続に Azure Web サイトを関連付けるには、ハイブリッド接続ブレードで **[既存のハイブリッド接続を使用する]** を選択します。「[ハイブリッド接続を使用して Azure の Web サイトを内部設置型のリソースに接続する](http://go.microsoft.com/fwlink/p/?LinkId=397538)」を参照してください。

既存のハイブリッド接続に Azure モバイル サービスを関連付けるには、モバイル サービスの変更時または作成時に、**[ハイブリッド接続の追加]** を選択します。「[ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)」を参照してください。


##<a name="InstallHCM"></a>内部設置型への Hybrid Connection Manager のインストール

ハイブリッド接続の作成後、内部設置型リソースに Hybrid Connection Manager をインストールします。Hybrid Connection Manager は Azure Web サイトまたは BizTalk サービスからダウンロードできます。BizTalk サービスの手順は以下のとおりです。 

1. [Azure の管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。 
3. **[ハイブリッド接続]** タブを選択します。
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. タスク バーで **[内部設置型設定]** をクリックします。
<br/>
![On-Premises Setup][HCOnPremSetup]
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

- Hybrid Connection Manager は、承認のために有効な内部設置型接続文字列を使用する必要があります。Azure Web サイトまたはモバイル サービスは、承認のために有効なアプリケーション接続文字列を使用する必要があります。


##<a name="ManageHybridConnection"></a>ハイブリッド接続の管理
ハイブリッド接続を管理するために、以下のことを実行できます。

- Azure ポータルを使用して BizTalk サービスに移動する。 
- [REST API](http://msdn.microsoft.com/library/azure/dn232347.aspx) を使用する。

####ハイブリッド接続文字列をコピー、再生成する

1. [Azure の管理ポータル](http://go.microsoft.com/fwlink/p/?LinkID=213885)にサインインします。
2. 左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。 
3. **[ハイブリッド接続]** タブを選択します。
<br/>
![Hybrid Connections Tab][HybridConnectionTab]
4. [ハイブリッド接続] を選択します。タスク バーで **[接続の管理]** を選択します。
<br/>
![Manage Options][HCManageConnection]
<br/>
**[接続の管理]** でアプリケーション接続文字列と内部設置型接続文字列が一覧表示されます。接続文字列をコピーすることも、接続文字列で使用するアクセス キーを再生成することもできます。 
<br/>
<br/>
**[再生成]** を選択すると、接続文字列内で使用される共有アクセス キーが変更されます。以下の手順を実行します。
- Azure 管理ポータルで、Azure アプリケーションの **[キーの同期]** を選択します。
- **[内部設置型設定]** を再実行します。[内部設置型設定] を再実行すると、更新されたプライマリ接続文字列が内部設置型リソースによって使用されるように自動的に構成されます。


####ハイブリッド接続で使用する内部設置型リソースを管理するグループ ポリシーの使用

1. Hybrid Connection Manager の管理用テンプレートをダウンロードします。
2. ファイルを解凍します。
3. グループ ポリシーを変更するコンピューターで、以下の手順を実行します。 
- .ADMX ファイルを *%WINROOT%\PolicyDefinitions* フォルダーにコピーします。
- .ADML ファイルを *%WINROOT%\PolicyDefinitions\ja-jp* フォルダーにコピーします。

コピーが終了すると、グループ ポリシー エディターを使用してポリシーを変更できます。




## 次へ

- [Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)](http://go.microsoft.com/fwlink/p/?LinkId=397538)
- [ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する](http://go.microsoft.com/fwlink/?LinkID=397979)
- [Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started)
- [ハイブリッド接続の概要](http://azure.microsoft.com/documentation/articles/integration-hybrid-connection-overview)


## 関連項目

- [Windows Azure での BizTalk サービス管理の REST API に関するページ](http://msdn.microsoft.com/library/azure/dn232347.aspx)
- [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>


[HybridConnectionTab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png

<!--HONumber=46--> 
