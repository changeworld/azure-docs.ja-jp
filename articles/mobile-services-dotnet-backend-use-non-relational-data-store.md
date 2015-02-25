<properties pageTitle="非リレーショナル データ ストアを使用するサービスの作成 - Azure Mobile Services" description="MongoDB や Azure テーブル ストレージなどの非リレーショナル データ ストアを .NET ベースのモバイル サービスで使用する方法について説明します。" services="" documentationCenter="windows" authors="mattchenderson" manager="dwrede" editor="mollybos"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="11/21/2014" ms.author="mahender"/>

# .NET バックエンドによる MongoDB をデータ ストアとして使用するサービスの作成

このトピックでは、モバイル サービスで非リレーショナル データ ストアを使用する方法を示します。このチュートリアルでは、Mobile Services クイック スタート プロジェクトを変更して、SQL ではなく MongoDB をデータ ストアとして使用します。

このチュートリアルでは、非リレーショナル ストアを設定するための、次の手順について説明します。

1. [非リレーショナル ストアを作成する]
2. [データとコントローラーを変更する]
3. [アプリケーションをテストする]

このチュートリアルを使用するには、「[Mobile Services の使用]」または「[データの使用]」チュートリアルを完了している必要があります。

## <a name="create-store"></a>非リレーショナル ストアを作成する

1. [Azure の管理ポータル]で、**[新規]** をクリックし、**[ストア]** を選択します。

2. **[MongoLab]** アドオンを選択し、ウィザードの指示に従い、アカウントにサインアップします。MongoLab の詳細については、[MongoLab アドオンのページ]を参照してください。

    ![][0]

2. アカウントが設定されたら、**[接続文字列]** を選択し、接続文字列をすべてコピーします。

3. ポータルの [モバイル サービス] セクションに移動して、**[構成]** タブをクリックします。

4. **[アプリケーション設定]** で、キー "MongoConnectionString" の接続文字列を入力し、**[保存]** をクリックします。

    ![][1]

2.  `TodoItemController` に次のコードを追加します。

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    このコードでは、アプリケーション設定を読み込み、アプリケーション設定を `TableController` が使用できる接続として扱うようモバイル サービスに指示します。後で、 `TodoItemController` が起動されるときにこのメソッドを呼び出します。



## <a name="modify-service"></a>データとコントローラーを変更する

1. **WindowsAzure.MobileServices.Backend.Mongo** パッケージをインストールします。

2.  `TodoItem` を変更し、 `EntityData` ではなく  `DocumentData` から派生するようにします。

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3.  `TodoItemController` で、 `Initialize` メソッドを次のコードで置き換えます。

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. 上の  `Initialize` メソッドのコード内で、**YOUR-DATABASE-NAME** を、MongoLab アドオンをプロビジョニングしたときに選択した名前に置き換えます。


## <a name="test-application"></a>アプリケーションをテストする

1. モバイル サービスのバックエンド プロジェクトを再発行します。

2. アプリケーションを実行します。このクイック スタート チュートリアルでは、以前に SQL データベースに保存した項目は表示されないことに注意してください。

3. 新しいテーブルを作成します。アプリケーションの動作は、以前と同様になります。ただし、データは非リレーショナル ストアに保存されます。


<!-- Anchors. -->
[非リレーショナル ストアを作成する]: #create-store
[データとコントローラーを変更する]: #modify-service
[アプリケーションをテストする]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Mobile Services の使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[データの使用]: /ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Azure の管理ポータル]: https://manage.windowsazure.com/
[テーブル サービスとは]: /ja-jp/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[MongoLab アドオンのページ]: /ja-jp/gallery/store/mongolab/mongolab

<!--HONumber=42-->
