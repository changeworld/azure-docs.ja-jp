<properties linkid="web-sites-hybrid-connection" title="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" pageTitle="Hybrid Connection: Connect an Azure Website to an On-Premises Resource" description="Create a connection between an Azure website and an on-premises resource that uses a static TCP port" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm" />

# ハイブリッド接続を使用して Azure の Web サイトを内部設置型のリソースに接続する

Microsoft Azure の Web サイトは、SQL Server、MySQL、HTTP Web APIs、Mobile Services、ほとんどのカスタム Web サービスなど、静的 TCP ポートを使用する任意の内部設置型のリソースに接続できます。ここでは、Azure の Web サイトと内部設置型の SQL Server データベース間のハイブリッド接続の作成方法を示します。

> [WACOM.NOTE] ハイブリッド接続機能の Websites 部分は、[Azure プレビュー ポータル][Azure プレビュー ポータル]でのみ利用できます。BizTalk Services で接続を作成するには、「[Hybrid Connections (ハイブリッド接続)][Hybrid Connections (ハイブリッド接続)]」を参照してください。

## 前提条件

-   Azure サブスクリプション。無料サブスクリプションについては、「[1 か月間無料評価版][1 か月間無料評価版]」を参照してください。

-   ハイブリッド接続で内部設置型の SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server は静的ポート 1433 を使用するため、SQL Server で既定のインスタンスを使用することをお勧めします。ハイブリッド接続で使用するための SQL Server Express のインストールと構成の詳細については、「[Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する)][Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する)]」を参照してください。

-   後でこの記事で説明する内部設置型の Hybrid Connection Manager のエージェントをインストールするコンピューターの条件は次のとおりです。

    -   ポート 5671 で Azure に接続できること
    -   内部設置型のリソースの *hostname*:*portnumber* に到達できること

> [WACOM.NOTE] この記事の手順では、内部設置型のハイブリッド接続のエージェントをホストするコンピューターからブラウザーを使用していると想定しています。

## この記事の内容

[Azure プレビュー ポータルで Web サイトを作成する][Azure プレビュー ポータルで Web サイトを作成する]

[ハイブリッド接続および BizTalk サービスを作成する][ハイブリッド接続および BizTalk サービスを作成する]

[内部設置型の Hybrid Connection Manager をインストールして接続を完了する][内部設置型の Hybrid Connection Manager をインストールして接続を完了する]

[次のステップ][次のステップ]

## Azure プレビュー ポータルで Web サイトを作成する

> [WACOM.NOTE] このチュートリアルを使用するために、既に Azure プレビュー ポータルで Web サイトを作成している場合は、この手順をスキップし、「[ハイブリッド接続および BizTalk サービスを作成する][ハイブリッド接続および BizTalk サービスを作成する]」から開始してください。

1.  [Azure プレビュー ポータル][Azure プレビュー ポータル]の左下端の **[新規作成]** をクリックして、**[Web サイト]** を選択します。

    ![New button][New button]

    ![New website][New website]

2.  **[Web サイト]** ブレードで、Web サイトの名前を入力して、**[作成]** をクリックします。

    ![Website name][Website name]

3.  しばらくすると、Web サイトが作成され、Web サイトのブレードが表示されます。ブレードは縦方向にスクロールできるダッシュボードで、サイトを管理することができます。

    ![Website running][Website running]

4.  サイトがライブかどうかを確認するには、**[参照]** アイコンをクリックして、既定のページを表示します。

    ![Click browse to see your website][Click browse to see your website]

    ![Default website page][Default website page]

次に、Web サイトに対するハイブリッド接続と BizTalk サービスを作成します。

<a name="CreateHC"></a>

## ハイブリッド接続および BizTalk サービスを作成する

1.  プレビュー ポータルに戻り、Web サイトのブレードを下にスクロールして、**[ハイブリッド接続]** を選択します。

    ![Hybrid connections][Hybrid connections]

2.  ハイブリッド接続ブレードで、**[追加]** をクリックします。

    ![Add a hybrid connnection][Add a hybrid connnection]

3.  **[ハイブリッド接続の追加]** ブレードが開きます。これは最初のハイブリッド接続であるため、**[新しいハイブリッド接続]** があらかじめ選択され、**[ハイブリッド接続の作成]** ブレードが開きます。

    ![Create a hybrid connection][Create a hybrid connection]

    **[ハイブリッド接続の作成] ブレード**で、次の手順を実行します。

    -   **[名前]** に、接続の名前を入力します。
    -   **[ホスト名]** に、リソースをホストする内部設置型のコンピューターの名前を入力します。
    -   **[ポート]** には、内部設置型のリソースが使用するポート番号 (SQL Server の既定のインスタンスの場合は 1433) を入力します。
    -   **[BizTalk サービス]** をクリックします。

4.  **[BizTalk サービスの作成]** ブレードが開きます。BizTalk サービスの名前を入力し、**[OK]** をクリックします。

    ![Create BizTalk service][Create BizTalk service]

    **[BizTalk サービスの作成]** ブレードが閉じ、**[ハイブリッド接続の作成]** ブレードに戻ります。

5.  [ハイブリッド接続の作成] ブレードで、**[OK]** をクリックします。

    ![OK をクリックします。][OK をクリックします。]

6.  処理が完了すると、ポータルの通知領域に接続の作成が完了したことが通知されます。

    ![Success notification][Success notification]

7.  Web サイト ブレードに、ハイブリッド接続が 1 つ作成されたことを示す **[ハイブリッド接続]** アイコンが表示されます。

    ![One hybrid connection created][One hybrid connection created]

これで、クラウド ハイブリッド接続のインフラストラクチャの重要な部分が完了しました。次に、対応する内部設置型の部分を作成します。

<a name="InstallHCM"></a>

## 内部設置型の Hybrid Connection Manager をインストールして接続を完了する

1.  Web サイト ブレードで、[ハイブリッド接続] アイコンをクリックします。

    ![Hybrid connections icon][Hybrid connections icon]

2.  **[ハイブリッド接続]** ブレードで、最近追加されたエンドポイントの **[状態]** 列に **[未接続]** と表示されています。接続をクリックして構成します。

    ![Not connected][Not connected]

    ハイブリッド接続ブレードが開きます。

    ![NotConnectedBlade][NotConnectedBlade]

3.  ブレードで、**[リスナーのセットアップ]** をクリックします。

    ![Click Listener Setup][Click Listener Setup]

4.  **[ハイブリッド接続のプロパティ]** ブレードが開きます。**内部設置型の Hybrid Connection Manager** で、**[インストールするにはここをクリックします]** をクリックします。

    ![Click here to install][Click here to install]

5.  [アプリケーションの実行 - セキュリティの警告] ダイアログで、**[実行]** を選択します。

    ![Choose Run to continue][Choose Run to continue]

6.  **[ユーザー アカウント制御]** ダイアログで、**[はい]** を選択します。

    ![Choose Yes][Choose Yes]

7.  Hybrid Connection Manager がダウンロードされ、インストールされます。

    ![インストール][インストール]

8.  インストールが完了したら、**[閉じる]** をクリックします。

    ![Click Close][Click Close]

    **[ハイブリッド接続]** ブレードで、**[状態]** 列に **[接続]** と表示されています。

    ![Connected Status][Connected Status]

これで、ハイブリッド接続のインフラストラクチャが完成しました。この接続を使用するハイブリッド アプリケーションを作成できます。

<a name="NextSteps"></a>

## 次のステップ

-   ハイブリッド接続を使用する ASP.NET Web アプリケーションの作成の詳細については、「[Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する)][Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する)]」を参照してください。

-   モバイル サービスでのハイブリッド接続の使用の詳細については、「[ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する][ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する]」を参照してください。

### その他のリソース

[ハイブリッド接続の概要][Hybrid Connections (ハイブリッド接続)]

[Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (チャネル 9 ビデオ)][Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (チャネル 9 ビデオ)]

[ハイブリッド接続の Web サイト][ハイブリッド接続の Web サイト]

[BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ][]

[Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (チャネル 9 ビデオ)][Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (チャネル 9 ビデオ)]

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (チャネル 9 ビデオ)][Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (チャネル 9 ビデオ)]

<!-- IMAGES -->

  [Azure プレビュー ポータル]: https://portal.azure.com
  [Hybrid Connections (ハイブリッド接続)]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [1 か月間無料評価版]: http://azure.microsoft.com/ja-jp/pricing/free-trial/
  [Connect to an on-premises SQL Server from an Azure web site using Hybrid Connections (ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する)]: http://go.microsoft.com/fwlink/?LinkID=397979
  [Azure プレビュー ポータルで Web サイトを作成する]: #CreateSite
  [ハイブリッド接続および BizTalk サービスを作成する]: #CreateHC
  [内部設置型の Hybrid Connection Manager をインストールして接続を完了する]: #InstallHCM
  [次のステップ]: #NextSteps
  [New button]: ./media/web-sites-hybrid-connection-get-started/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-get-started/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-get-started/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-get-started/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-get-started/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-get-started/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-get-started/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-get-started/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-get-started/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-get-started/C04CreateHCCreateBTS.png
  [OK をクリックします。]: ./media/web-sites-hybrid-connection-get-started/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-get-started/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-get-started/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-get-started/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-get-started/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-get-started/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-get-started/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-get-started/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-get-started/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-get-started/D07UAC.png
  [インストール]: ./media/web-sites-hybrid-connection-get-started/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-get-started/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-get-started/D10HCStatusConnected.png
  [ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (チャネル 9 ビデオ)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [ハイブリッド接続の Web サイト]: http://azure.microsoft.com/ja-jp/services/biztalk-services/
  [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ]: http://azure.microsoft.com/ja-jp/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (チャネル 9 ビデオ)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (チャネル 9 ビデオ)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
