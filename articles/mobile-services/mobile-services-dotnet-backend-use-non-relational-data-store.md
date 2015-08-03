<properties 
	pageTitle="非リレーショナル データ ストアを使用するサービスの作成 | Azure Mobile Services" 
	description="MongoDB や Azure テーブル ストレージなどの非リレーショナル データ ストアを .NET ベースのモバイル サービスで使用する方法について説明します。" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# ストレージとして SQL Database の代わりに MongoDB を使用する .NET バックエンド Mobile Service の作成

このトピックでは、.NET バックエンド モバイル サービスで非リレーショナル データ ストアを使用する方法を説明します。このチュートリアルでは、既定の Azure SQL Database データ ストアではなく MongoDB が使用されるように Mobile Services クイック スタート プロジェクトを変更します。

このチュートリアルを受けるには、[Mobile Services の使用]のチュートリアルまたは [Add Mobile Services to an existing app] のチュートリアルを完了している必要があります。また、サブスクリプションに MongoLab サービスを追加する必要もあります。

## <a name="create-store"></a>MongoLab の非リレーショナル ストアを作成する

1. [Azure 管理ポータル]で、[**新規**] 、[**Marketplace**] の順にクリックします。

2. [**MongoLab**] アドオンをクリックし、ウィザードの指示に従って MongoLab アカウントにサインアップします。

	MongoLab の詳細については、[MongoLab Add-on Page (MongoLab アドオンのページ)]を参照してください。

2. アカウントが設定されたら、[**接続情報**] を選択し、接続文字列をコピーします。

3. モバイル サービスで [**構成**] タブをクリックして、下にスクロールして [**接続文字列**] を表示します。`MongoConnectionString` という**名前**で、ユーザーの MongoDB 接続を示す **値**を含む新しい接続文字列を入力して [**保存**] をクリックします。

	![MongoDB 接続文字列を追加する](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	ストレージ アカウント接続文字列は、暗号化されてアプリ設定に格納されます。この文字列は、実行時に任意のテーブル コント ローラーで表示できます。

8. Visual Studio のソリューション エクスプ ローラーで、モバイル サービス プロジェクトの Web.config ファイルを開き、次の新しい接続文字列を追加します。

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. `<MONGODB_CONNECTION_STRING>` プレース ホルダーを、MongoDB 接続文字列で置き換えます。

	モバイル サービスがローカル コンピューター上で実行されるときにこの接続文字列が使用されるため、この文字列を発行前にテストできます。Azure で実行される場合、モバイル サービスにはポータルで設定された接続文字列の値が使用され、プロジェクト内の接続文字列は無視されます。

## <a name="modify-service"></a>データ型とテーブル コント ローラーの変更

1. **WindowsAzure.MobileServices.Backend.Mongo** NuGet パッケージをインストールします。

2. **TodoItem** が、**EntityData** ではなく **DocumentData** から派生するように変更を加えます。

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. **TodoItemController** で、**Initialize** メソッドを次のコードで置き換えます。

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. 前述の **Initialize** メソッドのコード内で、`<YOUR-DATABASE-NAME>` を、MongoLab アドオンをプロビジョニングしたときに選択した名前に置き換えます。

これで、アプリをテストする準備ができました。

## <a name="test-application"></a>アプリケーションをテストする

1. モバイル サービスの .NET バックエンド プロジェクトを再発行します (省略可能)。

	.NET バックエンド プロジェクトを Azure に発行する前に、モバイル サービスをローカルでテストすることもできます。ローカルまたは Azure でテストするかどうかにかかわらず、モバイル サービスでは MongoDB がストレージに使用されます。

4. これまでのようにスタート ページの [**今すぐ試す**] ボタン、または Mobile App に接続されたクライアント アプリのいずれかを使用して、データベースの項目をクエリします。
 
	このクイック スタート チュートリアルでは、以前に SQL Database に保存した項目は表示されないことに注意してください。

	>[AZURE.NOTE] **[今すぐ試す]** ボタンを使用して API のヘルプ ページを起動する場合は、アプリケーション キーをパスワードとして指定します (ユーザー名は空白にします)。

3. 新しいテーブルを作成します。

	アプリとモバイル サービスは以前と同様に動作しますが、データが格納される場所が SQL Database ではなく非リレーショナル ストアになったという点が異なります。

##次のステップ

ここまでで、テーブル ストレージを .NET バックエンドに使用するのがいかに簡単かを説明したので、次に示すその他のバックエンド ストレージ オプションの使用も検討してみてください。

+ [Build a .NET backend Mobile Service that uses Table storage instead of a SQL Database (SQL Database ではなくデータ ストアを使用する .NET バックエンド Mobile Service の作成)](mobile-services-dotnet-backend-store-data-table-storage.md)</br>完了したばかりのチュートリアルと同じように、このトピックでは、モバイル サービスに非リレーショナル データ ストアを使用する方法を紹介しています。このチュートリアルでは、データ ストアとして SQL Database ではなく Azure Storage が使用されるように Mobile Services クイック スタート プロジェクトを変更します。
 
+ [ハイブリッド接続を使用して Azure Mobile Services からオンプレミスの SQL Server に接続する](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>ハイブリッド接続により、モバイル サービスを安全にオンプレミスのアセットに接続します。この方法で、モバイル クライアントが Azure を使用することによってオンプレミスのデータにアクセスできるようにします。サポートされているアセットには、Microsoft SQL Server、MySQL、HTTP Web APIs、およびほとんどのカスタム Web サービスなど、静的 TCP ポートで実行されるすべてのリソースが含まれます。

+ [Mobile Services を使用した Azure Storage へのイメージのアップロード](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>TodoList サンプル プロジェクトを拡張して、アプリケーションから Azure Blob ストレージにイメージをアップロードする方法を説明します。


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Mobile Services の使用]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Add Mobile Services to an existing app]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure 管理ポータル]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page (MongoLab アドオンのページ)]: /gallery/store/mongolab/mongolab
 

<!---HONumber=July15_HO4-->