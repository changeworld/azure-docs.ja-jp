<properties 
	pageTitle="ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する - Azure Mobile Services" 
	description="ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する方法について説明します。" 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

  
# ハイブリッド接続を使用して Azure Mobile Services から内部設置型の SQL Server に接続する 

企業がクラウドに移行する際に、技術、コンプライアンス、またはセキュリティ上の理由で、アセットを内部設置型にすることが必要になる場合があります。Mobile Services を使用すると、クラウドでホストされるモビリティ レイヤーをアセットの上部に容易に作成できるのと同時に、これらをハイブリッド接続を使用して安全に社内に接続できます。サポートされているアセットには、Microsoft SQL Server、MySQL、HTTP Web APIs、およびほとんどのカスタム Web サービスなど、静的 TCP ポートで実行されるすべてのリソースが含まれます。 

このチュートリアルでは、サービスでプロビジョニングされた既定の Azure SQL Database の代わりに、ローカルの内部設置型の SQL Server データベースを使用するように .NET バックエンド モバイル サービスを変更する方法について説明します。ハイブリッド接続は JavaScript バックエンド モバイル サービスをサポートしていますが、このトピックでは .NET バックエンド モバイル サービスのみを取り扱います。

このトピックでは、次の基本的な手順を説明します。

1. [前提条件](#Prerequisites)
2. [SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する](#InstallSQL)
3. [ハイブリッド接続を作成する](#CreateHC)
4. [内部設置型の Hybrid Connection Manager をインストールして接続を完了する](#InstallHCM)
5. [接続を使用するように Mobile Service を変更する](#CreateService)

<a name="Prerequisites"></a>
##前提条件##
このチュートリアルを完了するには、以下の製品が必要です。すべて無料版を利用できるため、Azure の開発を完全に無料で始めることができます。

- **Visual Studio 2013** - Visual Studio 2013 の無料試用版のダウンロードについては、「[Visual Studio のダウンロード](http://www.visualstudio.com/downloads/download-visual-studio-vs)」を参照してください。これをインストールしてから、次に進みます。

- **SQL Server 2014 Express with Tools** - [Microsoft Web プラットフォーム データベースのページ](http://www.microsoft.com/web/platform/database.aspx)で Microsoft SQL Server Express の無料版をダウンロードします。**Express** (LocalDB ではない) 版を選択します。**Express with Tools** 版には、このチュートリアルで使用する SQL Server Management Studio が含まれています。

ハイブリッド接続を使用して Azure に接続する内部設置型のコンピューターも必要になります。このコンピューターは次の条件を満足している必要があります。

- ポート 5671 経由で Azure に接続できる
- 接続する内部設置型のリソースの  *hostname*:*portnumber*にアクセスできる。リソースが同じコンピューターでホストされているかどうかは問いません。 

<a name="InstallSQL"></a>
## SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する

ハイブリッド接続で内部設置型の SQL Server または SQL Server Express のデータベースを使用するには、TCP/IP が静的ポートで有効になっている必要があります。SQL Server の既定のインスタンスは静的ポート 1433 を使用しますが、名前付きインスタンスは静的ポート 1433 を使用しません。 

上に示した条件を満たすように SQL Server を構成する方法の詳細な手順については、[SQL Server Express をインストールし、TCP/IP を有効にして、内部設置型の SQL Server データベースを作成する](/ja-jp/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#InstallSQL).方法に関するページを参照してください。上記の条件を満たす構成および環境に既に SQL Server をインストールしている場合は、その手順をスキップし、[内部設置型の SQL Server データベースを作成する](/ja-jp/documentation/articles/web-sites-hybrid-connection-connect-on-premises-sql-server#CreateSQLDB). 方法から開始できます。

このチュートリアルでは、データベース名が **OnPremisesDB** で、ポート **1433** で実行され、コンピューターのホスト名が **onPremisesServer** であると想定しています。

<a name="CreateHC"></a>
## ハイブリッド接続を作成する
1. 内部設置型のコンピューターで、[Azure の管理ポータル](http://go.microsoft.com/fwlink/p/?linkid=213885&clcid=0x409)にログオンします。

2. ナビゲーション ペインの下部にある **[+新規]** を選択し、**[アプリケーション サービス]**、**[BizTalk サービス]**、**[カスタム作成]** の順に選択します。

	![Create BizTalk Service][CreateBTS]

3. **BizTalk サービス名**を指定し、**エディション**を選択します。 

	![Configure new BizTalk Service][ConfigureBTS]

	このチュートリアルでは、**mobile1** を使用します。新しい BizTalk サービスに一意の名前を指定する必要があります。

4. BizTalk サービスが作成されたら、**[ハイブリッド接続]** タブをクリックし、**[追加]** をクリックします。

	![Add Hybrid Connection][AddHC]

	これで、新しいハイブリッド接続が作成されます。

5. ハイブリッド接続の**名前**と**ホスト名**を指定し、**ポート**を `1433`. に設定します。 
  
	![Configure Hybrid Connection][ConfigureHC]

	ホスト名は内部設置型のサーバーの名前です。これで、ポート 1433 で実行されている SQL Server にアクセスするようにハイブリッド接続が構成されます。

6. 新しい接続が作成されると、次のテーブルに表示されます。 
 
	![Hybrid Connection successfully created][HCCreated]
	
	新しい接続の状態に、**"内部設置型設定が完了していません"** と表示されます。

ここで、内部設置型のコンピューターに Hybrid Connection Manager をインストールする必要があります。

<a name="InstallHCM"></a>
## 内部設置型の Hybrid Connection Manager をインストールして接続を完了する

Hybrid Connection Manager を使用すると、内部設置型のコンピューターを Azure に接続して TCP トラフィックを中継できます。このソフトウェアを、Azure からアクセスする内部設置型のコンピューターにインストールする必要があります。

1. 作成した接続を選択し、下部のバーにある **[内部設置型設定]** をクリックします。

	![On-Premises Setup][DownloadHCM]

4. **[インストールと構成]** をクリックします。

	![Install Hybrid Connection Manager][InstallHCM]

	これで、Hybrid Connection Manager のカスタマイズされたインスタンスがインストールされます。これは、作成したハイブリッド接続を使用するように、既に事前構成されています。

3. Hybrid Connection Manager の設定手順の残りを完了します。

	![Hybrid Connection Manager setup][HCMSetup]

	インストールが完了すると、ハイブリッド接続の状態が "**1 インスタンス接続済み**" に変更されます。ブラウザーの表示を更新し、数分間待つことが必要になる場合があります。内部設置型設定がこれで完了しました。

	![Hybrid Connection connected][HCConnected]

<a name="CreateService"></a>
## 接続を使用するようにモバイル サービスを変更する
### ハイブリッド接続とサービスの関連付け
1. ポータルの **[モバイル サービス]** タブをクリックし、既存のモバイル サービスを選択するか、新しいモバイル サービスを作成します。 

	>[AZURE.NOTE]必ず、.NET バックエンドを使用して作成されたサービスを選択するか、新しい .NET バックエンド モバイル サービスを作成してください。新しい .NET バックエンド モバイル サービスを作成する方法については、「[Mobile Services の使用](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) 」を参照してください。

2. モバイル サービスの **[構成]** タブで、**[ハイブリッド接続]** セクションを見つけて、**[ハイブリッド接続の追加]** を選択します。

	![Associate Hybrid Connection][AssociateHC]

3. [BizTalk サービス] タブで作成したハイブリッド接続を選択し、**[OK]** をクリックします。 

	![Pick associated Hybrid Connection][PickHC]

これで、モバイル サービスが新しいハイブリッド接続に関連付けられました。

### 内部設置型接続文字列を使用するためのサービスの更新
最後に、内部設置型の SQL Server に接続文字列の値を格納するためのアプリケーション設定を作成する必要があります。新しい接続文字列を使用するには、モバイル サービスを変更する必要があります。 

1. **[接続文字列]** の **[構成]** タブで、 `OnPremisesDatabase` という名前の新しい接続文字列を追加し、 `Server=onPremisesServer,1433;Database=OnPremisesDB;User ID=sa;Password={password}` のような値を設定します。

	![Connection string for on-premises database][ConnectionString]

	 `{password}` をセキュリティ保護されたパスワードと置き換えます。

2. **[保存]** をクリックして、作成したハイブリッド接続と接続文字列を保存します。

3. Visual Studio 2013 で、.NET ベースのモバイル サービスを定義したプロジェクトを開きます。 

	.NET バックエンド プロジェクトのダウンロード方法については、「[Mobile Services の使用](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/) .」を参照してください。
 
4. ソリューション エクスプローラーで **Models** フォルダーを展開し、 *Context.cs* で終わるデータ モデル ファイルを開きます。

6. **DbContext** インスタンス コンストラクターを次のスニペットのように変更します。

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDatabase")
            {
            }
        
            // snipped
        }

	これで、Azure に定義された新しい接続文字列が使用されるようになります。

5. 変更を公開し、モバイル サービスに接続されたクライアント アプリケーションを使用して、データベースの変更を生成する操作を呼び出します。

6. SQL Server が実行されている内部設置型のコンピューターの SQL Management Studio を開き、オブジェクト エクスプローラーで、**OnPremisesDB** データベースを展開し、**Tables** を展開します。 

9. **hybridService1.TodoItems** テーブルを右クリックし、**[先頭の 1000 行を選択]** をクリックすると、結果が表示されます。

	![SQL Management Studio][SMS]

アプリケーションで生成された変更が、モバイル サービスによって内部設置型データベースに公開されました。

##関連項目##
 
+ [ハイブリッド接続の Web サイト](http://azure.microsoft.com/services/biztalk-services/)
+ [ハイブリッド接続の概要](http://go.microsoft.com/fwlink/p/?LinkID=397274)
+ [BizTalk Services:[ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] タブ](http://azure.microsoft.com/documentation/articles/biztalk-dashboard-monitor-scale-tabs/)

<!-- IMAGES -->
 
[CreateBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/1.png
[ConfigureBTS]: ./media/mobile-services-dotnet-backend-hybrid-connections-get-started/2.png
[AddHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/3.png
[ConfigureHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/4.png
[HCCreated]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5.png
[InstallHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/6.png
[HCMSetup]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/7.png
[HCConnected]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/8.png
[AssociateHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/9.png
[PickHC]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/10.png
[ConnectionString]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png
[SMS]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/12.png
[DownloadHCM]:./media/mobile-services-dotnet-backend-hybrid-connections-get-started/5-1.png


<!--HONumber=42-->
