<properties
	pageTitle="ハイブリッド接続を使用して .NET バックエンド モバイル サービスからオンプレミスの SQL Server に接続する | Azure Mobile Services"
	description="Azure ハイブリッド接続を使用して .NET バックエンド モバイル サービスからオンプレミスの SQL Server に接続する方法について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="glenga"/>


# ハイブリッド接続を使用して Azure Mobile Services からオンプレミスの SQL Server に接続する

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> このトピックの Mobile Apps バージョンについては、「[Access on-premises resources using hybrid connections in Azure App Service (Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする)](../app-service-web/web-sites-hybrid-connection-get-started.md)」を参照してください。

企業がクラウドに移行するときに、すべての資産を Azure にすぐに移行できない場合があります。ハイブリッド接続を使用すると、Azure Mobile Services からオンプレミスの資産に安全に接続できます。この方法で、モバイル クライアントが Azure を使用することによってオンプレミスのデータにアクセスできるようにします。サポートされている資産には、Microsoft SQL Server、MySQL、HTTP Web APIs、およびほとんどのカスタム Web サービスなど、静的 TCP ポートで実行されるすべてのリソースが含まれます。ハイブリッド接続では、Shared Access Signature (SAS) 認証を使用して、モバイル サービスとオンプレミスの Hybrid Connection Manager からハイブリッド接続への接続を保護します。詳細については、[ハイブリッド接続の概要](../biztalk-services/integration-hybrid-connection-overview.md)を参照してください。

このチュートリアルでは、サービスでプロビジョニングされた既定の Azure SQL Database の代わりに、ローカルのオンプレミスの SQL Server データベースを使用するように .NET バックエンド モバイル サービスを変更する方法について説明します。ハイブリッド接続は JavaScript バックエンド モバイル サービスでもサポートされており、[この記事](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx)でも説明されています。

##前提条件##

このチュートリアルを完了するには、次のものが必要です。

- **既存の .NET バックエンド モバイル サービス** <br/>[Mobile Services の使用]チュートリアルに従がって、新しい .NET バックエンド モバイル サービスを [Azure クラシック ポータル]から作成およびダウンロードします。

[AZURE.INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## SQL Server Express をインストールし、TCP/IP を有効にして、オンプレミスの SQL Server データベースを作成する

[AZURE.INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## ハイブリッド接続を作成する

[AZURE.INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## オンプレミスのハイブリッド接続マネージャーをインストールして接続を完了する

[AZURE.INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## SQL Server データベースに接続するモバイル サービス プロジェクトを構成する

このステップでは、オンプレミスのデータベースに接続するための接続文字列を定義し、この接続が使用されるようにモバイル サービスを変更します。

1. Visual Studio 2013 で、.NET バックエンドのモバイル サービスを定義するプロジェクトを開きます。

	.NET バックエンド プロジェクトのダウンロード方法については、[Mobile Services の使用](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md)を参照してください。

2. ソリューション エクスプローラーで、Web.config ファイルを開き、 **connectionStrings** セクションを探し、次のような新しい SqlClient エントリを追加します。このエントリはオンプレミスの SQL Server データベースを指しています。

	    <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />

	この文字列内の `<**secure_password**>` は、*HbyridConnectionLogin* 用に作成したパスワードに置き換えてください。

3. Visual Studio で **[保存]** をクリックして、Web.config ファイルを保存します。

	> [AZURE.NOTE]この接続設定は、ローカル コンピューターで実行されるときに使用されます。Azure で実行される場合、この設定は、ポータルで定義された接続設定によってオーバーライドされます。

4. **Models** フォルダーを展開し、*Context.cs* で終わるデータ モデル ファイルを開きます。

6. **DbContext** を、値 `OnPremisesDBConnection` をベース **DbContext** コンストラクターに渡すように変更します。次のスニペットに似ています。

        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }

	これで、サービスは、SQL Server データベースへの新しい接続を使用するようになります。

##データベース接続をローカルでテストする

ハイブリッド接続を Azure に発行して使用する前に、ローカルで実行したときにデータベース接続が機能するかどうかを確認することをお勧めします。これによって、接続に関する問題を簡単に診断して修正した後、ハイブリッド接続を再発行して使用を開始できます。

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## オンプレミスの接続文字列を使用するように Azure を更新する

データベース接続の確認が終わったら、この新しい接続文字列が Azure から使用できるようにアプリ設定を追加して、モバイル サービスを Azure に発行する必要があります。

1. [Azure クラシック ポータル]で、モバイル サービスを参照します。

1. [**構成**] タブをクリックして、[**接続文字列**] セクションを参照します。

	![オンプレミスのデータベースの接続文字列](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)

2. `OnPremisesDBConnection` という名前の新しい接続 **SQL Server** 文字列に次のように値を入力して追加します。

		Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>


	`<**secure_password**>` を *HybridConnectionLogin* のセキュリティ保護されたパスワードで置き換えます。

2. [**保存**] をクリックして、今作成したハイブリッド接続と接続文字列を保存します。

3. Visual Studio を使用して、更新済みのモバイル サービス プロジェクトを Azure に発行します。

	サービスのスタート ページが表示されます。

4. これまでのようにスタート ページの [**今すぐ試す**] ボタン、またはモバイル サービスに接続されたクライアント アプリのいずれかを使用して、データベースの変更を生成するいくつかの操作を呼び出します。

	>[AZURE.NOTE] [**今すぐ試す**] ボタンを使用して API のヘルプ ページを起動する場合は、アプリケーション キーをパスワードとして指定します (ユーザー名は空白にします)。

4. SQL Server Management Studio で、SQL Server インスタンスに接続し、オブジェクト エクスプローラーを開き、**OnPremisesDB** データベースを展開し、**[テーブル]** を展開します。

5. **hybridService1.TodoItems** テーブルを右クリックし、[**先頭の 1000 行を選択**] をクリックすると、結果が表示されます。

	アプリで発生した変更が、モバイル サービスによって、ハイブリッド接続を介してオンプレミスのデータベースに保存されたことに注意してください。

##関連項目##

+ [ハイブリッド接続の Web サイト](../../services/biztalk-services/)
+ [ハイブリッド接続の概要](../biztalk-services/integration-hybrid-connection-overview.md)
+ [BizTalk Services: [ダッシュボード]、[監視]、[スケール]、[構成]、および [ハイブリッド接続] タブ](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)
+ [データ モデルの変更を .NET バックエンド モバイル サービスに加える方法](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[Azure クラシック ポータル]: http://manage.windowsazure.com
[Mobile Services の使用]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0309_2016-->