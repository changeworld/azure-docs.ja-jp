<properties linkid="manage-services-integration-hybrid-connection" urlDisplayName="Create and Manage Hybrid Connections - BizTalk Services" pageTitle="Create and Manage Hybrid Connections | Azure" metaKeywords="BizTalk Services, BizTalk, web sites, website, hybrid connections, Azure" description="Learn how to create a hybrid connection, manage the connection, and install the Hybrid Connection Manager." metaCanonical="" services="integration-services" documentationCenter="" title="Create and Manage Hybrid Connections" authors="mandia" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="biztalk-services" ms.workload="integration" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="mandia" />

# ハイブリッド接続の作成と管理

このトピックでは、Azure ハイブリッド接続の作成および管理の手順を示します。

内部設置型リソースに接続するには、以下の手順に従います。

1.  プライベート ネットワークの内部設置型リソースのホスト名または IP アドレスを指定して[ハイブリッド接続を作成する][ハイブリッド接続を作成する]。

2.  ハイブリッド接続に [Azure Web サイトまたは Azure モバイル サービスを関連付ける][Azure Web サイトまたは Azure モバイル サービスを関連付ける]。

3.  内部設置型リソースに [Hybrid Connection Manager をインストール][Hybrid Connection Manager をインストール]し、特定のハイブリッド接続に接続する。Azure ポータルでは 1 回のクリックでインストールと接続を実行できます。

4.  [ハイブリッド接続を管理][ハイブリッド接続を管理]し、その接続キーを管理します。

## <a name="CreateHybridConnection"></a>ハイブリッド接続を作成する

ハイブリッド接続は、Azure 管理ポータルで Web サイトと BizTalk サービスの**いずれか**を使用して作成できます。

**Web サイトを使用してハイブリッド接続を作成する**には、「[Connect an Azure Website to an On-Premises Resource (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)][Connect an Azure Website to an On-Premises Resource (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]」を参照してください。

**BizTalk サービスでハイブリッド接続を作成する**には、以下の手順に従います。

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左側のナビゲーション ウィンドウで **[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。
    既存の BizTalk サービスがない場合は、[BizTalk サービスを作成][BizTalk サービスを作成]します。
3.  [ハイブリッド接続] タブを選択します。
<br/>
    ![Hybrid Connections Tab][Hybrid Connections Tab]

4.  **[ハイブリッド接続の作成]** を選択するか、タスク バーの **[追加]** ボタンを選択します。次のように入力します。

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
<td>内部設置型リソースの完全修飾ホスト名、ホスト名のみ、または、IPv4 アドレスを入力します。たとえば、次のようになります。
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

5.  チェック マークを選択します。

#### その他

-   複数のハイブリッド接続を作成することができます。許可されている接続数については「[BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート][BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]」を参照してください。
-   それぞれのハイブリッド接続は、送信用のアプリケーション キーとリッスン用の内部設置型キーで構成される接続文字列のペアと共に作成されます。各ペアにはプライマリ キーとセカンダリ キーが含まれています。

## <a name="LinkWebSite"></a>Azure Web サイトまたは Azure モバイル サービスの関連付け

既存のハイブリッド接続に Azure Web サイトを関連付けるには、ハイブリッド接続ブレードで **[既存のハイブリッド接続を使用する]** を選択します。「[Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)][Connect an Azure Website to an On-Premises Resource (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]」を参照してください。

既存のハイブリッド接続に Azure モバイル サービスを関連付けるには、モバイル サービスの変更時または作成時に、 **[ハイブリッド接続の追加]** を選択します。「[Connect to an on-premises SQL Server from Azure Mobile Services and Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)][Connect to an on-premises SQL Server from Azure Mobile Services and Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)]」を参照してください。

## <a name="InstallHCM"></a>内部設置型への Hybrid Connection Manager のインストール

ハイブリッド接続の作成後、内部設置型リソースに Hybrid Connection Manager をインストールします。Hybrid Connection Manager は Azure Web サイトまたは BizTalk サービスからダウンロードできます。BizTalk サービスの手順は以下のとおりです。

1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。
3.  **[ハイブリッド接続]** タブを選択します。<br />
    ![Hybrid Connections Tab][Hybrid Connections Tab]
4.  タスク バーで、**[内部設置型設定]** を選択します。<br />
    ![On-Premises Setup][On-Premises Setup]
5.  **[インストールと構成]** を選択して、内部設置型システムで Hybrid Connection Manager を実行またはダウンロードします。
6.  チェック マークを選択して、インストールを開始します。

<!-- You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:  1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps. 2. Download the Hybrid Connection Manager MSI file.  3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file.  4. Using Windows PowerShell, type:  > Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*”  -->

#### その他

-   ハイブリッド接続では、以下のオペレーティング システムにインストールされている内部設置型リソースがサポートされます。

    -   Windows Server 2008 R2
    -   Windows Server 2012
    -   Windows Server 2012 R2
-   Hybrid Connection Manager をインストールすると、以下のことが行われます。

    -   プライマリ アプリケーション接続文字列が使用されるように、Azure がホストするハイブリッド接続が自動的に構成されます。
    -   プライマリ内部設置型接続文字列が使用されるように、内部設置型リソースが自動的に構成されます。
-   Hybrid Connection Manager は、承認のために有効な内部設置型文字列を使用する必要があります。Azure Web サイトまたはモバイル サービスは、承認のために有効なアプリケーション接続文字列を使用する必要があります。

## <a name="ManageHybridConnection"></a>ハイブリッド接続の管理

ハイブリッド接続を管理するために、以下のことを実行できます。

-   Azure ポータルを使用して BizTalk サービスに移動する。
-   [REST API][REST API] を使用する。
    <!-- - Use Windows PowerShell cmdlets  **INSERT LINK**. -->

#### ハイブリッド接続文字列をコピー、再生成する
1.  [Azure 管理ポータル][Azure 管理ポータル]にサインインします。
2.  左側のナビゲーション ウィンドウで、**[BizTalk サービス]** をクリックし、BizTalk サービスを選択します。
3.  **[ハイブリッド接続]** タブを選択します。<br />
    ![Hybrid Connections Tab][Hybrid Connections Tab]
4.  [ハイブリッド接続] を選択します。タスク バーで **[接続の管理]** を選択します。<br />
    ![Manage Options][Manage Options]<br />
**[接続の管理]** でアプリケーション接続文字列と内部設置型接続文字列が一覧表示されます。接続文字列をコピーすることも、接続文字列で使用するアクセス キーを再生成することもできます。
<br/>
<br/>
**[再生成]** を選択すると、接続文字列内で使用される共有アクセス キーが変更されます。以下の手順を実行します。
5.  Azure 管理ポータルで、Azure アプリケーションの **[キーの同期]** を選択します。
6.  **[内部設置型設定]** を再実行します。[内部設置型設定] を再実行すると、更新されたプライマリ接続文字列が内部設置型リソースによって使用されるように自動的に構成されます。


#### ハイブリッド接続で使用する内部設置型リソースを管理するグループ ポリシーの使用

1.  Hybrid Connection Manager の管理用テンプレートをダウンロードします。
2.  ファイルを解凍します。
3.  グループ ポリシーを変更するコンピューターで、以下の手順を実行します。
4.  .ADMX ファイルを *%WINROOT%\\PolicyDefinitions* フォルダーにコピーします。
5.  .ADML ファイルを *%WINROOT%\\PolicyDefinitions\\ja-jp* フォルダーにコピーします。

コピーが終了すると、グループ ポリシー エディターを使用してポリシーを変更できます。

## 次へ

-   [Connect an Azure Website to an On-Premises Resource using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)][Connect an Azure Website to an On-Premises Resource (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]
-   [Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)][Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)]
-   [Connect to an on-premises SQL Server from Azure mobile services using Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)][Connect to an on-premises SQL Server from Azure Mobile Services and Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)]
-   [ハイブリッド接続の概要][ハイブリッド接続の概要]

<h2 id="see-also">関連項目</h2>
<ul>
<li><a href="http://msdn.microsoft.com/library/azure/dn232347.aspx">Windows Azure での BizTalk サービス管理の REST API に関するページ</a></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302279">BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート</a><br/></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302280">BizTalk サービス: Azure 管理ポータルを使用したプロビジョニング</a><br/></li>
<li><a href="http://go.microsoft.com/fwlink/p/?LinkID=302281">BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ</a><br/></li>
</ul>

  [ハイブリッド接続を作成する]: #CreateHybridConnection
  [Azure Web サイトまたは Azure モバイル サービスを関連付ける]: #LinkWebSite
  [Hybrid Connection Manager をインストール]: #InstallHCM
  [ハイブリッド接続を管理]: #ManageHybridConnection
  [Connect an Azure Website to an On-Premises Resource (ハイブリッド接続を使用した Azure Web サイトの内部設置型リソースへの接続)]: http://go.microsoft.com/fwlink/p/?LinkId=397538
  [Azure 管理ポータル]: http://go.microsoft.com/fwlink/p/?LinkID=213885
  [BizTalk サービスを作成]: http://go.microsoft.com/fwlink/p/?LinkID=329870
  [Hybrid Connections Tab]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionTab.png
  [BizTalk サービス: 開発者、基本、標準、およびプレミアム エディションのチャート]: http://go.microsoft.com/fwlink/p/?LinkID=302279
  [Connect to an on-premises SQL Server from Azure Mobile Services and Hybrid Connections (ハイブリッド接続を使用した Azure モバイル サービスから内部設置型 SQL Server への接続)]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started
  [On-Premises Setup]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionOnPremSetup.png
  [REST API]: http://msdn.microsoft.com/library/azure/dn232347.aspx
  [Manage Options]: ./media/integration-hybrid-connection-overview/WABS_HybridConnectionManageConn.png
  [Connect to on-premises SQL Server from an Azure website using Hybrid Connections (ハイブリッド接続を使用した Azure Web サイトから内部設置型 SQL Server への接続)]: http://go.microsoft.com/fwlink/?LinkID=397979
  [ハイブリッド接続の概要]: http://azure.microsoft.com/ja-jp/documentation/articles/integration-hybrid-connection-overview
