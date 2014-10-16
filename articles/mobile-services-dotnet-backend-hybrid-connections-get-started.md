<properties linkid="mobile-services-dotnet-backend-hybrid-connections-get-started" urlDisplayName="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" pageTitle="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections - Azure Mobile Services" metaKeywords="" description="Learn how to connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" metaCanonical="" services="" documentationCenter="Mobile" title="Connect to an on-premises SQL Server from an Azure mobile service using Hybrid Connections" authors="yavorg" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg"></tags>

# ハイブリッド接続を使用して Azure のモバイル サービスから内部設置型の SQL Server に接続する

企業がクラウドに移行する際に、技術、コンプライアンス、またはセキュリティ上の理由で、アセットを内部設置型にすることが必要になる場合があります。Mobile Services を使用すると、クラウドでホストされるモビリティ レイヤーをアセットの上部に容易に作成できるのと同時に、これらをハイブリッド接続を使用して安全に社内に接続できます。サポートされているアセットには、Microsoft SQL Server、MySQL、HTTP Web APIs、およびほとんどのカスタム Web サービスなど、静的 TCP ポートで実行されるすべてのリソースが含まれます。

このチュートリアルでは、サービスでプロビジョニングされた既定の Azure SQL Database の代わりに、ローカルの内部設置型の SQL Server データベースを使用するように .NET バックエンド モバイル サービスを変更する方法について説明します。ハイブリッド接続は JavaScript バックエンド モバイル サービスをサポートしていますが、このトピックでは .NET バックエンド モバイル サービスのみを取り扱います。

このトピックでは、次の基本的な手順を説明します。

1.  [前提条件][]
2.  [SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する][]
3.  [ハイブリッド接続を作成する][]
4.  [内部設置型の Hybrid Connection Manager をインストールして接続を完了する][]
5.  [接続を使用するようにモバイル サービスを変更する][]

<a name="Prerequisites"></a>

## 前提条件

このチュートリアルを完了するには、以下の製品が必要です。すべて無料版を利用できるため、Azure の開発を完全に無料で始めることができます。

-   **Visual Studio 2013** - Visual Studio 2013 の無料試用版のダウンロードについては、「[Visual Studio のダウンロード][]」を参照してください。これをインストールしてから、次に進みます。

-   **SQL Server 2014 Express with Tools** - [Microsoft Web プラットフォーム データベースのページ][]で Microsoft SQL Server Express の無料版をダウンロードします。**Express** (LocalDB ではない) 版を選択します。**Express with Tools** 版には、このチュートリアルで使用する SQL Server Management Studio が含まれています。

ハイブリッド接続を使用して Azure に接続する内部設置型のコンピューターも必要になります。このコンピューターは次の条件を満足している必要があります。

-   ポート 5671 経由で Azure に接続できる
-   接続する内部設置型のリソースの *hostname*:*portnumber* にアクセスできる。リソースが同じコンピューターでホストされているかどうかは問いません。

<a name="InstallSQL"></a>

## SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する

ハイブリッド接続で内部設置型の SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server の既定のインスタンスは静的ポート 1433 を使用しますが、名前付きインスタンスは静的ポート 1433 を使用しません。

上に示した条件を満たすように SQL Server を構成する方法の詳細な手順については、「[SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する][1]」を参照してください。上記の条件を満たす構成および環境に既に SQL Server をインストールしている場合は、この手順をスキップし、「[内部設置型の SQL Server を作成する][]」から開始できます。

このチュートリアルでは、データベース名が **OnPremisesDB** で、ポート **1433** で実行され、コンピューターのホスト名が **onPremisesServer** であると想定しています。

<a name="CreateHC"></a>

## ハイブリッド接続を作成する

1.  内部設置型のコンピューターで、[Azure の管理ポータル][]にログオンします。

2.  ナビゲーション ペインの下部にある **[+新規]** を選択し、**[アプリケーション サービス]**、**[BizTalk サービス]**、**[カスタム作成]** の順に選択します。

    ![Create BizTalk Service][]

3.  **BizTalk サービス名**を指定し、**エディション**を選択します。

    ![Configure new BizTalk Service][]

    このチュートリアルでは、**mobile1** を使用します。新しい BizTalk サービスに一意の名前を指定する必要があります。

4.  BizTalk サービスが作成されたら、**[ハイブリッド接続]** タブをクリックし、**[追加]** をクリックします。

    ![Add Hybrid Connection][]

    これで、新しいハイブリッド接続が作成されます。

5.  ハイブリッド接続の**名前**と**ホスト名**を指定し、**ポート**を `1433` に設定します。

    ![Configure Hybrid Connection][]

    ホスト名は内部設置型のサーバーの名前です。これで、ポート 1433 で実行されている SQL Server にアクセスするようにハイブリッド接続が構成されます。

6.  新しい接続が作成されると、次のテーブルに表示されます。

    ![Hybrid Connection successfully created][]

    新しい接続の状態に、**内部設置型設定が未完了**と表示されます。

ここで、内部設置型のコンピューターに Hybrid Connection Manager をインストールする必要があります。

<a name="InstallHCM"></a>

## 内部設置型の Hybrid Connection Manager をインストールして接続を完了する

Hybrid Connection Manager を使用すると、内部設置型のコンピューターを Azure に接続して TCP トラフィックを中継できます。このソフトウェアを、Azure からアクセスする内部設置型のコンピューターにインストールする必要があります。

1.  作成した接続を選択し、下部のバーにある **[内部設置型設定]** をクリックします。

    ![On-Premises Setup][]

2.  **[インストールと構成]** をクリックします。

    ![Install Hybrid Connection Manager][]

    これで、Hybrid Connection Manager のカスタマイズされたインスタンスがインストールされます。これは、作成したハイブリッド接続を使用するように、既に事前構成されています。

3.  Hybrid Connection Manager の設定手順の残りを完了します。

    ![Hybrid Connection Manager setup][]

    インストールが完了すると、ハイブリッド接続の状態が **"1 インスタンス接続済み"** に変更されます。ブラウザーの表示を更新し、数分間待つことが必要になる場合があります。内部設置型設定がこれで完了しました。

    ![Hybrid Connection connected][]

<a name="CreateService"></a>

## 接続を使用するようにモバイル サービスを変更する

### ハイブリッド接続とサービスの関連付け

1.  ポータルの **[モバイル サービス]** タブをクリックし、既存のモバイル サービスを選択するか、新しいモバイル サービスを作成します。

    > [WACOM.NOTE] 必ず、.NET バックエンドを使用して作成されたサービスを選択するか、新しい .NET バックエンド モバイル サービスを作成してください。新しい .NET バックエンド モバイル サービスを作成する方法については、「[モバイル サービスの使用][]」を参照してください。

2.  モバイル サービスの **[構成]** タブで、**[ハイブリッド接続]** セクションを見つけて、**[ハイブリッド接続の追加]** を選択します。

    ![Associate Hybrid Connection][]

3.  [BizTalk サービス] タブで作成したハイブリッド接続を選択し、**[OK]** をクリックします。

    ![Pick associated Hybrid Connection][]

これで、モバイル サービスが新しいハイブリッド接続に関連付けられました。

### 内部設置型接続文字列を使用するためのサービスの更新

最後に、内部設置型の SQL Server に接続文字列の値を格納するためのアプリケーション設定を作成する必要があります。新しい接続文字列を使用するには、モバイル サービスを変更する必要があります。

1.  **[アプリケーション設定]** の **[構成]** タブで、名前が `onPremisesDatabase`、値が `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}` の新しいアプリケーション設定を追加します。

    ![App setting for connection string][]

    `{password}` をセキュリティ保護されたパスワードと置き換えます。

2.  **[保存]** をクリックして、作成したハイブリッド接続とアプリケーション設定を保存します。

3.  Visual Studio 2013 で、.NET ベースのモバイル サービスを定義したプロジェクトを開きます。

    .NET バックエンド プロジェクトのダウンロード方法については、「[モバイル サービスの使用][]」を参照してください。

4.  ソリューション エクスプローラーで **Models** フォルダーを展開し、*Context.cs* で終わるデータ モデル ファイルを開きます。

5.  **DbContext** インスタンス コンストラクターを次のスニペットのように変更します。

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base(ConfigurationManager.AppSettings["onPremisesDatabase"])
            {
            }

            // snipped
        }

    Azure のアプリケーション設定で、サービスが新しいハイブリッド接続文字列定義を使用するようになりました。

6.  変更を公開し、モバイル サービスに接続されたクライアント アプリケーションを使用して、データベースの変更を生成する操作を呼び出します。

7.  SQL Server が実行されている内部設置型のコンピューターの SQL Management Studio を開き、オブジェクト エクスプローラーで、**OnPremisesDB** データベースを展開し、**Tables** を展開します。

8.  **hybridService1.TodoItems** テーブルを右クリックし、**[先頭の 1000 行を選択]** をクリックすると、結果が表示されます。

    ![SQL Management Studio][]

アプリケーションで生成された変更が、モバイル サービスによって内部設置型データベースに公開されました。

## 関連項目

-   [ハイブリッド接続の Web サイト][]
-   [ハイブリッド接続の概要][]
-   [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ][]

<!-- IMAGES -->

  [前提条件]: #Prerequisites
  [SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する]: #InstallSQL
  [ハイブリッド接続を作成する]: #CreateHC
  [内部設置型の Hybrid Connection Manager をインストールして接続を完了する]: #InstallHCM
  [接続を使用するようにモバイル サービスを変更する]: #CreateService
  [Visual Studio のダウンロード]: http://www.visualstudio.com/downloads/download-visual-studio-vs
  [Microsoft Web プラットフォーム データベースのページ]: http://www.microsoft.com/web/platform/database.aspx
  [1]: /ja-jp/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL
  [内部設置型の SQL Server を作成する]: /ja-jp/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB
  [Azure の管理ポータル]: http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409
  [Create BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
  [Configure new BizTalk Service]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
  [Add Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
  [Configure Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
  [Hybrid Connection successfully created]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
  [On-Premises Setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png
  [Install Hybrid Connection Manager]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
  [Hybrid Connection Manager setup]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
  [Hybrid Connection connected]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
  [モバイル サービスの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Associate Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
  [Pick associated Hybrid Connection]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
  [App setting for connection string]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
  [SQL Management Studio]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
  [ハイブリッド接続の Web サイト]: http://azure.microsoft.com/en-us/services/biztalk-services/
  [ハイブリッド接続の概要]: http://go.microsoft.com/fwlink/p/?LinkID=397274
  [BizTalk サービス: [ダッシュボード]、[監視]、および [スケール] タブ]: http://azure.microsoft.com/ja-jp/documentation/articles/biztalk-dashboard-monitor-scale-tabs/
