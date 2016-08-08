<properties
	pageTitle="ユニバーサル Windows アプリでのオフライン データの競合の処理 | Microsoft Azure"
	description="Azure Mobile Services を使用して、ユニバーサル Windows アプリケーションでのオフライン データの同期時に発生する競合を処理する方法について説明します。"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="glenga"/>


# モバイル サービスでのオフライン データの同期との競合の処理

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##概要

このトピックでは、Azure Mobile Services のオフライン機能を使用しているときに、データを同期し、競合を処理する方法について説明します。

このチュートリアルのビデオを見る場合は、下のクリップでこのチュートリアルと同じ手順が表示されます。

> [AZURE.VIDEO build-offline-apps-with-mobile-services]

このチュートリアルでは、オフラインの同期で発生する競合の処理をサポートするアプリケーション用に、ユニバーサル Windows C# ソリューションをダウンロードします。モバイル サービスとアプリケーションを統合します。次に、Windows ストア 8.1 と Windows Phone 8.1 のクライアントを実行して同期の競合を生成して、これを解決します。

このチュートリアルは、前の「[オフライン データの使用]」チュートリアルの手順およびサンプル アプリケーションを基に作成されています。このチュートリアルを開始する前に、「[オフライン データの使用]」チュートリアルを完了している必要があります。


##前提条件

このチュートリアルでは、Windows 8.1 で実行されている Visual Studio 2013 が必要です。


##サンプル プロジェクトのダウンロード

![][0]

このチュートリアルは、[Todo オフラインのモバイル サービスのサンプル]を使用してローカルのオフライン ストアと Azure のモバイル サービス データベースとの間の同期の競合を処理する方法について説明します。

1. [Mobile Services のサンプルの GitHub リポジトリ]にある zip ファイルをダウンロードし、作業ディレクトリに展開します。

2. 「[オフライン データの使用]」チュートリアルで説明された手順で SQLite for Windows 8.1 および Windows Phone 8.1 をインストールしていない場合は、両方をランタイムでインストールします。

3. Visual Studio 2013 で、*mobile-services-samples\\TodoOffline\\WindowsUniversal\\TodoOffline-Universal.sln* ソリューション ファイルを開きます。**F5** キーを押してプロジェクトをリビルドして実行します。NuGet パッケージが復元され、参照が正しく設定されていることを確認します。

    >[AZURE.NOTE] SQLite ランタイムへの以前の参照を削除し、これを「[オフライン データの使用]」チュートリアルで説明したように、更新された参照で置き換える必要がある場合があります。

4. アプリケーションの [**TodoItem の挿入**] にテキストを入力し、[**保存**] をクリックして、ローカルのストアに Todoitem を追加します。次に、アプリケーションを閉じます。

アプリケーションはモバイル サービスにまだ接続されていないため、**[プッシュ]** および **[プル]** は例外をスローすることに注意してください。




##モバイル サービスに対するアプリケーションのテスト

次に、Mobile Services に対してアプリケーションをテストします。

1. [Azure クラシック ポータル]で、**[ダッシュボード]** タブのコマンド バーの **[キーの管理]** をクリックしてモバイル サービスのアプリケーション キーを検索します。**アプリケーション キー**をコピーします。

2. Visual Studio のソリューション エクスプローラーで、クライアント サンプル プロジェクトの App.xaml.cs ファイルを開きます。**MobileServiceClient** の初期化を変更して、モバイル サービス URL およびアプリケーション キーを使用します。

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Visual Studio で、**F5** キーを押してアプリケーションをビルドおよび再実行します。

    ![][0]


##競合を作成するためのバックエンドでのデータの更新

現実のシナリオでは、1 つのアプリケーションがデータベースのレコードに更新をプッシュし、次に別のアプリケーションがそのレコードの使用していないバージョンを使用して同じレコードへプッシュしようとする場合に同期の競合が発生します。「[オフライン データの使用]」にあるように、オフラインでの同期の機能をサポートするにはバージョンのシステム プロパティが必要です。このバージョン情報は、データベースの更新ごとに検証されます。アプリケーションのインスタンスが以前のバージョンを使用してレコードを更新しようとすると、競合が発生し、アプリケーションで `MobileServicePreconditionFailedException` として捕捉されます。アプリケーションが `MobileServicePreconditionFailedException` を捕捉しなかった場合は、発生した同期エラーの数を示す `MobileServicePushFailedException` が最終的にスローされます。

>[AZURE.NOTE] オフライン データの同期で削除されたレコードの同期をサポートするには、[論理削除](mobile-services-using-soft-delete.md)を有効にする必要があります。それ以外の場合は、ローカル ストアのレコードを手動で削除するか、`IMobileServiceSyncTable::PurgeAsync()` を呼び出してローカル ストアを消去する必要があります。


次の手順では、同時に実行する Windows Phone 8.1 クライアントと Windows ストア 8.1 のクライアントに競合が発生し、サンプルを使用してこれを解決する方法を示します。

1. Visual Studio で、Windows Phone 8.1 プロジェクトを右クリックし、[**スタートアップ プロジェクトに設定**] をクリックします。次に、**Ctrl+F5** キーを押して、デバッグせずに Windows Phone 8.1 クライアントを実行します。エミュレーターで Windows Phone 8.1 クライアントが起動したら、[**プル**] をクリックして、ローカル ストアとデータベースの現在の状態を同期します。

    ![][3]


2. Visual Studio で Windows 8.1 ランタイム プロジェクトを右クリックしてから [**スタートアップ プロジェクトに設定**] をクリックして、もう一度スタートアップ プロジェクトに設定します。次に、**F5** を押して実行します。Windows ストア 8.1 クライアントが起動したら、[**プル**] をクリックして、ローカル ストアとデータベースの現在の状態を同期します。

    ![][4]

3. この時点では、両方のクライアントはデータベースと同期しています。両クライアントのコードは増分同期も使用しているため、完了していない Todo 項目のみ同期します。完了した Todo 項目は無視されます。項目のいずれかを選択し、両クライアントで同じ項目のテキストを別の値に編集します。[**プッシュ**] をクリックして両方の変更と、サーバーのデータベースを同期します。

    ![][5]

    ![][6]


4. 最後にプッシュを実行したクライアントには競合が発生しますが、ユーザーはデータベースにコミットする値をどちらにするか決定できます。例外により、競合を解決するために使用する正しいバージョンの値が提供されます。

    ![][7]



##同期の競合を処理するためのコードの確認

Mobile Services でオフライン機能を使用するためには、ローカル データベースとデータ転送オブジェクトの両方に [バージョン] 列を含める必要があります。これは `TodoItem` クラスを次のメンバーで更新することによって実行されます。

        [Version]
        public string Version { get; set; }

`TodoItem` クラスを使用してローカル ストアが定義されるときに、`OnNavigatedTo()` メソッドで `__version` 列がローカル データベースに含まれます。

コードでオフラインの同期の競合を処理するには、`IMobileServiceSyncHandler` を実装するクラスを作成します。呼び出しのこの型のオブジェクトを `MobileServiceClient.SyncContext.InitializeAsync()` に渡します。これは、サンプルの `OnNavigatedTo()` メソッドにも発生します。

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

**SyncHandler.cs** のクラス `SyncHandler` が `IMobileServiceSyncHandler` を実装します。メソッド `ExecuteTableOperationAsync` は、各プッシュ操作がサーバーに送信されるときに呼び出されます。型 `MobileServicePreconditionFailedException` の例外がスローされると、項目のローカルとリモートのバージョン間で競合が発生していることを意味します。

ローカル項目を優先して競合を解決するには、操作を再試行します。競合が発生すると、ローカル項目のバージョンはサーバーのバージョンと一致するように更新されるため、操作をもう一度実行するとサーバーの変更はローカルの変更で上書きされます。

    await operation.ExecuteAsync();

サーバー項目を優先して競合を解決するには、`ExecuteTableOperationAsync` から戻ります。オブジェクトのローカル バージョンは破棄され、サーバーの値に置き換えられます。

プッシュ操作を停止する (ただし、キューに設定された変更は保持) には、`AbortPush()` メソッドを使用します。

    operation.AbortPush();

これによって現在のプッシュ操作は停止しますが、`AbortPush` が `ExecuteTableOperationAsync` から呼び出される場合は、現在の操作を含めて、すべての保留中の変更は保存されます。次回 `PushAsync()` が呼び出されると、これらの変更はサーバーに送信されます。

プッシュがキャンセルされると、`PushAsync` は `MobileServicePushFailedException` をスローし、例外プロパティ `PushResult.Status` の値は `MobileServicePushStatus.CancelledByOperation` となります。



<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=394787
[Get started with Mobile Services]: ../mobile-services-windows-store-get-started.md
[オフライン データの使用]: mobile-services-windows-store-dotnet-get-started-offline-data.md
[SQLite for Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure クラシック ポータル]: https://manage.windowsazure.com/
[Handling Database Conflicts]: mobile-services-windows-store-dotnet-handle-database-conflicts.md#test-app
[Mobile Services のサンプルの GitHub リポジトリ]: http://go.microsoft.com/fwlink/?LinkId=512865
[Todo オフラインのモバイル サービスのサンプル]: http://go.microsoft.com/fwlink/?LinkId=512866

<!---HONumber=AcomDC_0727_2016-->