<properties 
	pageTitle="Azure App Service のハイブリッド接続を使用して内部設置型リソースにアクセスする" 
	description="静的 TCP ポートを使用する内部設置型リソースと Azure App Service の Web アプリケーションの間の接続を作成します。" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

#Azure App Service のハイブリッド接続を使用して内部設置型リソースにアクセスする

Azure App Service の Web アプリケーションは、SQL Server、MySQL、HTTP Web APIs、Mobile Services、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用する任意の内部設置型のリソースに接続できます。ここでは、App Service の Web アプリケーションと内部設置型の SQL Server データベース間のハイブリッド接続の作成方法を示します。

> [AZURE.NOTE] ハイブリッド接続機能の Web アプリケーション 部分は、[Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)でのみ利用できます。BizTalk Services で接続を作成するには、「[Hybrid Connections (ハイブリッド接続)](http://go.microsoft.com/fwlink/p/?LinkID=397274)」を参照してください。  

## 前提条件
- Azure サブスクリプション。無料サブスクリプションについては、「[1 か月間無料評価版](http://azure.microsoft.com/pricing/free-trial/)」を参照してください。 

- ハイブリッド接続で内部設置型の SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server は静的ポート 1433 を使用するため、SQL Server で既定のインスタンスを使用することをお勧めします。ハイブリッド接続で使用するための SQL Server Express のインストールと構成の詳細については、「[ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する](http://go.microsoft.com/fwlink/?LinkID=397979)」を参照してください。

- 後でこの記事で説明する内部設置型の Hybrid Connection Manager のエージェントをインストールするコンピューターの条件は次のとおりです。

	- ポート 5671 で Azure に接続できること
	- 内部設置型のリソースの  *hostname*:*portnumber* に到達できること 

> [AZURE.NOTE] この記事の手順では、内部設置型のハイブリッド接続のエージェントをホストするコンピューターからブラウザーを使用していると想定しています。


## Azure ポータルでの Web アプリケーションの作成 ##

> [AZURE.NOTE] このチュートリアルを使用するために、既に Azure ポータルで Web アプリケーションを作成している場合は、この手順をスキップし、「[ハイブリッド接続および BizTalk サービスを作成する](#CreateHC) 」から開始してください。

1. [Azure ポータル](https://portal.azure.com)の左下端の **[新規]** > **[Web + モバイル]** > **[Web サイト]** をクリックします。
	
	![New button][New]
	
	![New web app][NewWebsite]
	
2. **Web アプリケーション** ブレードで、 URL を入力して **[作成]** をクリックします。 
	
	![Website name][WebsiteCreationBlade]
	
3. しばらくすると、Web アプリケーションが作成され、Web アプリケーションのブレードが表示されます。ブレードは縦方向にスクロールできるダッシュボードで、サイトを管理することができます。
	
	![Website running][WebSiteRunningBlade]
	
4. サイトがライブかどうかを確認するには、**[参照]** アイコンをクリックして、既定のページを表示します。
	
	![Click browse to see your web app][Browse]
	
	![Default web app page][DefaultWebSitePage]
	
次に、Web アプリケーションに対するハイブリッド接続と BizTalk サービスを作成します。

<a name="CreateHC"></a>
## ハイブリッド接続および BizTalk サービスを作成する ##

1. Web アプリケーションのブレードを下にスクロールして、**[ハイブリッド接続]** を選択します。
	
	![Hybrid connections][CreateHCHCIcon]
	
2. ハイブリッド接続ブレードで、**[追加]** をクリックします。
	
	<!-- ![Add a hybrid connnection][CreateHCAddHC]
-->
	
3. **[ハイブリッド接続の追加]** ブレードが開きます。これは最初のハイブリッド接続であるため、**[新しいハイブリッド接続]** があらかじめ選択され、**[ハイブリッド接続の作成]** ブレードが開きます。
	
	![Create a hybrid connection][TwinCreateHCBlades]
	
	**[ハイブリッド接続の作成] ブレード**で、次の手順を実行します。
	- **[名前]** に、接続の名前を入力します。
	- **[ホスト名]** に、リソースをホストする内部設置型のコンピューターの名前を入力します。
	- **[ポート]** には、内部設置型のリソースが使用するポート番号 (SQL Server の既定のインスタンスの場合は 1433) を入力します。
	- **[Biz Talk サービス]** をクリックします。


4. **[BizTalk サービスの作成]** ブレードが開きます。BizTalk サービスの名前を入力し、**[OK]** をクリックします。
	
	![Create BizTalk service][CreateHCCreateBTS]
	
	**[BizTalk サービスの作成]** ブレードが閉じ、**[ハイブリッド接続の作成]** ブレードに戻ります。
	
5. [ハイブリッド接続の作成] ブレードで、**[OK]** をクリックします。 
	
	![Click OK][CreateBTScomplete]
	
6. 処理が完了すると、ポータルの通知領域に接続の作成が完了したことが通知されます。
	<!-- TODO

    この手順ではすべてが失敗します。新しいポータルでは BizTalk サービスを作成できません。以前のポータル (フル ポータル) に切り替えて
	BizTalk サービスを作成しましたが、接続はできないようです、
	ハイブリッド接続作成の手順を終了すると、次のエラーが表示されます。
	ハイブリッド接続 RelecIoudHC の作成に失敗しました。この 
	リソース タイプは名前空間に見つかりませんでした。 
	'Microsoft.BizTaIkServices for api version 2014-06-01'.
	
	このエラーは、インスタンスではなく、タイプが見つからなかったことを示しています。
	![Success notification][CreateHCSuccessNotification]
	-->
7. Web アプリケーション ブレードの **[ハイブリッド接続]** アイコンは、ハイブリッド接続が 1 つ作成されたことを示しています。
	
	![One hybrid connection created][CreateHCOneConnectionCreated]
	
これで、クラウド ハイブリッド接続のインフラストラクチャの重要な部分が完了しました。次に、対応する内部設置型の部分を作成します。

<a name="InstallHCM"></a>
## 内部設置型の Hybrid Connection Manager をインストールして接続を完了する ##

1. Web アプリケーション ブレードで、[ハイブリッド接続] アイコンをクリックします。 
	
	![Hybrid connections icon][HCIcon]
	
2. **[ハイブリッド接続]** ブレードで、最近追加されたエンドポイントの **[状態]** 列に **[未接続]** と表示されています。接続をクリックして構成します。
	
	![Not connected][NotConnected]
	
	ハイブリッド接続ブレードが開きます。
	
	![NotConnectedBlade][NotConnectedBlade]
	
3. ブレードで、**[リスナーのセットアップ]** をクリックします。
	
	![Click Listener Setup][ClickListenerSetup]
	
4. **[ハイブリッド接続のプロパティ]** ブレードが開きます。**内部設置型の Hybrid Connection Manager** で、**[インストールするにはここをクリックします]** をクリックします。
	
	![Click here to install][ClickToInstallHCM]
	
5. [アプリケーションの実行 - セキュリティの警告] ダイアログで、**[実行]** を選択します。
	
	![Choose Run to continue][ApplicationRunWarning]
	
6.	**[ユーザー アカウント制御]** ダイアログで、**[はい]** を選択します。
	
	![Choose Yes][UAC]
	
7. Hybrid Connection Manager がダウンロードされ、インストールされます。 
	
	![Installing][HCMInstalling]
	
8. インストールが完了したら、**[閉じる]** をクリックします。
	
	![Click Close][HCMInstallComplete]
	
	**[ハイブリッド接続]** ブレードで、**[状態]** 列に **[接続]** と表示されています。 
	
	![Connected Status][HCStatusConnected]

これで、ハイブリッド接続のインフラストラクチャが完成しました。この接続を使用するハイブリッド アプリケーションを作成できます。 

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service を実際に使ってみるには、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)にアクセスしてください。App Service に有効期限付きのスターター Web  アプリケーションを無償ですぐに作成できます。No credit cards required; no commitments.

<a name="NextSteps"></a>
## 次のステップ ##

- ハイブリッド接続を使用する ASP.NET Web アプリケーションの作成の詳細については、「[ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する](http://go.microsoft.com/fwlink/?LinkID=397979)」を参照してください。

- モバイル サービスでのハイブリッド接続の使用の詳細については、「[ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する](mobile-services-dotnet-backend-hybrid-connections-get-started.md)」を参照してください。

### その他のリソース

[ハイブリッド接続の概要](http://go.microsoft.com/fwlink/p/?LinkID=397274)

[Josh Twist によるハイブリッド接続の紹介 (チャネル 9 ビデオ)](http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections)

[ハイブリッド接続の Web サイト](http://azure.microsoft.com/services/biztalk-services/)

[BizTalk Services:[ダッシュボード]、[モニター]、[スケール]、[構成]、[ハイブリッド接続] タブ](../biztalk-dashboard-monitor-scale-tabs/)

[シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする (チャネル 9 ビデオ)](http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=)

[ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する (チャネル 9 ビデオ)](http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections)

## 変更点
* Web サイトから App Service への変更に関するガイド:[Azure App Service と既存の Azure サービスへの影響](http://go.microsoft.com/fwlink/?LinkId=529714)
* 以前のポータルから新しいポータルへの変更に関するガイド:[Azure App Service の Web サイトと Web アプリケーションに関するリファレンス](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[New]:./media/web-sites-hybrid-connection-get-started/B01New.png
[NewWebsite]:./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
[WebsiteCreationBlade]:./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
[WebSiteRunningBlade]:./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
[Browse]:./media/web-sites-hybrid-connection-get-started/B05Browse.png
[DefaultWebSitePage]:./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
[CreateHCHCIcon]:./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
[CreateHCAddHC]:./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
[TwinCreateHCBlades]:./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
[CreateHCCreateBTS]:./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
[CreateBTScomplete]:./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
[CreateHCSuccessNotification]:./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
[CreateHCOneConnectionCreated]:./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
[HCIcon]:./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
[NotConnected]:./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
[NotConnectedBlade]:./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
[ClickListenerSetup]:./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
[ClickToInstallHCM]:./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
[ApplicationRunWarning]:./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
[UAC]:./media/web-sites-hybrid-connection-get-started/D07UAC.png
[HCMInstalling]:./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
[HCMInstallComplete]:./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
[HCStatusConnected]:./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png

<!--HONumber=49-->