<properties linkid="web-sites-hybrid-connection-getting-started" title="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" pageTitle="Hybrid Connections Step-by-Step: Connect to on-premises SQL Server from an Azure website" description="Create a a website on Microsoft Azure and connect it to an on-premises SQL Server database" metaKeywords="" services="web-sites" solutions="web" documentationCenter="" authors="timamm" manager="paulettm" editor="mollybos" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="timamm"></tags>

# ハイブリッド接続を使用して Azure の Web サイトから内部設置型の SQL Server に接続する

## はじめに

ハイブリッド接続を使用することで、Microsoft Azure の Web サイトを、静的 TCP ポートを使用する内部設置型のリソースに接続できます。サポートされているリソースには、Microsoft SQL Server、MySQL、HTTP Web APIs、Mobile Services、およびほとんどのカスタム Web サービスが含まれます。

このチュートリアルでは、Azure プレビュー ポータルでの Web サイトの作成方法、新しいハイブリッド接続機能を使用したローカルの内部設置型の SQL Server データベースへの Web サイトの接続方法、ハイブリッド接続を使用する単純な ASP.NET の Web アプリケーションの作成方法、および Azure の Web サイトへのアプリケーションのデプロイ方法について学習します。完成した Azure の Web サイトは、ユーザーの資格情報を内部設置型のメンバーシップ データベースに保存します。このチュートリアルは、Azure または ASP.NET を使用した経験がない読者を対象に作成されています。

> [WACOM.NOTE] ハイブリッド接続機能の Websites 部分は、[Azure プレビュー ポータル][]でのみ利用できます。BizTalk Services で接続を作成するには、「[Hybrid Connections (ハイブリッド接続)][]」を参照してください。

## 前提条件

このチュートリアルを完了するには、以下の製品が必要です。すべて無料版を利用できるため、Azure の開発を完全に無料で始めることができます。

-   **Azure サブスクリプション** - 無料サブスクリプションについては、「[1 か月間無料評価版][]」を参照してください。

-   **Visual Studio 2013** - Visual Studio 2013 の無料試用版のダウンロードについては、「[Visual Studio のダウンロード][]」を参照してください。これをインストールしてから、次に進みます。

-   **Microsoft .NET Framework 3.5 Service Pack 1** - オペレーティング システムが Windows 8.1、Windows Server 2012 R2、Windows 8、Windows Server 2012、Windows 7、または Windows Server 2008 R2 の場合は、[コントロール パネル] \> [プログラムと機能] \> [Windows の機能の有効化または無効化] の順に選択してこれを実行できます。そうでない場合は、[Microsoft Download Center][] からダウンロードできます。

-   **SQL Server 2014 Express with Tools** - [Microsoft Web プラットフォーム データベースのページ][]で Microsoft SQL Server Express の無料版をダウンロードします。**Express** (LocalDB ではない) 版を選択します。**Express with Tools** 版には、このチュートリアルで使用する SQL Server Management Studio が含まれています。

-   **SQL Server Management Studio Express** - これは、上記の SQL Server 2014 Express with Tools のダウンロードに含まれていますが、個別にインストールする必要がある場合は、[SQL Server Express のダウンロードのページ][Microsoft Web プラットフォーム データベースのページ]からダウンロードしてインストールすることができます。

このチュートリアルでは、Azure サブスクリプションを持っている、Visual Studio 2013 をインストールしている、および .NET Framework 3.5 をインストールしているか有効にしていると想定しています。このチュートリアルでは、Azure のハイブリッド接続機能 (静的 TCP ポートの既定のインスタンス) を使用する構成に SQL Server 2014 Express をインストールする方法を説明します。SQL Server をインストールしていない場合は、チュートリアルを開始する前に、上記の場所から SQL Server 2014 Express with Tools をダウンロードしてください。

### メモ

ハイブリッド接続で内部設置型の SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server の既定のインスタンスは静的ポート 1433 を使用しますが、名前付きインスタンスは静的ポート 1433 を使用しません。

内部設置型の Hybrid Connection Manager のエージェントをインストールするコンピューターの条件は、次のとおりです。

-   ポート 5671 で Azure に接続できること
-   内部設置型のリソースの *hostname*:*portnumber* に到達できること

この記事の手順では、内部設置型のハイブリッド接続のエージェントをホストするコンピューターからブラウザーを使用していると想定しています。

上記の条件を満たす構成および環境に既に SQL Server をインストールしている場合は、この手順をスキップし、「[内部設置型の SQL Server を作成する][]」から開始できます。

## この記事の内容

[A. SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する][]

[B Azure プレビュー ポータルで Web サイトを作成する][]

[C. ハイブリッド接続および BizTalk サービスを作成する][]

[D. 内部設置型の Hybrid Connection Manager をインストールして接続を完了する][]

[E. 基本的な ASP.NET Web プロジェクトを作成し、データベース接続文字列を編集して、プロジェクトをローカルで実行する][]

[F. Web アプリケーションを Azure に発行して、テストする][]

<a name="InstallSQL"></a>

## A. SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する

このセクションでは、Web アプリケーションが Azure プレビュー環境を使用するために、SQL Server Express をインストールし、TCP/IP を有効にして、データベースを作成する方法を説明します。

### SQL Server Express をインストールする

1.  SQL Server Express をインストールするには、ダウンロードした **SQLEXPRWT\_x64\_ENU.exe** または **SQLEXPR\_x86\_ENU.exe** ファイルを実行します。SQL Server インストール センター ウィザードが表示されます。

    ![SQL Server Install][]

2.  **[SQL Server の新規スタンドアロン インストールを実行するか、既存のインストールに機能を追加します]** をクリックします。指示に従って、**[インスタンスの構成]** ページが表示されるまで、既定の選択と設定を使用します。

3.  **[インスタンスの構成]** ページで、**[既定のインスタンス]** を選択します。

    ![Choose Default Instance][]

    既定では、SQL Server の既定のインスタンスが静的ポート 1433 の SQL Server クライアントから要求をリッスンします。ハイブリッド接続機能には静的ポート 1433 が必要です。名前付きインスタンスは動的ポートと UDP を使用します。これはハイブリッド接続ではサポートされません。

4.  **[サーバーの構成]** ページでは既定値をそのまま使用します。

5.  **[データベース エンジンの構成]** ページの **[認証モード]** で、**[混合モード (SQL Server 認証と Windows 認証)]** を選択して、パスワードを入力します。

    ![Choose Mixed Mode][]

    このチュートリアルでは、SQL Server 認証を使用します。後で必要になるため、入力したパスワードを忘れないでください。

6.  ウィザードの残りの手順を実行し、インストールを完了します。

### TCP/IP を有効にする

TCP/IP を有効にするには、SQL Server Express をインストールした際にインストールされた SQL Server 構成マネージャーを使用します。次に進む前に、「[SQL Server の TCP/IP ネットワーク プロトコルの有効化][]」の手順に従ってください。

<a name="CreateSQLDB"></a>

### 内部設置型の SQL Server データベースを作成する

Visual Studio Web アプリケーションには、Azure がアクセスできるメンバーシップ データベースが必要です。これには、(既定で MVC テンプレートが使用する LocalDB データベースではなく) SQL Server または SQL Server Express データベースが必要なため、次にメンバーシップ データベースを作成します。

1.  SQL Server Management Studio で、インストールした SQL Server に接続します (**[サーバーへの接続]** ダイアログが自動的に表示されない場合は、左パネルの **[オブジェクト エクスプローラー]** に移動し、**[接続]**、次に **[データベース エンジン]** をクリックします)。
    ![Connect to Server][]

    **[サーバーの種類]** には、**[データベース エンジン]** を選択します。**[サーバー名]** には、**[localhost]**、または使用しているコンピューターの名前を使用します。**[SQL Server 認証]** を選択し、前に作成したユーザー名とパスワードでログインします。

2.  SQL Server Management Studio を使用して新しいデータベースを作成するには、オブジェクト エクスプローラーで **[データベース]** を右クリックしてから、**[新しいデータベース]** をクリックします。

    ![Create new database][]

3.  **[新しいデータベース]** ダイアログで、データベース名に「MembershipDB」と入力し、**[OK]** をクリックします。

    ![Provide database name][]

    この時点では、データベースに変更を加えることはできないことに注意してください。メンバーシップ情報は、後で Web アプリケーションを実行すると、自動的に追加されます。

4.  オブジェクト エクスプローラーで、**[データベース]** を展開すると、メンバーシップ データベースが作成されたことが示されます。

    ![MembershipDB created][]

<a name="CreateSite"></a>

## B. Azure プレビュー ポータルで Web サイトを作成する

> [WACOM.NOTE] このチュートリアルを使用するために、既に Azure プレビュー ポータルで Web サイトを作成している場合は、この手順をスキップし、「[ハイブリッド接続および BizTalk サービスを作成する][C. ハイブリッド接続および BizTalk サービスを作成する]」から続けてください。

1.  [Azure プレビュー ポータル][]の左下端の **[新規作成]** をクリックして、**[Web サイト]** を選択します。

    ![New button][]

    ![New website][]

2.  **[Web サイト]** ブレードで、Web サイトの名前を入力して、**[作成]** をクリックします。

    ![Website name][]

3.  しばらくすると、Web サイトが作成され、Web サイトのブレードが表示されます。ブレードは縦方向にスクロールできるダッシュボードで、サイトを管理することができます。

    ![Website running][]

4.  サイトがライブかどうかを確認するには、**[参照]** アイコンをクリックして、既定のページを表示します。

    ![Click browse to see your website][]

    ![Default website page][]

次に、Web サイトに対するハイブリッド接続と BizTalk サービスを作成します。

<a name="CreateHC"></a>

## C. ハイブリッド接続および BizTalk サービスを作成する

1.  プレビュー ポータルに戻り、Web サイトのブレードを下にスクロールして、**[ハイブリッド接続]** を選択します。

    ![Hybrid connections][]

2.  ハイブリッド接続ブレードで、**[追加]** をクリックします。

    ![Add a hybrid connnection][]

3.  **[ハイブリッド接続の追加]** ブレードが開きます。これは最初のハイブリッド接続であるため、**[新しいハイブリッド接続]** があらかじめ選択され、**[ハイブリッド接続の作成]** ブレードが開きます。

    ![Create a hybrid connection][]

    **[ハイブリッド接続の作成] ブレード**で、次の手順を実行します。

    -   **[名前]** に、接続の名前を入力します。
    -   **[ホスト名]** には、SQL Server ホスト コンピューターのコンピューター名を入力します。
    -   **[ポート]** には、1433 (SQL Server の既定のポート) を入力します。
    -   **[BizTalk サービス]** をクリックします。

4.  **[BizTalk サービスの作成]** ブレードが開きます。BizTalk サービスの名前を入力し、**[OK]** をクリックします。

    ![Create BizTalk service][]

    **[BizTalk サービスの作成]** ブレードが閉じ、**[ハイブリッド接続の作成]** ブレードに戻ります。

5.  [ハイブリッド接続の作成] ブレードで、**[OK]** をクリックします。

    ![Click OK][]

6.  処理が完了すると、ポータルの通知領域に接続の作成が完了したことが通知されます。

    ![Success notification][]

7.  Web サイト ブレードに、ハイブリッド接続が 1 つ作成されたことを示す **[ハイブリッド接続]** アイコンが表示されます。

    ![One hybrid connection created][]

これで、クラウド ハイブリッド接続のインフラストラクチャの重要な部分が完了しました。次に、対応する内部設置型の部分を作成します。

<a name="InstallHCM"></a>

## D. 内部設置型の Hybrid Connection Manager をインストールして接続を完了する

1.  Web サイト ブレードで、[ハイブリッド接続] アイコンをクリックします。

    ![Hybrid connections icon][]

2.  **[ハイブリッド接続]** ブレードで、最近追加されたエンドポイントの **[状態]** 列に **[未接続]** と表示されています。接続をクリックして構成します。

    ![Not connected][]

    ハイブリッド接続ブレードが開きます。

    ![NotConnectedBlade][]

3.  ブレードで、**[リスナーのセットアップ]** をクリックします。

    ![Click Listener Setup][]

4.  **[ハイブリッド接続のプロパティ]** ブレードが開きます。**内部設置型の Hybrid Connection Manager** で、**[インストールするにはここをクリックします]** をクリックします。

    ![Click here to install][]

5.  [アプリケーションの実行 - セキュリティの警告] ダイアログで、**[実行]** を選択します。

    ![Choose Run to continue][]

6.  **[ユーザー アカウント制御]** ダイアログで、**[はい]** を選択します。

    ![Choose Yes][]

7.  Hybrid Connection Manager がダウンロードされ、インストールされます。

    ![インストール][]

8.  インストールが完了したら、**[閉じる]** をクリックします。

    ![Click Close][]

    **[ハイブリッド接続]** ブレードで、**[状態]** 列に **[接続]** と表示されています。

    ![Connected Status][]

これで、ハイブリッド接続のインフラストラクチャが完成しました。この接続を使用する Web アプリケーションを作成できます。

<a name="CreateASPNET"></a>

## E. 基本的な ASP.NET Web プロジェクトを作成し、データベース接続文字列を編集して、プロジェクトをローカルで実行する

### 基本的な ASP.NET プロジェクトを作成する

1.  Visual Studio の **[ファイル]** メニューで、新しいプロジェクトを作成します。

    ![New Visual Studio project][]

2.  **[新しいプロジェクト]** ダイアログの **[テンプレート]** セクションで、**[Web]**、**[ASP.NET Web アプリケーション]** の順に選択して、**[OK]** をクリックします。

    ![Choose ASP.NET Web Application][]

3.  **[新しい ASP.NET プロジェクト]** ダイアログで、**[MVC]** を選択し、**[OK]** をクリックします。

    ![Choose MVC][]

4.  プロジェクトが作成されると、アプリケーションの readme ページが表示されます。まだ Web プロジェクトを実行しないでください。

    ![Readme page][]

### アプリケーションのデータベース接続文字列を編集する

この手順では、ローカル SQL Server Express データベースの場所をアプリケーションに伝える接続文字列を編集します。接続文字列は、アプリケーションの Web.config ファイルにあり、アプリケーションの構成情報が含まれています。

> [WACOM.NOTE] アプリケーションで、Visual Studio の既定の LocalDB ではなく、SQL Server Express で作成したデータベースを使用するためには、プロジェクトを実行する前にこの手順を完了することが重要です。

1.  ソリューション エクスプローラーで、Web.config ファイルをダブルクリックします。

    ![web.config][]

2.  **[connectionStrings]** セクションを編集して、ローカル コンピューターの SQL Server データベースを指すようにします。次の例の構文を使用します。

    ![接続文字列][]

    接続文字列を作成する際には、次の事項に留意してください。

    -   既定のインスタンスではなく、名前付きインスタンス (YourServer\\SQLEXPRESS など) に接続している場合は、静的ポートを使用するように SQL Server を構成する必要があります。静的ポートの構成の詳細については、「[特定のポートでリッスンするように SQL Server を構成する方法][]」を参照してください。既定では、名前付きインスタンスは動的ポートと UDP を使用します。これはハイブリッド接続ではサポートされません。

    -   ポート (例に示すように既定では 1433) を接続文字列に指定することをお勧めします。これにより、ローカル SQL Server で TCP が有効になり、正しいポートが使用されます。

    -   SQL Server 認証を使用して接続するのを忘れないでください。接続文字列にユーザー ID とパスワードを指定します。

3.  Visual Studio で **[保存]** をクリックして、Web.config ファイルを保存します。

### プロジェクトをローカルで実行し、新しいユーザーを登録する

1.  ここで、[デバッグ] の [参照] ボタンをクリックして、新しい Web プロジェクトをローカルで実行します。この例では、Internet Explorer を使用します。

    ![Run project][]

2.  既定の Web ページの右上で、**[登録]** をクリックして新しいアカウントを登録します。

    ![Register a new account][]

3.  ユーザー名とパスワードを入力します。

    ![Enter user name and password][]

    これで、アプリケーションのメンバーシップ情報を保持するローカル SQL Server にデータベースが自動的に作成されます。テーブルの 1 つ (**dbo.AspNetUsers**) に、入力した Web サイト ユーザーの資格情報が保持されます。このテーブルは後でチュートリアルで使用されます。

4.  既定の Web ページのブラウザー ウィンドウを閉じます。これで、Visual Studio でアプリケーションが停止します。

	これで、アプリケーションを Azure に発行してテストする次の手順に進む準備ができました。

<a name="PubNTest"></a>

## F. Web アプリケーションを Azure に発行して、テストする

ここで、アプリケーションを Azure の Web サイトに発行して、テストします。前に構成したハイブリッド接続が、ローカル コンピューターのデータベースに Web サイト アプリケーションを接続するためにどう使用されるかを確認します。

### Web アプリケーションを発行する

1.  Azure ポータルの Web サイトに対する発行プロファイルをダウンロードできます。Web サイトのブレードで、**[発行プロファイルのダウンロード]** を選択して、コンピューターにファイルを保存します。

    ![発行プロファイルのダウンロード][]

    ![Publish profile in downloads folder][]

    次に、このファイルを Visual Studio Web アプリケーションにインポートします。

2.  Visual Studio で、ソリューション エクスプローラーのプロジェクト名を右クリックし、**[発行]** を選択します。

    ![Select publish][]

3.  **[Web の発行]** ダイアログの **[プロファイル]** タブで、**[インポート]** を選択します。

    ![インポート][]

4.  ダウンロードした発行プロファイルを参照して、選択してから、**[OK]** をクリックします。

    ![Browse to profile][]

5.  発行情報がインポートされ、ダイアログの **[接続]** タブに表示されます。

    ![発行 のクリック][]

    **[発行]** をクリックします。

    発行が完了すると、ブラウザーが起動し、見慣れた Web サイトが表示されますが、Azure クラウドでライブになっているという点が違います。

次に、ライブ Web アプリケーションを使用して、ハイブリッド接続の実行を確認します。

### 完成した Web アプリケーションを Azure でテストする

1.  Azure の Web ページの右上で、**[ログイン]** を選択します。

    ![Test log in][]

2.  Azure の Web サイトが、ローカル コンピューターの Web アプリケーションのメンバーシップ データベースに接続されています。これを確認するには、前にローカル データベースに入力した資格情報でログインします。

    ![Hello greeting][]

3.  新しいハイブリッド接続を詳細にテストするには、Azure Web アプリケーションからログオフし、別のユーザーとして登録します。新しいユーザー名とパスワードを入力して、**[登録]** をクリックします。

    ![Test register another user][]

4.  ハイブリッド接続を通じて新しいユーザーの資格情報がローカル データベースに保存されたことを確認するには、ローカル コンピューターで SQL Management Studio を開きます。オブジェクト エクスプローラーで、**[MembershipDB]** データベースを展開してから、**[テーブル]** を展開します。**[dbo.AspNetUsers]** メンバーシップ テーブルを右クリックし、**[先頭の 1000 行を選択]** を選択して、結果を確認します。

    ![View the results][]

5.  ローカル メンバーシップ テーブルには、ローカルで作成したメンバーシップと、Azure クラウドで作成したメンバーシップの両方が表示されています。クラウドで作成したメンバーシップは、Azure のハイブリッド接続機能で内部設置型のデータベースに保存されています。

    ![Registered users in on-premises database][]

これで、Azure クラウドと内部設置型の SQL Server データベース間のハイブリッド接続を使用する ASP.NET Web アプリケーションを作成し、デプロイすることができました。ご利用ありがとうございます。

## 関連項目

[ハイブリッド接続の概要][Hybrid Connections (ハイブリッド接続)]

[Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (チャネル 9 ビデオ)][]

[ハイブリッド接続の Web サイト][]

[BizTalk サービス: ダッシュボード、監視、および スケール タブ][]

[Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (チャネル 9 ビデオ)][]

[ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する][]

[Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (チャネル 9 ビデオ)][]

[ASP.NET の Id の概要][]

<!-- IMAGES -->

  [Azure プレビュー ポータル]: https://portal.azure.com
  [Hybrid Connections (ハイブリッド接続)]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [1 か月間無料評価版]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Visual Studio のダウンロード]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft Download Center]: http://www.microsoft.com/download/en/details.aspx?displaylang=en&id=22
  [Microsoft Web プラットフォーム データベースのページ]: http://www.microsoft.com/web/platform/database.aspx
  [内部設置型の SQL Server を作成する]: #CreateSQLDB
  [A. SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する]: #InstallSQL
  [B Azure プレビュー ポータルで Web サイトを作成する]: #CreateSite
  [C. ハイブリッド接続および BizTalk サービスを作成する]: #CreateHC
  [D. 内部設置型の Hybrid Connection Manager をインストールして接続を完了する]: #InstallHCM
  [E. 基本的な ASP.NET Web プロジェクトを作成し、データベース接続文字列を編集して、プロジェクトをローカルで実行する]: #CreateASPNET
  [F. Web アプリケーションを Azure に発行して、テストする]: #PubNTest
  [SQL Server Install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A01SQLServerInstall.png
  [Choose Default Instance]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A02ChooseDefaultInstance.png
  [Choose Mixed Mode]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A03ChooseMixedMode.png
  [SQL Server の TCP/IP ネットワーク プロトコルの有効化]: http://technet.microsoft.com/en-us/library/hh231672%28v=sql.110%29.aspx
  [Connect to Server]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A04SSMSConnectToServer.png
  [Create new database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A05SSMScreateNewDBlh.png
  [Provide database name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A06SSMSprovideDBname.png
  [MembershipDB created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/A07SSMSMembershipDBCreated.png
  [New button]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B01New.png
  [New website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B02NewWebsite.png
  [Website name]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B03WebsiteCreationBlade.png
  [Website running]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B04WebSiteRunningBlade.png
  [Click browse to see your website]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B05Browse.png
  [Default website page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/B06DefaultWebSitePage.png
  [Hybrid connections]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C01CreateHCHCIcon.png
  [Add a hybrid connnection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C02CreateHCAddHC.png
  [Create a hybrid connection]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C03TwinCreateHCBlades.png
  [Create BizTalk service]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C04CreateHCCreateBTS.png
  [Click OK]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C05CreateBTScomplete.png
  [Success notification]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C06CreateHCSuccessNotification.png
  [One hybrid connection created]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/C07CreateHCOneConnectionCreated.png
  [Hybrid connections icon]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D01HCIcon.png
  [Not connected]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D02NotConnected.png
  [NotConnectedBlade]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D03NotConnectedBlade.png
  [Click Listener Setup]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D04ClickListenerSetup.png
  [Click here to install]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D05ClickToInstallHCM.png
  [Choose Run to continue]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D06ApplicationRunWarning.png
  [Choose Yes]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D07UAC.png
  [インストール]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D08HCMInstalling.png
  [Click Close]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D09HCMInstallComplete.png
  [Connected Status]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/D10HCStatusConnected.png
  [New Visual Studio project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E01HCVSNewProject.png
  [Choose ASP.NET Web Application]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E02HCVSChooseASPNET.png
  [Choose MVC]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E03HCVSChooseMVC.png
  [Readme page]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E04HCVSReadmePage.png
  [web.config]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E05HCVSChooseWebConfig.png
  [接続文字列]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSConnectionString.png
  [特定のポートでリッスンするように SQL Server を構成する方法]: http://support.microsoft.com/kb/823938
  [Run project]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E06HCVSRunProject.png
  [Register a new account]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E07HCVSRegisterLocally.png
  [Enter user name and password]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/E08HCVSCreateNewAccount.png
  [発行プロファイルのダウンロード]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F01PortalDownloadPublishProfile.png
  [Publish profile in downloads folder]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F02HCVSPublishProfileInDownloadsFolder.png
  [Select publish]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F03HCVSRightClickProjectSelectPublish.png
  [インポート]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F04HCVSPublishWebDialogImport.png
  [Browse to profile]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F05HCVSBrowseToImportPubProfile.png
  [発行 のクリック]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F06HCVSClickPublish.png
  [Test log in]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F07HCTestLogIn.png
  [Hello greeting]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F08HCTestHelloContoso.png
  [Test register another user]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F09HCTestRegisterRelecloud.png
  [View the results]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F10HCTestSSMSTree.png
  [Registered users in on-premises database]: ./media/web-sites-hybrid-connection-connect-on-premises-sql-server/F11HCTestShowMemberDb.png
  [Josh Twist introduces hybrid connections (Josh Twist によるハイブリッド接続の紹介) (チャネル 9 ビデオ)]: http://channel9.msdn.com/Shows/Azure-Friday/Josh-Twist-introduces-hybrid-connections
  [ハイブリッド接続の Web サイト]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [BizTalk サービス: ダッシュボード、監視、および スケール タブ]: http://azure.microsoft.com/ja-jp/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
  [Building a Real-World Hybrid Cloud with Seamless Application Portability (シームレスなアプリケーションの移植性を使用して実際のハイブリッド クラウドをビルドする) (チャネル 9 ビデオ)]: http://channel9.msdn.com/events/TechEd/NorthAmerica/2014/DCIM-B323#fbid=
  [ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する]: http://azure.microsoft.com/ja-jp/documentation/articles/mobile-services-dotnet-backend-hybrid-connections-get-started/
  [Connect to an on-premises SQL Server from Azure Mobile Services using Hybrid Connections (ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する) (チャネル 9 ビデオ)]: http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Connect-to-an-on-premises-SQL-Server-from-Azure-Mobile-Services-using-Hybrid-Connections
  [ASP.NET の Id の概要]: http://www.asp.net/identity
