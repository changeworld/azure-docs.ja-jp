<properties 
	pageTitle="Mobile Services での論理削除の使用方法 (Windows ストア) | モバイル デベロッパー センター" 
	description="アプリケーション内で Azure Mobile Services の論理削除機能を使用する方法を説明します。" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="wesmc"/>

# Mobile Services での論理削除の使用方法

##概要

JavaScript または .NET バックエンドで作成されたテーブルは、オプションで論理削除を有効にすることができます。論理削除を使用する場合、*__deleted* という [SQL bit 型]の新しい列がデータベースに追加されます。論理削除を有効にすると、削除操作では列をデータベースから物理的に削除しませんが、deleted 列の値が TRUE に設定されます。

論理削除が有効なテーブルのレコードをクエリした場合、既定では、クエリで削除済みの行が返されることはありません。これらの行を要求するには、クエリ パラメーター *__includeDeleted=true* を [REST クエリ操作](http://msdn.microsoft.com/library/azure/jj677199.aspx)に渡す必要があります。.NET クライアント SDK では、ヘルパー メソッドの `IMobileServiceTable.IncludeDeleted()` を使用することもできます。

.NET バックエンド対応の論理削除サポートは、最初に、Microsoft Azure Mobile Services .NET バックエンドのバージョン 1.0.402 でリリースされました。最新の NuGet パッケージは、「[Microsoft Azure Mobile Services .NET Backend (Microsoft Azure Mobile Services .NET バックエンド)](http://go.microsoft.com/fwlink/?LinkId=513165)」から入手できます。


論理削除の使用において潜在するメリットは次のとおりです。

* [Mobile Services でのオフライン データの同期]機能を使用すると、クライアント SDK は自動的に削除済みのレコードをクエリし、それらをローカル データベースから削除します。論理削除が有効でない場合は、クライアント SDK がローカル ストアからどのレコードを削除するか認識できるように、バックエンドに追加コードを記述する必要があります。記述しないと、クライアント ローカル ストアとバックエンドの間でこれらの削除済みレコードに関して矛盾が生じるため、クライアント メソッド `PurgeAsync()` を呼び出して、ローカル ストアを消去する必要があります。
* アプリケーションによっては、データを決して物理的に削除しない、または監査の後のみデータを削除するなどのビジネス要件があります。論理削除機能は、このようなシナリオで役立ちます。
* 論理削除を使用して "削除の取り消し" 機能を実装することにより、誤って削除されたデータを回復できます。ただし、論理削除済みのレコードはデータベース内の領域を占有するので、スケジュールされたジョブを作成して、論理削除済みのレコードを一定期間ごとに物理的に削除することを検討する必要があります。この例については、「[.NET バックエンドでの論理削除の使用方法]」および「[JavaScript バックエンドでの論理削除の使用方法]」を参照してください。また、これらの物理的に削除されたレコードがデバイスのローカル データ ストアに残存しないように、クライアント コードで定期的に `PurgeAsync()` を呼び出す必要があります。





##.NET バックエンドの論理削除を有効にする

.NET バックエンド対応の論理削除サポートは、最初に、Microsoft Azure Mobile Services .NET バックエンドのバージョン 1.0.402 でリリースされました。最新の NuGet パッケージは、「[Microsoft Azure Mobile Services .NET Backend (Microsoft Azure Mobile Services .NET バックエンド)](http://go.microsoft.com/fwlink/?LinkId=513165)」から入手できます。

次の手順は、.NET バックエンド モバイル サービスの論理削除を有効にする方法を示しています。

1. Visual Studio で、.NET バックエンド モバイル サービス プロジェクトを開きます。
2. .NET バックエンド プロジェクトを右クリックし、**[NuGet パッケージの管理]** をクリックします。 
3. パッケージ マネージャー ダイアログで、更新プログラムの下にある **Nuget.org** をクリックし、[Microsoft Azure Mobile Services .NET バックエンド](http://go.microsoft.com/fwlink/?LinkId=513165) NuGet パッケージのバージョン 1.0.402 以降をインストールします。
3. Visual Studio のソリューション エクスプローラーで、.NET バックエンド プロジェクトの下の **Controllers** ノードを展開し、コントローラー ソースを開きます。たとえば、*TodoItemController.cs*。
4. コントローラーの `Initialize()` メソッドで、`enableSoftDelete: true` パラメーターを EntityDomainManager コンストラクターに渡します。

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


##JavaScript バックエンドの論理削除を有効にする

モバイル サービス用の新しいテーブルを作成している場合は、テーブル作成ページで論理削除を有効にすることができます。

![][2]

JavaScript バックエンドの既存のテーブルで論理削除を有効にするには、次の手順に従います。

1. [管理ポータル]で、モバイル サービスをクリックします。その後、[データ] タブをクリックします。
2. データ ページで、目的のテーブルをクリックして選択します。コマンド バーにある **[論理削除の有効化]** をクリックします。テーブルの論理削除が既に有効な場合、このボタンは表示されませんが、テーブルの [**参照**] または [**列**] タブをクリックすると *_deleted* 列を表示できます。

    ![][0]

    テーブルの論理削除を無効にするには、[**列**] タブをクリックし、その後、*__deleted* 列と [**削除**] をクリックします。

    ![][1]

## <a name="using-with-dotnet"></a>.NET バックエンドでの論理削除の使用方法


次のスケジュールされたジョブは、1 か月を超えた論理削除済みのレコードを消去します。

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

.NET バックエンド Mobile Services でのスケジュール ジョブの詳細については、「[JavaScript バックエンド Mobile Services での繰り返し発生するジョブのスケジュール](mobile-services-dotnet-backend-schedule-recurring-tasks.md)」を参照してください。




##JavaScript バックエンドでの論理削除の使用方法

テーブル スクリプトを使用して、JavaScript バックエンド Mobile Services での論理削除機能に関連するロジックを追加します。

削除の取り消し要求を検出するには、更新テーブル スクリプトで "undelete" プロパティを使用します。
    
    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
削除済みレコードをスクリプトのクエリ結果に含めるには、"includeDeleted" パラメーターを true に設定します。
    
    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

HTTP 要求を介して削除済みレコードを取得するには、"__includedeleted=true" クエリ パラメーターを追加します。

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### 論理削除済みレコードを消去するスケジュールされたジョブのサンプル

これは、特定の日付より前に更新されたレコードを削除するスケジュールされたジョブのサンプルです。

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

JavaScript バックエンド Mobile Services でのスケジュール ジョブの詳細については、「[JavaScript バックエンド Mobile Services での繰り返し発生するジョブのスケジュール](mobile-services-schedule-recurring-tasks.md)」を参照してください。





<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[SQL bit 型]: http://msdn.microsoft.com/library/ms177603.aspx
[Mobile Services でのオフライン データの同期]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[管理ポータル]: https://manage.windowsazure.com/



<!--HONumber=54--> 