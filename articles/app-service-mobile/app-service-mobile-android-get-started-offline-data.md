<properties
	pageTitle="Azure Mobile App (Android) のオフライン同期の有効化"
	description="App Service Mobile Apps を使用して、Android アプリケーションのオフライン データをキャッシュおよび同期する方法について説明します。"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>

# Android モバイル アプリのオフライン同期の有効化

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## 概要

このチュートリアルでは、Android 向け Azure Mobile Apps のオフライン同期機能について説明します。オフライン同期を使用すると、エンド ユーザーはネットワークにアクセスできなくても、データの表示、追加、変更など、モバイル アプリとやり取りできます。変更はローカル データベースに格納され、デバイスが再びオンラインになると、これらの変更がリモート バックエンドと同期されます。

Azure Mobile Apps を初めて使用する場合は、チュートリアル「[Create an Android App (Android アプリの作成)]」を完了しておく必要があります。ダウンロードしたクイック スタートのサーバー プロジェクトを使用しない場合は、データ アクセス拡張機能パッケージをプロジェクトに追加する必要があります。サーバーの拡張機能パッケージの詳細については、「[Work with the .NET backend server SDK for Azure Mobile Apps (Azure Mobile Apps 用の .NET バックエンド サーバー SDK を操作する)](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」を参照してください。

オフラインの同期機能の詳細については、トピック「[Azure Mobile Apps でのオフライン データ同期]」をご覧ください。

## オフライン同期をサポートするようにアプリケーションを更新する

オフライン同期では、*同期テーブル*に対して読み取りと書き込みを行います (*IMobileServiceSyncTable* インターフェイスを使用)。このテーブルは、デバイス上の **SQLite** データベースの一部です。

デバイスと Azure Mobile Services の間で変更をプッシュしたりプルしたりするには、*同期コンテキスト* (*MobileServiceClient.SyncContext*) を使用します。この同期コンテキストは、データをローカルに格納するローカル データベースで初期化されます。

1. `TodoActivity.java` で、`mToDoTable` の既存の定義をコメント アウトし、同期テーブルのバージョンをコメント解除します。

	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. `onCreate` メソッドで、`mToDoTable` の既存の初期化をコメント アウトし、次の定義をコメント解除します。

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. `refreshItemsFromTable` で、`results` の定義をコメント アウトし、次の定義をコメント解除します。

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. `refreshItemsFromMobileServiceTable` の定義をコメント アウトします。

5. `refreshItemsFromMobileServiceTableSyncTable` の定義をコメント解除します。

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. `sync` の定義をコメント解除します。

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## アプリケーションをテストする

このセクションでは、WiFi 接続した状態で動作をテストしてから、WiFi を切断してオフラインのシナリオを用意します。

データ項目を追加したときに、これらの項目はローカルの SQLite ストアに保持されていますが、**[更新]** ボタンをクリックするまでモバイル サービスと同期されません。他のアプリでは、データを同期させるタイミングに関する要件が異なる場合がありますが、このチュートリアルではデモの目的で、ユーザーが明示的にデータ同期を要求したことにします。

[更新] ボタンを押すと、新しいバック グラウンド タスクが開始し、最初に、同期コンテキストを使用してローカル ストアに加えられたすべての変更がプッシュされます。次に、Azure から変更されたすべてのデータがローカル テーブルにプルされます。

### オフライン テスト

1. デバイスまたはシミュレーターを [*機内モード*] に切り替えます。これでオフラインのシナリオが準備できます。

2. *ToDo* 項目をいくつか追加するか、一部の項目を完了済みとしてマークします。デバイスまたはシミュレーターを終了して (またはアプリケーションを強制的に閉じて)、再起動します。変更はローカルの SQLite ストアに保持されているため、変更がデバイスに保存されたことを確認します。

3. *SQL Server Management Studio* などの SQL ツール、または *Fiddler* や *Postman* などの REST クライアントを使用して、Azure *TodoItem* テーブルの内容を表示します。新しい項目が、サーバーと同期されなかったことを確認します。

   	+ Node.js バックエンドの場合は、[Azure ポータル](https://portal.azure.com/)に移動し、Mobile App バックエンドで **[簡易テーブル]**、**[TodoItem]** の順にクリックして、`TodoItem` テーブルの内容を表示します。
   	+ .NET バックエンドの場合は、*SQL Server Management Studio* などの SQL ツール、または *Fiddler* や *Postman* などの REST クライアントを使用して、テーブルの内容を表示します。

4. デバイスまたはシミュレーターの WiFi を有効にします。次に [**更新**] ボタンを押します。

5. Azure ポータルで、もう一度 TodoItem のデータを参照します。新しく変更した TodoItems が表示されます。

## その他のリソース

* [Azure Mobile Apps でのオフライン データ同期]

* [Cloud Cover: Azure Mobile Services でのオフライン同期] \(注: このビデオは Mobile Services に関するものですが、オフライン同期は Azure Mobile Apps でも同様に機能します)


<!-- URLs. -->

[Azure Mobile Apps でのオフライン データ同期]: ../app-service-mobile-offline-data-sync.md

[Create an Android App (Android アプリの作成)]: ../app-service-mobile-android-get-started.md

[Cloud Cover: Azure Mobile Services でのオフライン同期]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_0727_2016-->