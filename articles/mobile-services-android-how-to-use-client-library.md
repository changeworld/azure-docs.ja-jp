<properties pageTitle="Mobile Services 向け Android クライアント ライブラリの操作" description="Azure Mobile Services 向け Android クライアントを使用する方法について説明します。" services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/20/2014" ms.author="ricksal"/>

# モバイル サービス向け Android クライアント ライブラリの使用方法

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/ja-jp/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android" class="current">Android</a><a href="/ja-jp/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


このガイドでは、Azure Mobile Services 向け Android クライアントを使用して一般的なシナリオを実行する方法について説明します。紹介するシナリオは、データの照会、挿入、更新、および削除、ユーザーの認証、エラー処理、クライアントのカスタマイズなどです。Mobile Services を初めて使用する場合は、まず [Mobile Services のクイック スタート][Get started with Mobile Services]を完了することを検討してください。このクイック スタート チュートリアルでは、アカウントを構成し、初めてのモバイル サービスを作成します。

サンプルは Java で記述され、[モバイル サービス SDK] を必要とします。このチュートリアルを行うには、Eclipse 統合開発環境 (IDE) と Android Developer Tools (ADT) プラグインを含む [Android SDK](https://go.microsoft.com/fwLink/p/?LinkID=280125&clcid=0x409) も必要です。モバイル サービス SDK では Android Version 2.2 以降がサポートされますが、Android Version 4.2 以降を対象に作成することをお勧めします。



## 目次

- [Mobile Services とは]
- [概念]
- [セットアップと前提条件]
- [方法:モバイル サービス クライアントを作成する]
- [方法:テーブル参照を作成する]
	- [API の構造]
- [方法:モバイル サービスのデータを照会する]
	- [返されるデータをフィルター処理する]
    - [返されるデータを並べ替える]
	- [ページにデータを返す]
	- [特定の列を選択する]
	- [方法:クエリ メソッドを連結する]
- [方法:モバイル サービスにデータを挿入する]
- [方法:モバイル サービスのデータを更新する]
- [方法:モバイル サービスのデータを削除する]
- [方法:特定の項目を検索する]
- [方法:型指定のないデータを処理する]
- [方法:データをユーザー インターフェイスにバインドする]
	- [方法:レイアウトを定義する]
	- [方法:アダプターを定義する]
	- [方法:アダプターを使用する]
- [方法:ユーザーを認証する]
	- [認証トークンをキャッシュする]
- [方法:エラーを処理する]
- [方法:クライアントをカスタマイズする]
	- [要求ヘッダーをカスタマイズする]
	- [シリアル化をカスタマイズする]
- [次のステップ][]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]


<h2><a name="setup"></a>セットアップと前提条件</h2>

前提条件として、モバイル サービスとテーブルを作成してあるとします。詳細については、「[テーブルの作成](http://go.microsoft.com/fwlink/p/?LinkId=298592)」を参照してください。このトピックで使用するコードでは、次の列を含むテーブル  *ToDoItem* を想定しています。

<ul>
<li>id</li>
<li>text</li>
<li>complete</li>
<li>due</li>
<li>duration</li>
</ul>

これに対応する型指定されたクライアント側オブジェクトを次に示します。

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
		private Date due
		private Integer duration;
	}
	
動的スキーマが有効な場合、挿入または更新の要求に含まれるオブジェクトに基づいて、Azure Mobile Services によって自動的に新しい列が生成されます。詳細については、[動的スキーマ]( http://go.microsoft.com/fwlink/p/?LinkId=296271).」を参照してください。

<h2><a name="create-client"></a>方法:モバイル サービス クライアントを作成する</h2>

次のコードは、モバイル サービスにアクセスするために使用される [MobileServiceClient](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) オブジェクトを生成します。 

			MobileServiceClient mClient = new MobileServiceClient(
					"MobileServiceUrl", // Replace with the above Site URL
					"AppKey", 			// replace with the Application Key 
					this)

前のコードの  `MobileServiceUrl` と  `AppKey` を、モバイル サービスの URL とアプリケーション キーで順に置き換えます。どちらも Azure の管理ポータルで確認できます。モバイル サービスを選択し、[ *Dashboard*] をクリックしてください。

<h2><a name="instantiating"></a>方法:テーブル参照を作成する</h2>

Java は厳密に型指定された言語であるため、モバイル サービスのデータを照会または変更する最も簡単な方法は *typed programming model*を使用する方法です (後で *untyped*モデルについて説明します)。このモデルは、クライアントとモバイル サービスの間でデータを送信するときに <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用して JSON にシームレスなシリアル化と非シリアル化を提供します。開発者は何も作業する必要がありません。フレームワークによってすべてが処理されます。

データを照会または変更するためには、最初に [**MobileServiceClient**](http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) の **getTable** メソッドを呼び出して [MobileServiceTable](http://go.microsoft.com/fwlink/p/?LinkId=296835) オブジェクトを作成します。ここでは、このメソッドの 2 つのオーバーロードに注目します。

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

次のコードで、 *mClient* は、モバイル サービス クライアントへの参照です。

[1 つ目のオーバーロード](http://go.microsoft.com/fwlink/p/?LinkId=296839)は、クラス名とテーブル名が同じ場合に使用されています。

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[2 つ目のオーバーロード](http://go.microsoft.com/fwlink/p/?LinkId=296840)は、テーブル名と型名が異なる場合に使用されています。

		MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

 


### <a name="api"></a>API の構造

モバイル サービスのテーブル操作では、非同期コールバック モデルを使用します。クエリや挿入、更新、削除などの操作に関連するすべてのメソッドは、コールバック オブジェクトであるパラメーターを持ちます。このオブジェクトには、常に **OnCompleted** メソッドが含まれます。**onCompleted** メソッドには、**Exception** オブジェクトである 1 つのパラメーターが含まれます。これを使って、メソッド呼び出しの成功を確認するテストを実行できます。null の **Exception** オブジェクトは成功を示します。それ以外の **Exception** オブジェクトは、エラーの原因を示します。

コールバック オブジェクトには複数の種類があり、どのオブジェクトを使用するかは実行する操作 (データの照会、変更、または削除) によって異なります。 *onCompleted* メソッドのパラメーターは、属しているコールバック オブジェクトによって異なります。


<h2><a name="querying"></a>方法:モバイル サービスのデータを照会する</h2>

このセクションでは、モバイル サービスにクエリを発行する方法について説明します。サブセクションでは、並べ替え、フィルター処理、ページングなど、さまざまな処理について説明します。最後に、これらの操作を連結する方法について説明します。

次のコードは、 *ToDoItem* テーブル内のすべての項目を返します。 

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
				public void onCompleted(List<ToDoItem> result, int count,
					Exception exception, ServiceFilterResponse response) {
					if (exception == null) {
						for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
						}
					}
				}
			});

このようなクエリでは、[**TableQueryCallback&lt;E&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296849) コールバック オブジェクトを使用します。

 *result* パラメーターは、クエリの結果セットを返します。 *exception* テストの成功ブランチ内のコードは、個々の行を解析する方法を示します。


### <a name="filtering"></a>方法:返されるデータをフィルター処理する

次のコードは、 *ToDoItem* テーブルから、 *complete* フィールドが *false* に等しいすべての項目を返します。 *mToDoTable* は、以前に作成したモバイル サービス テーブルへの参照です。 

		mToDoTable.where().field("complete").eq(false)
				  .execute(new TableQueryCallback<ToDoItem>() {
						public void onCompleted(List<ToDoItem> result, 
												int count, 
												Exception exception,
												ServiceFilterResponse response) {
				if (exception == null) {
					for (ToDoItem item : result) {
                		Log.i(TAG, "Read object with ID " + item.id);  
					}
				} 
			}
		});

フィルターは、テーブル参照での [**where**](http://go.microsoft.com/fwlink/p/?LinkId=296867) メソッドの呼び出しで開始します。その次に [**field**](http://go.microsoft.com/fwlink/p/?LinkId=296869) メソッド呼び出し、その次に論理述語を指定するメソッド呼び出しを続けます。使用できる述語メソッドには、[**eq**](http://go.microsoft.com/fwlink/p/?LinkId=298461), [**ne**](http://go.microsoft.com/fwlink/p/?LinkId=298462), [**gt**](http://go.microsoft.com/fwlink/p/?LinkId=298463), [**ge**](http://go.microsoft.com/fwlink/p/?LinkId=298464), [**lt**](http://go.microsoft.com/fwlink/p/?LinkId=298465), [**le**](http://go.microsoft.com/fwlink/p/?LinkId=298466)などがあります。

数値フィールドと文字列フィールドを特定の値と比較するにはこれだけで十分ですが、それ以外のさまざまな操作を行うことができます。

たとえば、日付をフィルター処理できます。日付フィールド全体を比較できるほか、[**year**](http://go.microsoft.com/fwlink/p/?LinkId=298467), [**month**](http://go.microsoft.com/fwlink/p/?LinkId=298468), [**day**](http://go.microsoft.com/fwlink/p/?LinkId=298469), [**hour**](http://go.microsoft.com/fwlink/p/?LinkId=298470), [**minute**](http://go.microsoft.com/fwlink/p/?LinkId=298471) and [**second**](http://go.microsoft.com/fwlink/p/?LinkId=298472) などのメソッドを使用して日付の一部を比較することもできます。次の部分的なコードでは、 *due date* が 2013 に等しい項目のフィルターを追加しています。

		mToDoTable.where().year("due").eq(2013)

[**startsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298473), [**endsWith**](http://go.microsoft.com/fwlink/p/?LinkId=298474), [**concat**](http://go.microsoft.com/fwlink/p/?LinkId=298475), [**subString**](http://go.microsoft.com/fwlink/p/?LinkId=298477), [**indexOf**](http://go.microsoft.com/fwlink/p/?LinkId=298488), [**replace**](http://go.microsoft.com/fwlink/p/?LinkId=298491), [**toLower**](http://go.microsoft.com/fwlink/p/?LinkId=298492), [**toUpper**](http://go.microsoft.com/fwlink/p/?LinkId=298493), [**trim**](http://go.microsoft.com/fwlink/p/?LinkId=298495), and [**length**](http://go.microsoft.com/fwlink/p/?LinkId=298496) などのメソッドを使用すると、文字列フィールドに対してさまざまな複雑なフィルターを適用できます。次の部分的なコードでは、 *text* 列が "PRI0" で始まるテーブル行をフィルター処理します。

		mToDoTable.where().startsWith("text", "PRI0")

数値フィールドに対しては、[**add**](http://go.microsoft.com/fwlink/p/?LinkId=298497)、[**sub**](http://go.microsoft.com/fwlink/p/?LinkId=298499), [**mul**](http://go.microsoft.com/fwlink/p/?LinkId=298500), [**div**](http://go.microsoft.com/fwlink/p/?LinkId=298502), [**mod**](http://go.microsoft.com/fwlink/p/?LinkId=298503), [**floor**](http://go.microsoft.com/fwlink/p/?LinkId=298505), [**ceiling**](http://go.microsoft.com/fwlink/p/?LinkId=298506), and [**round**](http://go.microsoft.com/fwlink/p/?LinkId=298507) などのメソッドを使用してさまざまな複雑なフィルターを適用できます。次の部分的なコードでは、 *duration* が偶数のテーブル行をフィルター処理します。

		mToDoTable.where().field("duration").mod(2).eq(0)


[**and**](http://go.microsoft.com/fwlink/p/?LinkId=298512), [**or**](http://go.microsoft.com/fwlink/p/?LinkId=298514) and [**not**](http://go.microsoft.com/fwlink/p/?LinkId=298515) のようなメソッドを使用すると、述語を結合できます。次の部分的なコードでは、前の 2 つの例を結合しています。

		mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")

次の部分的なコードに示すように、論理演算子をグループ化したり入れ子にしたりすることができます。

		mToDoTable.where()
					.year("due").eq(2013)
						.and
					(startsWith("text", "PRI0").or().field("duration").gt(10))

フィルター処理の詳細と例については、[Mobile Services の Android クライアント クエリ モデル](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)の機能に関する記事を参照してください。

### <a name="sorting"></a>方法:返されるデータを並べ替える

次のコードは、 *ToDoItems* テーブルから、 *text* フィールドの値に基づいて昇順に並べ替えられたすべての項目を返します。 *mToDoTable* は、以前に作成したモバイル サービス テーブルへの参照です。

		mToDoTable.orderBy("text", QueryOrder.Ascending)
			.execute(new TableQueryCallback<ToDoItem>() { 
				/* same implementation as above */ 
			}); 

[**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=298519) メソッドの 1 つ目のパラメーターは、並べ替えに使用するフィールドの名前に等しい文字列です。

2 番目のパラメーターでは、[**QueryOrder**](http://go.microsoft.com/fwlink/p/?LinkId=298521) 列挙を使用して、昇順または降順のどちらで並べ替えを行うかを指定します。

***where*** メソッドを使用してフィルター処理を行う場合、***where*** メソッドは ***orderBy*** メソッドの前に呼び出す必要があります。

### <a name="paging"></a>方法:ページにデータを返す

最初の例は、テーブルから最初の 5 つの項目を選択する方法を示しています。このクエリは、 *ToDoItems* テーブルから項目を返します。 *mToDoTable* は、以前に作成したモバイル サービス テーブルへの参照です。

		mToDoTable.top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            public void onCompleted(List<ToDoItem> result, 
										int count,
	                    				Exception exception, 
										ServiceFilterResponse response) {
	                if (exception == null) {
	                    for (ToDoItem item : result) {
                			Log.i(TAG, "Read object with ID " + item.id);  
	                    }
	                } 
	            }
	        });

次に、最初の 5 つの項目をスキップしてその次の 5 つの項目を返すクエリを定義します。

		mToDoTable.skip(5).top(5)
	            .execute(new TableQueryCallback<ToDoItem>() {	
	            // implement onCompleted logic here
	        });


### <a name="selecting"></a>方法:特定の列を選択する

次のコードは、 *ToDoItems* テーブルからすべての項目を返したうえで *complete* フィールドと *text* フィールドのみを表示する方法を示しています。 *mToDoTable* は、以前に作成したモバイル サービス テーブルへの参照です。

		mToDoTable.select("complete", "text")
	            .execute(new TableQueryCallback<ToDoItem>() { 
					/* same implementation as above */ 
			}); 

	
select 関数のパラメーターは、取得するテーブルの列の文字列名です。

[**select**](http://go.microsoft.com/fwlink/p/?LinkId=290689) メソッドは、[**where**](http://go.microsoft.com/fwlink/p/?LinkId=296296) や [**orderBy**](http://go.microsoft.com/fwlink/p/?LinkId=296313) のようなメソッドがある場合はその後に記述する必要があります。このメソッドの後に [**top**](http://go.microsoft.com/fwlink/p/?LinkId=298731) のようなメソッドを記述することができます。

### <a name="chaining"></a>方法:クエリ メソッドを連結する 

モバイル サービス テーブルの照会に使用されているメソッドは連結できます。これにより、並べ替えとページングが行われたフィルター処理された行の特定の列を選択するなど、さまざまな操作を行うことができます。複雑な論理フィルターを作成できます。

そのしくみは、使用しているクエリ メソッドによって [**MobileServiceQuery&lt;T&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=298551) オブジェクトを返し、次にこのオブジェクトで追加のメソッドを呼び出します。一連のメソッドを終了し、クエリを実際に実行するには、[**execute**](http://go.microsoft.com/fwlink/p/?LinkId=298554) メソッドを呼び出します。

次にコード サンプルを示します。ここで、 *mToDoTable* は、モバイル サービスの *ToDoItem* テーブルへの参照です。

		mToDoTable.where().year("due").eq(2013)
						.and().startsWith("text", "PRI0")
						.or().field("duration").gt(10)
					.select("id", "complete", "text", "duration")
					.orderBy(duration, QueryOrder.Ascending).top(20)				
					.execute(new TableQueryCallback<ToDoItem>() { 
						/* code to execute */ 
				});

メソッドを連結する場合の主な要件として、 *where* メソッドと述語を最初に記述する必要があります。それ以降、アプリケーションのニーズに最も合う順番で後続のメソッドを呼び出すことができます。


<h2><a name="inserting"></a>方法:モバイル サービスにデータを挿入する</h2>

次のコードは、テーブルに新しい行を挿入する方法を示しています。

最初に、 *ToDoItem* クラスのインスタンスをインスタンス化し、そのプロパティを設定します。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 
		
 次に、[**insert**](http://go.microsoft.com/fwlink/p/?LinkId=296862) メソッドを呼び出します。

		mToDoTable.insert(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
								Exception exception, 
								ServiceFilterResponse response) {	
				if (exception == null) {
                		Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

**insert** 操作の場合、コールバック オブジェクトは、[**TableOperationCallback&lt;ToDoItem&gt;**](http://go.microsoft.com/fwlink/p/?LinkId=296865) です。

**onCompleted** メソッドの entity パラメーターは、新しく挿入されたオブジェクトを格納します。この正常に動作するコードは、挿入された行の  *id* にアクセスする方法を示しています。

Mobile Services では、テーブル ID として一意のカスタム文字列値をサポートしています。このため、アプリケーションは、Mobile Services テーブルの ID 列に電子メール アドレスやユーザー名などのカスタム値を使用できます。たとえば、各レコードを電子メール アドレスで識別する場合は、次の JSON オブジェクトを使用できます。

		ToDoItem mToDoItem = new ToDoItem();
		mToDoItem.id = "myemail@mydomain.com";
		mToDoItem.text = "Test Program";
		mToDoItem.complete = false;
		mToDoItem.duration = 5; 

新しいレコードをテーブルに挿入するときに文字列 ID 値が指定されない場合は、モバイル サービスによって ID 用の一意の値が生成されます。

文字列 ID のサポートは、開発者にとって次のような利点があります。

+ データベースと往復することなく ID が生成されます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値をより適切にアプリケーションのロジックに統合できます。

サーバー スクリプトを使用して ID 値を設定することもできます。次のスクリプト例は、カスタム GUID を生成し、新しいレコードの ID に割り当てます。これは、レコードの ID として値を渡さなかった場合に Mobile Services によって生成される ID 値に似ています。

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


アプリケーションが ID の値を指定すると、モバイル サービスはそれをそのまま格納します。これには、前後の空白文字も含まれます。値から空白文字が除去されることはありません。

 `id` の値は一意である必要があり、次のセット内の文字を含まないようにする必要があります。

+ 制御文字:[0x0000-0x001F] および [0x007F-0x009F]。詳細については、[ASCII 制御コード C0 および C1 に関するページ]を参照してください。
+  印刷可能文字:**"**(0x0022)、**\+** (0x002B)、**/** (0x002F)、**?**(0x003F)、**\\** (0x005C)、**`** (0x0060)
+  ID "." および ".."

また、テーブルに整数 ID を使用することもできます。整数 ID を使用するには、 `mobile table create` コマンドで `--integerId` オプションを使用してテーブルを作成する必要があります。このコマンドは、Azure のコマンド ライン インターフェイス (CLI) で使用されます。CLI の使い方の詳細については、「[モバイル サービス テーブルの管理用コマンド]」を参照してください。


<h2><a name="updating"></a>方法:モバイル サービスのデータを更新する</h2>

次のコードは、テーブルのデータを更新する方法を示しています。この例の *mToDoItem* は、 *ToDoItem* テーブルの項目への参照です。ここでは、その *duration* プロパティを更新します。

		mToDoItem.duration = 5;
		mToDoTable.update(mToDoItem, new TableOperationCallback<ToDoItem>() {
			public void onCompleted(ToDoItem entity, 
									Exception exception, 
									ServiceFilterResponse response) {
				if (exception == null) {
            			Log.i(TAG, "Read object with ID " + entity.id);  
				} 
			}
		});

コールバック オブジェクトと *onCompleted* メソッドのパラメーターは挿入操作の場合と同じであることに注意してください。

<h2><a name="deleting"></a>方法:モバイル サービスのデータを削除する</h2>

次のコードは、テーブルのデータを削除する方法を示しています。このコードでは、項目 (このケースでは *mToDoItem*) への参照を使用して ToDoItem テーブルから既存の項目を削除します。

		mToDoTable.delete(mToDoItem, new TableDeleteCallback() {
		    public void onCompleted(Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

 *delete* のケースのコールバック オブジェクトは [**TableDeleteCallback**](http://go.microsoft.com/fwlink/p/?LinkId=296858) です。**onCompleted** メソッドの動作は、テーブル行が返されない点が異なります。

次のコードは、別の方法でこの操作を行う方法を示しています。このコードでは、削除する行の id フィールドの値 ("37BBF396-11F0-4B39-85C8-B319C729AF6D" に等しいと想定) を指定して、ToDoItem テーブル内の既存の項目を削除します。 

		mToDoTable.delete("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

<h2><a name="lookup"></a>方法:特定の項目を検索する</h2>
一般的にクエリを使って特定の条件を満たした項目のコレクションを取得する代わりに、特定の項目を  *id* で検索する必要がある場合があります。その方法を次のコードに示します (ここでは  *id* として "37BBF396-11F0-4B39-85C8-B319C729AF6D" を想定しています)。

		mToDoTable.lookUp("37BBF396-11F0-4B39-85C8-B319C729AF6D", new TableOperationCallback<ToDoItem>() {
		    public void onCompleted(item entity, Exception exception,
		            ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Read object with ID " + entity.id);    
		        }
		    }
		});


<h2><a name="untyped"></a>方法:型指定のないデータを処理する</h2>

型指定のないプログラミング モデルでは、JSON シリアル化を的確に制御できます。このモデルを使用するシナリオとしては、モバイル サービス テーブルに大量の列が含まれ、参照する必要があるのはそのいくつかのみである場合などが想定されます。型指定されたモデルを使用するには、モバイル サービス テーブルのすべての列をデータ クラスに定義する必要があります。一方、型指定のないモデルでは、使用する列を定義するだけで済みます。

型指定されたモデルと同様、テーブル参照を取得することから始めますが、このケースでのオブジェクトは [MobileServicesJsonTable](http://go.microsoft.com/fwlink/p/?LinkId=298733) です。Mobile Services クライアントのインスタンスで [getTable()](http://go.microsoft.com/fwlink/p/?LinkId=298734) メソッドを呼び出して、参照を取得します。


このメソッドの次のオーバーロードを使用します。これは、型指定のない JSON ベースのプログラミング モデルを操作するために使用されます。

		public class MobileServiceClient {
		    public MobileServiceJsonTable getTable(String name);
		}

データにアクセスするための API 呼び出しのほとんどは、型指定されたプログラミングでの呼び出しに似ています。主な違いとして、型指定のないモデルでは、**MobileServiceTable** オブジェクトの代わりに **MobileServiceJsonTable** オブジェクトのメソッドを呼び出します。コールバック オブジェクトと **onCompleted** メソッドの使用法は、型指定されたモデルによく似ています。


### <a name="json_instance"></a>方法:型指定のないテーブルのインスタンスを作成する

Mobile Services クライアントのインスタンス (ここでは、 *mClient* 変数) を作成した後は、次のコードを使用して **MobileServiceJsonTable** のインスタンスを作成します。

		MobileServiceJsonTable mTable = mClient.getTable("ToDoItem");

**MobileServiceJsonTable** のインスタンスを作成すると、型指定されたプログラミング モデルで呼び出すことができるほぼすべてのメソッドをそのインスタンスで呼び出すことができます。ただし、次の例に示すように、メソッドが型指定のないパラメーターを受け取る場合もあります。

### <a name="json_insert"></a>方法:型指定のないテーブルに挿入する

次のコードは、挿入操作を行う方法を示しています。最初に、[**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) を作成します。これは、<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリの一部です。

		JsonObject task = new JsonObject();
		task.addProperty("text", "Wake up");
		task.addProperty("complete", false);
		task.addProperty("duration", 5);

次に、このオブジェクトを挿入します。[**insert**](http://go.microsoft.com/fwlink/p/?LinkId=298535) メソッドに渡されるコールバック関数は、[**TableJsonOperationCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298532) クラスのインスタンスです。 *onCompleted* メソッドの 1 つ目のパラメーターが JsonObject である点に注目してください。
		 
		mTable.insert(task, new TableJsonOperationCallback() {
		    public void onCompleted(JsonObject jsonObject, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object inserted with ID " + 
		        jsonObject.getAsJsonPrimitive("id").getAsString());
		        }
		    }
		});


このメソッド呼び出しで挿入されたオブジェクトの ID を取得する方法を示します。

		        jsonObject.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>方法:型指定のないテーブルから削除する

次のコードでは、インスタンス (このケースでは前の *insert* の例で作成したのと同じ **JsonObject** のインスタンス) を削除する方法を示します。コールバック オブジェクト **TableDeleteCallback** は型指定されたプログラミング モデルで使用したのと同じオブジェクトですが、その **onCompleted** メソッドの署名は **insert** の例に使用された署名とは異なります。


		mTable.delete(task, new TableDeleteCallback() {
		    public void onCompleted(Exception exception, 
									ServiceFilterResponse response) {
		        if(exception == null){
		            Log.i(TAG, "Object deleted");
		        }
		    }
		});

ID を使用してインスタンスを直接削除することもできます。 
		
		mTable.delete(task.getAsJsonPrimitive("id").getAsString(), ...)


### <a name="json_get"></a>方法:型指定のないテーブルからすべての行を返す

次のコードは、テーブル全体を取得する方法を示しています。型指定のないプログラミング モデルでは、異なるコールバック オブジェクト[**TableJsonQueryCallback**](http://go.microsoft.com/fwlink/p/?LinkId=298543)を使用します。

		mTable.execute(new TableJsonQueryCallback() {
		    public void onCompleted(JsonElement result, 
									int count, 
									Exception exception,
									ServiceFilterResponse response) {
		        if(exception == null){
		            JsonArray results = result.getAsJsonArray();
		            for(JsonElement item : results){
		                Log.i(TAG, "Read object with ID " + 
		            item.getAsJsonObject().getAsJsonPrimitive("id").getAsInt());
		            }
		        }
		    }
		});

型指定されたプログラミング モデルで使用されたのと同じ名前を持つメソッドを連結して、フィルター処理、並べ替え、およびページングを行うことができます。


<h2><a name="binding"></a>方法:データをユーザー インターフェイスにバインドする</h2>

データ バインドには、次の 3 つのコンポーネントが関係します。

- データ ソース
- 画面レイアウト
- これらの 2 つを連結するアダプター

次のサンプル コードでは、モバイル サービス テーブル *ToDoItem* のデータを配列に返します。これは、データ アプリケーションで一般的なパターンの 1 つです。一般に、データベース クエリでは、行のコレクションが返されます。クライアントは、これをリストまたは配列に取得します。この例では、配列はデータ ソースです。 

コードで、デバイスに表示されるデータのビューを定義する画面レイアウトを指定します。 

これらの 2 つをアダプターによってバインドします。アダプターは、このコードでは *ArrayAdapter&lt;ToDoItem&gt;* クラスの拡張です。

### <a name="layout"></a>方法:レイアウトを定義する
 
レイアウトは、XML コードの複数のスニペットで定義されます。既存のレイアウトがあり、次のコードがサーバーのデータを設定する **ListView** を表すと想定します。

	    <ListView
	        android:id="@+id/listViewToDo"
	        android:layout_width="match_parent"
	        android:layout_height="wrap_content"
	        tools:listitem="@layout/row_list_to_do" >
	    </ListView>
	

前のコードで、 *listitem* 属性は、リスト内の個々の行のレイアウトの ID を指定します。次にそのコードを示します。チェック ボックスとそれに関連付けられたテキストを指定しています。これは、リスト内のそれぞれの項目に対して 1 回インスタンス化されます。より複雑なレイアウトでは、表示する追加フィールドを指定することもできます。このコードは、 *row_list_to_do.xml* ファイルに含まれています。

		<?xml version="1.0" encoding="utf-8"?>
		<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
		    android:layout_width="match_parent"
		    android:layout_height="match_parent"
		    android:orientation="horizontal">		    
		    <CheckBox
		        android:id="@+id/checkToDoItem"
		        android:layout_width="wrap_content"
		        android:layout_height="wrap_content"
		        android:text="@string/checkbox_text" />
		</LinearLayout>
		

### <a name="adapter"></a>方法:アダプターを定義する
	
このビューのデータ ソースは *ToDoItem* の配列であるため、 *ArrayAdapter&lt;ToDoItem&gt;* クラスからアダプターをサブクラス化します。このサブクラスでは、 *row_list_to_do* レイアウトを使用してすべての *ToDoItem* のビューを生成します。

コードでは、 *ArrayAdapter&lt;E&gt;* クラスの拡張である次のクラスを定義します。

		public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {



アダプターの *getView* メソッドはオーバーライドする必要があります。その方法の 1 つを次のサンプル コードに示します。詳細はアプリケーションによって異なります。

	public View getView(int position, View convertView, ViewGroup parent) {
		View row = convertView;

		final ToDoItem currentItem = getItem(position);

		if (row == null) {
			LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
			row = inflater.inflate(R.layout.row_list_to_do, parent, false);
		}

		row.setTag(currentItem);

		final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
		checkBox.setText(currentItem.getText());
		checkBox.setChecked(false);
		checkBox.setEnabled(true);

		return row;
	}

次に示すように、このクラスのインスタンスを Activity 内で作成します。

		ToDoItemAdapter mAdapter;
		mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter コンストラクターの 2 つ目のパラメーターはレイアウトへの参照であることに注意してください。コンストラクターの呼び出しに続けて、次のコードを記述します。このコードは、最初に **ListView** への参照を取得します。次に、 *setAdapter* を呼び出して、作成したアダプターを使用するようにそれ自体を構成します。

		ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
		listViewToDo.setAdapter(mAdapter);


### <a name="use-adapter"></a>方法:アダプターを使用する

これで、データ バインドを使用する準備が整いました。次のコードでは、モバイル サービス テーブルの項目を取得した後、アダプターをクリアしています。次に、アダプターの *add* メソッドを呼び出して、返された項目をアダプターに設定しています。

		mToDoTable.execute(new TableQueryCallback<ToDoItem>() {
			public void onCompleted(List<ToDoItem> result, int count, Exception exception, ServiceFilterResponse response) {
				if (exception == null) {
					mAdapter.clear();
					for (ToDoItem item : result) {
						mAdapter.add(item);
					}
				} 
			}
		});

 *ToDoItem* テーブルを変更したときにその結果を表示するには、その都度アダプターを呼び出す必要があります。変更はレコード単位で加えられるため、操作の対象はコレクションではなく行になります。項目を挿入する場合は、アダプターの *add* メソッドを呼び出します。項目を削除する場合は、 *remove* メソッドを呼び出します。


<h2><a name="authentication"></a>方法:ユーザーを認証する</h2>

Mobile Services は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、[認証の使用](http://go.microsoft.com/fwlink/p/?LinkId=296316)に関するページを参照してください。

 *server* フローと *client* フローという 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

アプリケーションで認証を有効にするには、次の 3 つの手順を実行する必要があります。

<ol>
<li>認証するアプリケーションをプロバイダーに登録し、モバイル サービスを構成する</li>
<li>テーブルのアクセス許可を、認証されたユーザーのみに制限する</li>
<li>アプリケーションに認証コードを追加する</li>
</ol>

モバイル サービスは、ユーザーを認証するために使用できる次の既存の ID プロバイダーをサポートしています。

- Microsoft アカウント
- Facebook
- Twitter
- Google 
- Azure Active Directory

テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用して要求を変更することもできます。 

これらの最初の 2 つのタスクは、[Azure の管理ポータル](https://manage.windowsazure.com/)を使用して実行します。詳細については、[認証の使用](http://go.microsoft.com/fwlink/p/?LinkId=296316)に関するページを参照してください。

### <a name="caching"></a>方法:アプリケーションに認証コードを追加する

1.  アプリケーションのアクティビティ ファイルに次の import ステートメントを追加します。

		import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
		import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
		import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2. アクティビティ クラスの **onCreate** メソッドで、 `MobileServiceClient` オブジェクトを作成するコードの後に、次のコード行を追加します。 `MobileServiceClient` オブジェクトへの参照は *mClient* であると想定しています。
	
			// Login using the Google provider.
			mClient.login(MobileServiceAuthenticationProvider.Google,
					new UserAuthenticationCallback() {
						@Override
						public void onCompleted(MobileServiceUser user,
								Exception exception, ServiceFilterResponse response) {	
							if (exception == null) {
								/* User now logged in, you can get their identity via user.getUserId() */ 
							} else {
								/* Login error */
							}
						}
					});

    このコードでは、Google ログインを使用してユーザーを認証します。認証されたユーザーの ID を示すダイアログが表示されます。認証が成功しないと、次に進むことはできません。

    > [AZURE.NOTE] Google 以外の ID プロバイダーを使用している場合は、上の **login** メソッドに渡される値を、_MicrosoftAccount__Facebook_、_Twitter_、_WindowsAzureActiveDirectory_. のいずれかにします。
    </div>


3. アプリケーションを実行したときに、選択した ID プロバイダーを使ってサインインします。 


### <a name="caching"></a>方法:認証トークンをキャッシュする

このセクションでは、認証トークンをキャッシュする方法について説明します。これにより、トークンが有効なときにアプリケーションが "休止状態" になった場合にユーザーに認証のやり直しを求めることを回避できます。

認証トークンをキャッシュするには、ユーザー ID と認証トークンをデバイスにローカルで保存する必要があります。アプリケーションが次回起動されたときに、キャッシュを確認し、これらの値が存在する場合はログイン手順をスキップし、クライアントに再度このデータを渡します。ただし、このデータは慎重な扱いを要する情報であり、電話の盗難に備えて安全のために暗号化して保存する必要があります。 

次のコード スニペットでは、Microsoft アカウントのログイン用のトークンを取得する方法を示しています。トークンはキャッシュされ、キャッシュが見つかった場合に再ロードされます。 

	private void authenticate() {
		if (LoadCache())
		{
			createTable();
		}
		else
		{
		    // Login using the provider.
		    mClient.login(MobileServiceAuthenticationProvider.MicrosoftAccount,
		            new UserAuthenticationCallback() {
		                @Override
		                public void onCompleted(MobileServiceUser user,
		                        Exception exception, ServiceFilterResponse response) {
		                    if (exception == null) {
		                        createTable();
		                        cacheUser(mClient.getCurrentUser());
		                    } else {
		                        createAndShowDialog("You must log in. Login Required", "Error");
		                    }
		                }
		            });
		}
	}	


	private boolean LoadCache()
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		String tmp1 = prefs.getString("tmp1", "undefined"); 
		if (tmp1 == "undefined")
			return false;
		String tmp2 = prefs.getString("tmp2", "undefined"); 
		if (tmp2 == "undefined")
			return false;
		MobileServiceUser user = new MobileServiceUser(tmp1);
		user.setAuthenticationToken(tmp2);
		mClient.setCurrentUser(user);		
		return true;
	}


	private void cacheUser(MobileServiceUser user)
	{
		SharedPreferences prefs = getSharedPreferences("temp", Context.MODE_PRIVATE);
		Editor editor = prefs.edit();
		editor.putString("tmp1", user.getUserId());
		editor.putString("tmp2", user.getAuthenticationToken());
		editor.commit();
	}


トークンが期限切れになった場合はどのような処理が行われるでしょうか。その場合、トークンを使用して接続を試みると、"*401 許可されていません*" 応答が返されます。ユーザーは、ログインして新しいトークンを取得する必要があります。Mobile Services の呼び出しと Mobile Services からの応答を取得するフィルターを使用すると、アプリケーション内で Mobile Services を呼び出すすべての場所にこれを処理するコードを書かずに済みます。フィルター コードでは、次に 401 の応答の有無をテストし、必要に応じてログイン プロセスをトリガーし、401 を生成した要求を再開します。


<h2><a name="errors"></a>方法:エラーを処理する</h2>

検証とエラー処理の例については、<a href="https://www.windowsazure.com/ja-jp/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet/" target="_blank">ここ</a>を参照してください。サーバー スクリプトでエラー時に例外を返す検証を実装する方法と、例外を処理するクライアント コードが紹介されています。

もう 1 つの方法は、 *global* エラー ハンドラーを提供する方法です。これまで紹介したモバイル サービス テーブルにアクセスするコードでは、3 つの異なるコールバック オブジェクトが使用されています。

- **TableQueryCallback** / **TableQueryJsonCallback**
- **TableOperationCallback** / **TableJsonOperationCallback**
- **TableDeleteCallback**

それぞれのコールバック オブジェクトは、2 つ目のパラメーターが **java.lang.Exception** オブジェクトである **OnCompleted** メソッドを持ちます。これらのコールバック オブジェクトをサブクラス化し、例外パラメーターが null かどうかを調べる独自の **onCompleted** メソッドを実装できます。例外パラメーターが null の場合はエラーが発生していないことを示します。その場合は、単に <b>super.OnCompleted()</b> を呼び出します。

**Exception** オブジェクトが null でない場合は、汎用的なエラー処理を実行して、エラーに関する詳細情報を表示します。次のコード スニペットに、詳細な情報を表示する 1 つの方法を示します。

		String msg = exception.getCause().getMessage();



これで、開発者はサブクラス化されたコールバックを使用できるようになり、例外を確認する心配がなくなりました。それは、コールバックのすべてのインスタンスに関して例外が (#2) 一元的に処理されるためです。


<h2><a name="customizing"></a>方法:クライアントをカスタマイズする</h2>

### <a name="headers"></a>方法:要求ヘッダーをカスタマイズする

すべての出力要求にカスタム ヘッダーを接続する場合があります。次のような ServiceFilter を構成することによって実現できます。

		client = client.withFilter(new ServiceFilter() {
		
		    @Override
		    public void handleRequest(ServiceFilterRequest request,
					NextServiceFilterCallback nextServiceFilterCallback,
		        	ServiceFilterResponseCallback responseCallback) {
		        request.addHeader("My-Header", "Value");      
		        nextServiceFilterCallback.onNext(request, responseCallback);
		    }
		});


### <a name="serialization"></a>方法:シリアル化をカスタマイズする

モバイル サービスでは、サーバー上のテーブル名、列名、データ型のすべてがクライアント上の対応する名前や型と一致するものと既定で想定しています。ただし、サーバーとクライアントとの間で名前が一致しない理由はいくつかあります。1 つの例として、既存のクライアントがあり、これを競合企業の製品の代わりに Azure Mobile Services を使用するように変更することを検討しているとします。

次の種類のカスタマイズを行うことができます。
<ul>
<li>
モバイル サービス テーブルで使用されている列名がクライアントで使用されている名前と一致しない</li>

<li>クライアントでマップされているクラスと異なる名前を持つモバイル サービス テーブルを使用する</li>
<li>プロパティの大文字の自動設定を有効にする</li>

<li>複合プロパティをオブジェクトに追加する</li>

</ul>

### <a name="columns"></a>方法:クライアントとサーバーの間で異なる名前をマップする

Java クライアント コードにおいて *ToDoItem* オブジェクト プロパティに次のような標準 Java 形式の名前を使用しているとします。 
<ul>
<li>mId</li>
<li>mText</li>
<li>mComplete</li>
<li>mDuration</li>

</ul>

クライアントの名前は、サーバー上の *ToDoItem* テーブルの列名と一致する JSON 名にシリアル化する必要があります。<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用してこの操作を行うコードを次に示します。

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;
 
	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>方法:クライアントとモバイル サービスの間で異なるテーブル名をマップする

Mapping the クライアント テーブル名を異なるモバイル サービス テーブル名にマップするのは簡単です。単に、
<a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 関数のオーバーライドの 1 つを使用するだけです。次のコードに例を示します。

		mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>方法:列名のマッピングを自動化する

列が数個しかない狭いテーブルの列名のマッピングは、前のセクションに示したように簡単に行うことができます。ここで、テーブルに多数の列 (たとえば、20 ～ 30) があるとします。その場合は、<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> API を呼び出して、すべての列に適用される変換戦略を指定することにより、それぞれの列名に注釈を付けずに済みます。

そのためには、<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用します。Android クライアント ライブラリは、このライブラリをバックグラウンドで使用して Java オブジェクトを Azure Mobile Services に送信される JSON データにシリアル化します。

次のコードでは、 *setFieldNamingStrategy()* メソッドが定義された  *FieldNamingStrategy()* メソッドを使用しています。このメソッドでは、すべてのフィールド名について、最初の文字 ("m") を削除し、その次の文字を小文字にすることを指定しています。さらに、このコードは、出力 JSON の再フォーマットを有効にしています。

	client.setGsonBuilder(
	    MobileServiceClient
	    .createMobileServiceGsonBuilder()
	    .setFieldNamingStrategy(new FieldNamingStrategy() {
	        public String translateName(Field field) {
	            String name = field.getName();
	            return Character.toLowerCase(name.charAt(1))
	                + name.substring(2);
	            }
	        })
	        .setPrettyPrinting());
	


このコードは、モバイル サービス クライアント オブジェクトのすべてのメソッド呼び出しの前に実行する必要があります。

### <a name="complex"></a>方法:オブジェクトまたは配列プロパティをテーブルに保存する 

これまでのシリアル化のすべての例には、JSON およびモバイル サービス テーブルに簡単にシリアル化できるプリミティブ型 (整数、文字列など) が使用されていました。ここで、JSON やテーブルに自動的にシリアル化されない複合オブジェクトをクライアント型に追加する場合を想定します。たとえば、文字列の配列をクライアント オブジェクトに追加するとします。この段階で、シリアル化を行う方法と配列をモバイル サービス テーブルに格納する方法は自由に選ぶことができます。

これを行う方法の例については、<a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Mobile Services Android クライアントでの <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用したシリアル化のカスタマイズ</a>に関するブログ記事を参照してください。

この汎用的な方法は、JSON やモバイル サービス テーブルに自動的にシリアル化されない複合オブジェクトがある場合にいつでも使用できます。


## <a name="next-steps"></a>次のステップ

Android クライアント API に関する Javadocs リファレンスについては、[http://dl.windowsazure.com/androiddocs/com/microsoft/windowsazure/mobileservices/package-summary.html](http://go.microsoft.com/fwlink/p/?LinkId=298735 "here") を参照してください。

<!-- Anchors. -->

[Mobile Services とは]: #what-is
[概念]: #concepts
[方法:モバイル サービス クライアントを作成する]: #create-client
[方法:テーブル参照を作成する]: #instantiating
[API の構造]: #api
[方法:モバイル サービスのデータを照会する]: #querying
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[方法:クエリ メソッドを連結する]: #chaining
[方法:データをユーザー インターフェイスにバインドする]: #binding
[方法:レイアウトを定義する]: #layout
[方法:アダプターを定義する]: #adapter
[方法:アダプターを使用する]: #use-adapter
[方法:モバイル サービスにデータを挿入する]: #inserting
[方法:モバイル サービスのデータを更新する]: #updating
[方法:モバイル サービスのデータを削除する]: #deleting
[方法:特定の項目を検索する]: #lookup
[方法:型指定のないデータを処理する]: #untyped
[方法:ユーザーを認証する]: #authentication
[認証トークンをキャッシュする]: #caching
[方法:エラーを処理する]: #errors
[方法:単体テストを設計する]: #tests
[方法:クライアントをカスタマイズする]: #customizing
[要求ヘッダーをカスタマイズする]: #headers
[シリアル化をカスタマイズする]: #serialization
[次のステップ]: #next-steps
[セットアップと前提条件]: #setup

<!-- Images. -->


















<!-- URLs. -->
[Get started with Mobile Services]: /ja-jp/develop/mobile/tutorials/get-started-android/
[モバイル サービス SDK]: http://go.microsoft.com/fwlink/p/?linkid=280126
[認証の使用]: /ja-jp/develop/mobile/tutorials/get-started-with-users-android/
[ASCII 制御コード C0 および C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services テーブルの管理用コマンド]: http://www.windowsazure.com/ja-jp/manage/linux/other-resources/command-line-tools/#Mobile_Tables


<!--HONumber=42-->
