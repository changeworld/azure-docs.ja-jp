<properties
	pageTitle="Azure モバイル アプリ でのオフライン データ同期 | Microsoft Azure"
	description="Azure モバイル アプリのオフライン データ同期機能の概念リファレンスと概要"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="wesmc"/>

# Azure モバイル アプリでのオフライン データ同期

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## オフライン データ同期について

オフライン データ同期は、ネットワーク接続なしで機能するアプリを開発者が作成しやすくする、Azure モバイル アプリのクライアントおよびサーバーの SDK 機能です。

アプリがオフライン モードの場合でもユーザーはデータを作成および変更できますが、それらのデータはローカル ストアに保存されます。アプリは、オンラインに復帰するとローカルの変更内容を Azure モバイル アプリ バックエンドと同期します。この機能には、クライアントとバックエンドの両方で同じレコードが変更された場合の競合検出のサポートも含まれています。検出された競合は、サーバーまたはクライアントのどちらでも処理することができます。

オフライン同期にはさまざまな利点があります。

* サーバー データをデバイスにローカルでキャッシュすることにより、アプリケーションの応答性を向上させる。
* ネットワークの問題がある場合でも使用可能な信頼性の高いアプリを作成する。
* エンド ユーザーがネットワークにアクセスできなくてもデータを作成および変更できるようにすることで、接続がほとんどまたはまったく得られないような状況をサポートする。
* 複数のデバイス間でデータを同期させ、同じレコードが 2 つのデバイスによって変更されたときに競合を検出する。
* 待ち時間の長いネットワークや従量制ネットワークの使用を制限する。

以下のチュートリアルで、Azure モバイル アプリ を使用してモバイル アプリにオフライン同期を追加する方法について説明しています。

* [iOS: オフライン同期を有効にする]			
* [Xamarin iOS: オフライン同期を有効にする]	
* [Xamarin Android: オフライン同期を有効にする]
* [Windows 8.1: オフライン同期を有効にする]	

## 同期テーブルについて

Azure Mobile クライアント SDK では、"/tables" エンドポイントにアクセスするために `IMobileServiceTable` (.NET クライアント SDK) や `MSTable` (iOS クライアント) などのインターフェイスを提供しています。これらの API は Azure モバイル アプリ バックエンドに直接接続しているため、クライアント デバイスのネットワーク接続がなくなると機能しなくなります。

オフラインでの使用をサポートするには、代わりに `IMobileServiceSyncTable` (.NET クライアント SDK) や `MSSyncTable` (iOS クライアント) などの*同期テーブル* API をアプリで使用する必要があります。通常の CRUD 操作 (作成、読み取り、更新、削除) はすべて同期テーブル API に対して行われますが、読み取りまたは書き込みを*ローカル ストア*に対して行うようになる点だけが異なります。同期テーブル操作を実行する前に、ローカル ストアを初期化する必要があります。

## ローカル ストアについて

ローカル ストアは、クライアント デバイス上のデータ永続化レイヤーです。Azure モバイル アプリ クライアントの SDK では、既定のローカル ストアの実装を提供します。ローカル ストアのベースは、Windows、Xamarin、および Android では SQLite ですが、iOS では Core Data です。

Windows Phone または Windows Store 8.1 で SQLite ベースの実装を使用するには、SQLite の拡張機能をインストールする必要があります。詳細については、「[Windows 8.1: オフライン同期を有効にする]」を参照してください。Android と iOS では、デバイスのオペレーティング システム自体にあるバージョンの SQLite が同梱されているので、独自のバージョンの SQLite を参照する必要はありません。

開発者は、独自のローカル ストアを実装することもできます。たとえば、データを暗号化された形式でモバイル クライアント上に保存する必要がある場合は、SQLCipher を使用して暗号化を行うローカル ストアを定義できます。

## 同期コンテキストについて

*同期コンテキスト*はモバイル クライアント オブジェクト (`IMobileServiceClient` や `MSClient` など) に関連付けられており、同期テーブルに対して行われた変更を追跡します。同期コンテキストにより、後でサーバーに送信される CUD 操作 (作成、更新、削除) の順序付きリストを保持する*操作のキュー*が維持されます。

同期コンテキストへのローカル ストアの関連付けは、初期化メソッド (.NET クライアント SDK の `IMobileServicesSyncContext.InitializeAsync(localstore)` など) を使用して行います。

<!-- TODO: link to client references -->


<!-- 
Client code will interact with the table using the `IMobileServiceSyncTable` interface to support offline buffering. This interface supports all the methods of `IMobileServiceTable` along with additional support for pulling data from a Mobile App backend table and merging it into a local store table. How the local table is synchronized with the backend database is mainly controlled by your logic in the client app.

The sync table uses the [System Properties](https://msdn.microsoft.com/library/azure/dn518225.aspx) on the table to implement change tracking for offline synchronization. 



* The data objects on the client should have some system properties, most are not required.
	* Managed
		* Write out the attributes
	* iOS
		*table for the entity
* Note: because the iOS local store is based on Core Data, the developer must define the following tables:
	* System tables  -->


## オフライン同期のしくみ

同期テーブルを使用する場合、クライアント コードによって、ローカルの変更内容が Azure モバイル アプリ バックエンドと同期される時期が制御されます。ローカルの変更を*プッシュする*呼び出しが行われるまで、バックエンドには何も送信されません。同様に、ローカル ストアに新しいデータが入力されるのは、データを*プルする*呼び出しが行われる場合のみです。

* **プッシュ**: プッシュは同期コンテキストに対する操作であり、最後のプッシュ以降の CUD に関するすべての変更を送信します。個々のテーブルの変更だけを送信すると操作の順番が間違って送信される可能性があるため、このような送信を行うことができないことに注意してください。プッシュは Azure モバイル アプリ バックエンドに対して一連の REST 呼び出しを実行し、呼び出しを受けたバックエンドがサーバー データベースを変更します。

* **プル**: プルはテーブルごとに実行され、クエリを使用してサーバー データのサブセットのみを取得するようにカスタマイズできます。その後、Azure Mobile クライアント SDK が結果のデータをローカル ストアに挿入します。

* **暗黙的なプッシュ**: 保留中のローカルの更新があるテーブルに対してプルが実行された場合、プルはまず同期コンテキストに対してプッシュを実行します。これにより、キュー済みの変更とサーバーの新規データとの競合が最小限に抑えられます。

* **増分同期**: プル操作の最初のパラメーターは*クエリ名*であり、これはクライアントでのみ使用されます。null 以外のクエリ名を使用する場合、Azure Mobile SDK は*増分同期*を実行します。プル操作で結果のセットが返されるたびに、その結果セットから最新の `__updatedAt` タイムスタンプが SDK ローカル システム テーブルに格納されます。それ以降のプル操作では、そのタイムスタンプより後のレコードだけが取得されます。

  増分同期を使用するには、サーバーが意味のある `__updatedAt` 値を返すとともに、このフィールドでの並べ替えをサポートしている必要があります。ただし、SDK はupdatedAt フィールドに独自の並べ替えを追加するため、固有の `$orderBy$` 句を含むプル クエリは使用できません。

  クエリ名は任意の文字列にすることができますが、アプリ内の論理クエリごとに一意である必要があります。一意でない場合、異なるプル操作によって同じ増分同期のタイムスタンプが上書きされて、クエリが誤った結果を返す可能性があります。

  クエリでパラメーターを使用する場合、一意のクエリ名を作成する 1 つの方法はパラメーター値を組み込むことです。たとえば、userid でフィルター処理をする場合、クエリ名は次のようにすることができます。

		await todoTable.PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

  増分同期を無効にする場合は、`null` をクエリ ID として渡します。この場合、`PullAsync` への呼び出しごとにすべてのレコードが再取得されるため、場合によっては非効率となります。

 

<!--   mymobileservice-code.azurewebsites.net/tables/TodoItem?$filter=(__updatedAt ge datetimeoffset'1970-01-01T00:00:00.0000000%2B00:00')&$orderby=__updatedAt&$skip=0&$top=50&__includeDeleted=true&__systemproperties=__updatedAt%2C__deleted
 -->
* **消去**: ローカル ストアのコンテンツは `IMobileServiceSyncTable.PurgeAsync` を使用して削除できます。消去は、クライアント データベースに古くなったデータがある場合、または保留中の変更をすべて破棄する場合に行う必要があります。

  消去により、ローカル ストアからテーブルがクリアされます。サーバー データベースとの同期待ちの操作がある場合、*force purge* パラメーターを設定しない限り消去では例外がスローされます。

  クライアント上の古くなったデータの例として、"todo list" サンプルでデバイス 1 が未完了のアイテムだけを取得するとします。次に、別のデバイスによって、サーバー上の TodoItem "Buy milk" が完了とマークされたとします。ただし、デバイス 1 は完了とマークされていないアイテムだけをプルするので、デバイス 1 はローカル ストアに "Buy milk" TodoItem を残しています。消去によってこの古くなったアイテムがクリアされます。
 
## 次のステップ

* [iOS: オフライン同期を有効にする]			
* [Xamarin iOS: オフライン同期を有効にする]	
* [Xamarin Android: オフライン同期を有効にする]
* [Windows 8.1: オフライン同期を有効にする]	

<!-- Links -->

[iOS: オフライン同期を有効にする]: ../app-service-mobile-ios-get-started-offline-data-preview.md
[Xamarin iOS: オフライン同期を有効にする]: ../app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Xamarin Android: オフライン同期を有効にする]: ../app-service-mobile-xamarin-ios-get-started-offline-data-preview.md
[Windows 8.1: オフライン同期を有効にする]: ../app-service-mobile-windows-store-dotnet-get-started-offline-data-preview.md

<!---HONumber=August15_HO8-->