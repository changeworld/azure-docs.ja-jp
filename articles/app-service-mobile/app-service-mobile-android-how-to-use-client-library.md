<properties
	pageTitle="Android Mobile Apps クライアント ライブラリを使用する方法"
	description="Azure Mobile Apps 向け Android クライアント SDK の使用方法。"
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="ricksal"/>


# Mobile Apps 向け Android クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、Mobile Apps 向け Android クライアント SDK を使用して、データのクエリ (挿入、更新、削除)、ユーザーの認証、エラー処理、クライアントのカスタマイズなどの一般的なシナリオを実装する方法について説明します。また、ほとんどのモバイル アプリで使用される共通のクライアント コードについても詳しく説明します。

このガイドでは、クライアント側の Android SDK に注目します。Mobile Apps 用のサーバー側 SDK の詳細については、「[Azure Mobile Apps 用 .NET バックエンド サーバー SDK の操作](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)」または「[Azure Mobile Apps Node.js SDK の使用方法](app-service-mobile-node-backend-how-to-use-server-sdk.md)」を参照してください。

## リファレンス ドキュメント

[GitHub](http://azure.github.io/azure-mobile-apps-android-client/) で、Android クライアント ライブラリの Javadoc API リファレンスを検索できます。

## セットアップと前提条件

Android 用 Mobile Services SDK は Android バージョン 2.2 以降をサポートしますが、バージョン 4.2 以降を対象に作成することをお勧めします。

[Mobile Apps のクイックスタート](app-service-mobile-android-get-started.md) チュートリアルを完了して、Android Studio がインストールされた状態にします。これは、アカウントを構成し、最初のモバイル アプリ バックエンドを作成するのに役立ちます。これを行う場合は、このセクションの残りの部分を省略できます。

クイックスタート チュートリアルを行わず、Android アプリをモバイル アプリ バックエンドに接続する場合は、以下を行う必要があります。

- Android アプリで使用する[モバイル アプリ バックエンドを作成します](app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend) (アプリにまだない場合)
- Android Studio で [Gradle ビルド ファイルを更新します](#gradle-build)
- [インターネット アクセス許可を有効にします](#enable-internet)

以上を行った後、詳細セクションで説明する手順を完了する必要があります。

###<a name="gradle-build"></a>Gradle ビルド ファイルを更新する

2 つの **build.gradle** ファイルを変更します。

1. 次のコードを、*プロジェクト* レベルの **build.gradle** ファイルの *buildscript* タグ内に追加します。

		buildscript {
		    repositories {
		        jcenter()
		    }
		}

2. 次のコードを、*モジュール アプリ* レベルの **build.gradle** ファイルの *dependencies* タグ内に追加します。

		compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

	現在の最新バージョンは 3.1.0 です。サポートされているバージョンの一覧については、[こちら](http://go.microsoft.com/fwlink/p/?LinkID=717034)を参照してください。

###<a name="enable-internet"></a>インターネット アクセス許可を有効にする
Azure にアクセスするには、アプリで INTERNET アクセス許可が有効になっている必要があります。まだ有効になっていない場合は、次のコード行を **AndroidManifest.xml** ファイルに追加します。

	<uses-permission android:name="android.permission.INTERNET" />

## 基本の詳細情報

このセクションでは、Quickstart アプリのコードについて説明します。クイックスタートを行わなかった場合は、このコードを自分のアプリに追加する必要があります。

> [AZURE.NOTE] "MobileServices" という文字列がコード内に頻繁に出現します。コードは実際には Mobile Apps SDK を参照します。これは過去のコードから一時的に持ち越されているだけです。


###<a name="data-object"></a>クライアント データ クラスを定義する

SQL Azure のテーブルからデータにアクセスするには、モバイル アプリ バックエンドのテーブルに対応するクライアント データ クラスを定義します。このトピックの例では、次の列を含む、*ToDoItem* という名前のテーブルがあるものとします。

- id
- text
- 完了

これに対応する型指定されたクライアント側オブジェクトを次に示します。

	public class ToDoItem {
		private String id;
		private String text;
		private Boolean complete;
	}

コードは **ToDoItem.java** という名前のファイルに存在します。

SQL Azure テーブルにさらに列が含まれる場合は、対応するフィールドをこのクラスに追加します。

たとえば、整数の Priority 列がある場合は、次のフィールドと共にその getter および setter メソッドを追加します。

	private Integer priority;

	/**
	* Returns the item priority
	*/
	public Integer getPriority() {
	return mPriority;
	}
	
	/**
	* Sets the item priority
	*
	* @param priority
	*            priority to set
	*/
	public final void setPriority(Integer priority) {
	mPriority = priority;
	}

Mobile Apps バックエンドに追加のテーブルを作成する方法については、「[方法: テーブル コントローラーを定義する](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller)」(.NET バックエンド) または「[方法: 動的スキーマを使用してテーブルを定義する](app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations)」(Node.js バックエンド) を参照してください。Node.js バックエンドでは、[Azure ポータル]の **[テーブルの簡単操作]** 設定を使用することもできます。

###<a name="create-client"></a>方法: クライアント コンテキストを作成する

次のコードは、モバイル アプリ バックエンドにアクセスするために使用される **MobileServiceClient** オブジェクトを作成します。このコードは、**MAIN** アクションと **LAUNCHER** カテゴリとして *AndroidManifest.xml* で指定された **Activity** クラスの `onCreate` メソッドにあります。Quickstart のコードの場合は、**ToDoActivity.java** ファイルにあります。

		MobileServiceClient mClient = new MobileServiceClient(
			"MobileAppUrl", // Replace with the above Site URL
			this)

このコードで、`MobileAppUrl` をモバイル アプリ バックエンドの URL に置き換えます。この URL は、[Azure ポータル](https://portal.azure.com/)のモバイル アプリ バックエンド用ブレードで確認できます。このコード行をコンパイルするには、次の **import** ステートメントも追加する必要があります。

	import com.microsoft.windowsazure.mobileservices.*;

###<a name="instantiating"></a>方法: テーブル参照を作成する

Java は厳密に型指定された言語であるため、バックエンドのデータを照会または変更する最も簡単な方法は、*型指定されたプログラミング モデル*を使用する方法です (後で*型指定のない*モデルについて説明します)。このモデルは、クライアント オブジェクトとバックエンドの Azure SQL のテーブルの間でデータを送信するときに、[gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) ライブラリを使用して JSON のシームレスなシリアル化と非シリアル化を提供します。開発者は何も作業する必要がありません。フレームワークによってすべてが処理されます。

テーブルにアクセスするには、最初に [MobileServiceClient](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html) の **getTable** メソッドを呼び出して [MobileServiceTable](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html) オブジェクトを作成します。このメソッドには 2 つのオーバーロードがあります。

	public class MobileServiceClient {
	    public <E> MobileServiceTable<E> getTable(Class<E> clazz);
	    public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
	}

次のコードで、*mClient* は MobileServiceClient オブジェクトへの参照です。

[1 つ目のオーバーロード](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-)は、クラス名とテーブル名が同じ場合に使用され、Quickstart で使用されているものです。

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);


[2 つ目のオーバーロード](http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html#getTable-java.lang.String-java.lang.Class-)は、テーブル名とクラス名が異なる場合に使用されます。1 番目のパラメーターはテーブル名です。

	MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

###<a name="binding"></a>方法: データをユーザー インターフェイスにバインドする

データ バインドには、次の 3 つのコンポーネントが関係します。

- データ ソース
- 画面レイアウト
- これらの 2 つを連結するアダプター

次のサンプル コードでは、Mobile Apps の SQL Azure テーブル *ToDoItem* のデータを配列に返します。これは、データ アプリケーションで非常に一般的なパターンです。多くの場合、データベース クエリでは、行のコレクションが返されます。クライアントは、これをリストまたは配列に取得します。この例では、配列はデータ ソースです。

コードで、デバイスに表示されるデータのビューを定義する画面レイアウトを指定します。

これらの 2 つをアダプターによってバインドします。アダプターは、このコードでは *ArrayAdapter&lt;ToDoItem&gt;* クラスの拡張です。

#### <a name="layout"></a>方法: レイアウトを定義する

レイアウトは、XML コードの複数のスニペットで定義されます。既存のレイアウトがあり、次のコードがサーバーのデータを設定する **ListView** を表すと想定します。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

前のコードで、*listitem* 属性は、リスト内の個々の行のレイアウトの ID を指定します。次にそのコードを示します。チェック ボックスとそれに関連付けられたテキストを指定しています。これは、リスト内のそれぞれの項目に対して 1 回インスタンス化されます。このレイアウトでは **id** フィールドが表示されないため、さらに複雑なレイアウトで、表示する追加のフィールドを指定します。このコードは、**row\_list\_to\_do.xml** ファイルに含まれています。

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


#### <a name="adapter"></a>方法: アダプターを定義する

このビューのデータ ソースは *ToDoItem* の配列であるため、*ArrayAdapter<ToDoItem>* クラスからアダプターをサブクラス化します。このサブクラスでは、*row\_list\_to\_do* レイアウトを使用してすべての *ToDoItem* のビューを生成します。

コードでは、*ArrayAdapter&lt;E&gt;* クラスの拡張である次のクラスを定義します。

	public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {


アダプターの *getView* メソッドはオーバーライドする必要があります。その方法の 1 つを次のサンプル コードに示します。詳細はアプリケーションによって異なります。

    @Override
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

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


		return row;
	}

次に示すように、このクラスのインスタンスを Activity 内で作成します。

	ToDoItemAdapter mAdapter;
	mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter コンストラクターの 2 つ目のパラメーターはレイアウトへの参照であることに注意してください。コンストラクターの呼び出しに続けて、次のコードを記述します。このコードは、最初に **ListView** への参照を取得します。次に、*setAdapter* を呼び出して、作成したアダプターを使用するようにそれ自体を構成します。

	ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
	listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API の構造

Mobile Apps のテーブル操作とカスタム API 呼び出しは非同期であるため、クエリおよび挿入、更新、削除が関係するすべての非同期メソッドで、[Future](http://developer.android.com/reference/java/util/concurrent/Future.html) オブジェクトおよび [AsyncTask](http://developer.android.com/reference/android/os/AsyncTask.html) オブジェクトを使用します。これにより、複数の入れ子になったコールバックを処理しなくてもバックグラウンド スレッドで簡単に複数の操作を実行できます。

Android アプリでのこれらの非同期 API の使用方法、およびデータを UI に表示する方法については、[Azure ポータル]で提供されている Android クイックスタート プロジェクトの **ToDoActivity.java** ファイルを確認してください。


#### <a name="use-adapter"></a>方法: アダプターを使用する

これで、データ バインドを使用する準備が整いました。次のコードでは、モバイル サービス テーブルの項目を取得した後、アダプターをクリアしています。次に、アダプターの *add* メソッドを呼び出して、返された項目をアダプターに設定しています。

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
		runAsyncTask(task);
    }

*ToDoItem* テーブルを変更したときにその結果を表示するには、その都度アダプターを呼び出す必要があります。変更はレコード単位で加えられるため、操作の対象はコレクションではなく行になります。項目を挿入する場合は、アダプターの *add* メソッドを呼び出します。項目を削除する場合は、*remove* メソッドを呼び出します。

##<a name="querying"></a>方法: モバイル アプリ バックエンドのデータを照会する

このセクションでは、モバイル アプリ バックエンドにクエリを発行する方法について説明します。これには次のタスクが含まれます。

- [すべての項目を返す]
- [返されるデータをフィルター処理する]
- [返されるデータを並べ替える]
- [ページにデータを返す]
- [特定の列を選択する]
- [クエリ メソッドを連結する](#chaining)

### <a name="showAll"></a>方法: テーブルからすべての項目を返す

次のクエリは、*ToDoItem* テーブル内のすべての項目を返します。

	List<ToDoItem> results = mToDoTable.execute().get();

*results* 変数は、クエリの結果セットをリストとして返します。

### <a name="filtering"></a>方法: 返されるデータをフィルター処理する

次のクエリを実行すると、*ToDoItem* テーブルで *complete* が *false* と等しい項目がすべて返されます。これは、Quickstart に既にあるコードです。

	List<ToDoItem> result = mToDoTable.where()
								.field("complete").eq(false)
								.execute().get();

*mToDoTable* は、既に作成したモバイル サービス テーブルへの参照です。

テーブル参照で **where** メソッド呼び出しを使用してフィルターを定義します。次に **field** メソッドを呼び出し、その次に論理述語を指定するメソッドを呼び出します。使用できる述語メソッドとしては、**eq** (等しい)、**ne** (等しくない)、**gt** (より大きい)、**ge** (以上)、**lt** (より小さい)、**le** (以下) などがあります。これらのメソッドを使用して、数値フィールドおよび文字列フィールドを特定の値と比較できます。

日付をフィルター処理できます。次のメソッドを使用して、日付フィールド全体または日付の一部を比較できます。**year**、**month**、**day**、**hour**、**minute**、**second**。次の例では、期限 (*due*) が 2013 に等しい項目のフィルターを追加しています。

	mToDoTable.where().year("due").eq(2013).execute().get();

**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim**、**length** などのメソッドは、文字列フィールドに対する複雑なフィルターをサポートします。次の例では、*text* 列が "PRI0" で始まるテーブル行をフィルター処理します。

	mToDoTable.where().startsWith("text", "PRI0").execute().get();

数値フィールドでは、**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling**、**round** 演算子メソッドがサポートされます。次の例では、*duration* が偶数のテーブル行をフィルター処理します。

	mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

論理メソッド **and**、**or**、**not** を使用すると、述語を結合できます。次の例では、前の 2 つの例を結合しています。

	mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
				.execute().get();

次のように、論理演算子をグループ化したり入れ子にしたりすることができます。

	mToDoTable.where()
				.year("due").eq(2013)
					.and
				(startsWith("text", "PRI0").or().field("duration").gt(10))
				.execute().get();

フィルター処理の詳細と例については、「[Exploring the richness of the Android client query model (Android クライアント クエリ モデルの機能を調査する)](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)」を参照してください。

### <a name="sorting"></a>方法: 返されるデータを並べ替える

次のコードは、*ToDoItems* テーブルから、*text* フィールドの値に基づいて昇順に並べ替えられたすべての項目を返します。*mToDoTable* は、以前に作成したバックエンド テーブルへの参照です。

	mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** メソッドの 1 つ目のパラメーターは、並べ替えに使用するフィールドの名前に等しい文字列です。

2 番目のパラメーターでは、**QueryOrder** 列挙を使用して、昇順または降順のどちらで並べ替えを行うかを指定します。

***where*** メソッドを使用してフィルター処理を行う場合、***where*** メソッドは ***orderBy*** メソッドの前に呼び出す必要があります。

### <a name="paging"></a>方法: ページにデータを返す

最初の例は、テーブルから最初の 5 つの項目を選択する方法を示しています。このクエリは、*ToDoItems* テーブルから項目を返します。*mToDoTable* は、以前に作成したバックエンド テーブルへの参照です。

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


次に示すのは、最初の 5 つの項目をスキップしてその次の 5 つの項目を返すクエリです。

	mToDoTable.skip(5).top(5).execute().get();


### <a name="selecting"></a>方法: 特定の列を選択する

次のコードは、*ToDoItems* テーブルからすべての項目を返したうえで *complete* フィールドと *text* フィールドのみを表示する方法を示しています。*mToDoTable* は、以前に作成したバックエンド テーブルへの参照です。

	List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();


select 関数のパラメーターは、取得するテーブルの列の文字列名です。

**select** メソッドは、**where** や **orderBy** のようなメソッドがある場合はその後に記述する必要があります。このメソッドの後に **top** のようなページング メソッドを記述することができます。

### <a name="chaining"></a>方法: クエリ メソッドを連結する

前に説明したように、バックエンド テーブルのクエリに使用されるメソッドは連結できます。これにより、並べ替えとページングが行われたフィルター処理された行の特定の列を選択するなど、さまざまな操作を行うことができます。複雑な論理フィルターを作成できます。

そのしくみは、使用しているクエリ メソッドによって **MobileServiceQuery&lt;T&gt;** オブジェクトを返し、次にこのオブジェクトで追加のメソッドを呼び出します。一連のメソッドを終了し、クエリを実際に実行するには、**execute** メソッドを呼び出します。

次にコード サンプルを示します。ここで、*mToDoTable* は、*ToDoItem* テーブルへの参照です。

	mToDoTable.where().year("due").eq(2013)
					.and().startsWith("text", "PRI0")
					.or().field("duration").gt(10)
				.select("id", "complete", "text", "duration")
				.orderBy(duration, QueryOrder.Ascending).top(20)
				.execute().get();

メソッドを連結する場合の主な要件として、*where* メソッドと述語を最初に記述する必要があります。それ以降、アプリケーションのニーズに最も合う順番で後続のメソッドを呼び出すことができます。


##<a name="inserting"></a>方法: バックエンドにデータを挿入する

次のコードは、テーブルに新しい行を挿入する方法を示しています。

最初に、*ToDoItem* クラスのインスタンスをインスタンス化し、そのプロパティを設定します。

	ToDoItem item = new ToDoItem();
	item.text = "Test Program";
	item.complete = false;

次に、次のコードを実行します。

	ToDoItem entity = mToDoTable.insert(item).get();

返されるエンティティは、バックエンドで設定された ID および他の値を含め、バックエンド テーブルに挿入されたデータと一致します。

Mobile Apps では、各テーブルに **id** という名前の列がある必要があります。これは、テーブルにインデックスを設定するために使用されます。既定では、この列は文字列データ型です。これは、オフライン同期をサポートするために必要です。ID 列の既定値は GUID ですが、電子メール アドレスやユーザー名などの他の一意の値を指定することができます。挿入されたレコードに対して文字列 ID 値が提供されていない場合、バックエンドは新しい GUID 値を生成します。

文字列 ID 値には次のような利点があります。

+ データベースを往復しなくても ID を生成できます。
+ 他のテーブルやデータベースのレコードをより簡単にマージできます。
+ ID 値はより適切にアプリケーションのロジックと統合します。

##<a name="updating"></a>方法: モバイル アプリのデータを更新する

次のコードは、テーブルのデータを更新する方法を示しています。

    mToDoTable.update(item).get();

この例では、*item* は、いくつかの変更が加えられた *ToDoItem* テーブルの行への参照です。

##<a name="deleting"></a>方法: モバイル アプリのデータを削除する

次のコードでは、データ オブジェクトを指定することによってテーブルのデータを削除する方法を示します。

	mToDoTable.delete(item);

削除する行の **id** フィールドを指定して、項目を削除することもできます。

	String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
   	mToDoTable.delete(myRowId);


##<a name="lookup"></a>方法: 特定の項目を検索する

このコードでは、特定の *id* で項目を検索する方法を示します。

	ToDoItem result = mToDoTable
						.lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
						.get();

##<a name="untyped"></a>方法: 型指定のないデータを処理する

型指定のないプログラミング モデルでは、JSON シリアル化を的確に制御できます。このモデルを使用するシナリオとしては、バックエンド テーブルに大量の列が含まれ、参照する必要があるのはそのいくつかのみである場合などが想定されます。型指定されたモデルを使用するには、モバイル アプリ テーブルのすべての列をデータ クラスに定義する必要があります。一方、型指定のないモデルでは、使用する列を定義するだけで済みます。

データにアクセスするための API 呼び出しのほとんどは、型指定されたプログラミングでの呼び出しに似ています。主な違いとして、型指定のないモデルでは、**MobileServiceTable** オブジェクトの代わりに **MobileServiceJsonTable** オブジェクトのメソッドを呼び出します。


### <a name="json_instance"></a>方法: 型指定のないテーブルのインスタンスを作成する

型指定されたモデルと同様、テーブル参照を取得することから始めますが、このケースでのオブジェクトは**MobileServicesJsonTable** です。次のように、クライアントのインスタンスで **getTable()** メソッドを呼び出して、参照を取得します。

    private MobileServiceJsonTable mJsonToDoTable;
	//...
    mJsonToDoTable = mClient.getTable("ToDoItem");

**MobileServiceJsonTable** のインスタンスを作成すると、型指定されたプログラミング モデルで呼び出すことができるほぼすべてのメソッドをそのインスタンスで呼び出すことができます。ただし、次の例に示すように、メソッドが型指定のないパラメーターを受け取る場合もあります。

### <a name="json_insert"></a>方法: 型指定のないテーブルに挿入する

次のコードは、挿入操作を行う方法を示しています。最初に、[**JsonObject**](http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html) を作成します。これは、[gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) ライブラリの一部です。

	JsonObject jsonItem = new JsonObject();
	jsonItem.addProperty("text", "Wake up");
	jsonItem.addProperty("complete", false);

次に、このオブジェクトを挿入します。

    mJsonToDoTable.insert(jsonItem).get();


挿入されたオブジェクトの ID を取得する必要がある場合は、このメソッド呼び出しを使用します。

	jsonItem.getAsJsonPrimitive("id").getAsInt());


### <a name="json_delete"></a>方法: 型指定のないテーブルから削除する

次のコードでは、インスタンス (このケースでは前の **insert** の例で作成したのと同じ *JsonObject* のインスタンス) を削除する方法を示します。このコードは、型指定されたものと同じですが、このメソッドは **JsonObject** を参照するため、署名が異なります。


         mToDoTable.delete(jsonItem);


ID を使用してインスタンスを直接削除することもできます。

		 mToDoTable.delete(ID);


### <a name="json_get"></a>方法: 型指定のないテーブルからすべての行を返す

次のコードは、テーブル全体を取得する方法を示しています。JSON テーブルを使用しているため、テーブルの列の一部のみを選択的に取得できます。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

型指定されたプログラミング モデルで使用されたのと同じ名前を持つメソッドを連結して、フィルター処理、並べ替え、およびページングを行うことができます。



##<a name="custom-api"></a>方法: カスタム API の呼び出し

カスタム API を使用してカスタム エンドポイントを定義することにより、insert、update、delete、read のいずれの操作にも関連しないサーバー機能を公開することができます。カスタム API を使用することによって、HTTP メッセージ ヘッダーの読み取りや設定、JSON 以外のメッセージ本文形式の定義など、メッセージングをより柔軟に制御することができます。

Android クライアントから **invokeApi** メソッドを呼び出して、カスタム API エンドポイントを呼び出します。次の例では、*completeAll* という名前の API エンドポイントを呼び出す方法を示します。このエンドポイントは、MarkAllResult という名前のコレクション クラスを返します。

	public void completeItem(View view) {

	    ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

	    	Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
	    		@Override
	    		public void onFailure(Throwable exc) {
	    			createAndShowDialog((Exception) exc, "Error");
	    		}

	    		@Override
	    		public void onSuccess(MarkAllResult result) {
	    			createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
	                refreshItemsFromTable();
	    		}
	    	});
	    }

POST 要求を新しいカスタム API に送信する **invokeApi** メソッドがクライアントで呼び出されます。カスタム API から返された結果は、メッセージ ダイアログに表示されます。エラーが発生した場合はそれらも表示されます。オプションとして、他のバージョンの **invokeApi** を使用すると、要求本文でのオブジェクトの送信、HTTP メソッドの指定、要求でのクエリ パラメーターの送信が可能です。**invokeApi** の型指定なしバージョンも用意されています。

##<a name="authentication"></a>方法: アプリに認証を追加する

これらの機能を追加する方法については、チュートリアルで既に詳しく説明されています。

App Service は、Facebook、Google、Microsoft アカウント、Twitter、Azure Active Directory などのさまざまな外部 ID プロバイダーを使用した[アプリ ユーザーの認証](app-service-mobile-android-get-started-users.md)をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、バックエンドで承認ルールを実装することもできます。

*サーバー* フローと*クライアント* フローという 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。そのためのコードを作成する必要があります。

アプリケーションで認証を有効にするには、次の 3 つの手順を実行する必要があります。

- 認証するアプリケーションをプロバイダーに登録し、モバイル アプリ バックエンドを構成します。
- テーブルのアクセス許可を、認証されたユーザーのみに制限します。
- アプリケーションに認証コードを追加します。

テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの SID を使用して要求を変更することもできます。

これらの最初の 2 つのタスクは、[Azure ポータル](https://portal.azure.com/)を使用して実行します。詳細については、「[認証の使用]」を参照してください。

### <a name="caching"></a>方法: アプリケーションに認証コードを追加する

次のコードは、Google プロバイダーを使用してサーバー フローのログイン プロセスを開始します。

	MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

**getUserId** メソッドを使用して、**MobileServiceUser** からログイン ユーザーの ID を取得できます。Futures を使用して非同期ログイン API を呼び出す方法の例については、「[Android アプリに認証を追加する]」を参照してください。


### <a name="caching"></a>方法: 認証トークンをキャッシュする

認証トークンをキャッシュするには、ユーザー ID と認証トークンをデバイスにローカルで保存する必要があります。アプリケーションが次回起動されたときに、キャッシュを確認し、これらの値が存在する場合はログイン手順をスキップし、クライアントに再度このデータを渡します。ただし、このデータは慎重な扱いを要する情報であり、電話の盗難に備えて安全のために暗号化して保存する必要があります。

認証トークンをキャッシュする方法の完全な例については、「[方法: 認証トークンをキャッシュする](app-service-mobile-android-get-started-users.md#cache-tokens)」セクションを参照してください。

期限切れトークンを使用しようとすると、"*401 許可されていません*" 応答が返されます。ユーザーは、ログインして新しいトークンを取得する必要があります。Mobile Services の呼び出しと Mobile Services からの応答を取得するフィルターを使用すると、アプリケーション内でモバイル サービスを呼び出すすべての場所にこれを処理するコードを書かずに済みます。フィルター コードは、401 の応答の有無をテストし、必要に応じてログイン プロセスをトリガーし、401 を生成した要求を再開します。トークンを調べて期限切れを確認することもできます。


## <a name="adal"></a>方法: Active Directory 認証ライブラリを使用してユーザーを認証する

Active Directory 認証ライブラリ (ADAL) を使用して、Azure Active Directory を使用しているアプリケーションにユーザーをサインインさせることができます。これはよりネイティブ UX の感覚を提供し、さらなるカスタマイズが可能なため、多くの場合、`loginAsync()` メソッドの使用よりも推奨されます。

1. 「[Azure Active Directory ログインを使用するように App Service アプリケーションを構成する方法](app-service-mobile-how-to-configure-active-directory-authentication.md)」のチュートリアルに従って、AAD のサインイン用にモバイル アプリ バックエンドを構成します。ネイティブ クライアント アプリケーションを登録する省略可能な手順を確実に実行します。

2. build.gradle ファイルに以下を含めて変更し、ADAL をインストールします。

	repositories { mavenCentral() flatDir { dirs 'libs' } maven { url "YourLocalMavenRepoPath\\.m2\\repository" } } packagingOptions { exclude 'META-INF/MSFTSIG.RSA' exclude 'META-INF/MSFTSIG.SF' } dependencies { compile fileTree(dir: 'libs', include: ['*.jar']) compile('com.microsoft.aad:adal:1.1.1') { exclude group: 'com.android.support' } // Recent version is 1.1.1 compile 'com.android.support:support-v4:23.0.0' }

3. 以下のコードをアプリケーションに追加して、次の置換を行います。

* **INSERT-AUTHORITY-HERE** を、アプリケーションをプロビジョニングしたテナントの名前に置き換えます。形式は https://login.windows.net/contoso.onmicrosoft.com である必要があります。この値は、[Azure クラシック ポータル] の Azure Active Directory の [ドメイン] タブからコピーできます。

* **INSERT-RESOURCE-ID-HERE** を、モバイル アプリ バックエンドのクライアント ID に置き換えます。これは、ポータルの **[Azure Active Directory の設定]** の **[詳細]** タブで入手できます。

* **INSERT-CLIENT-ID-HERE** を、ネイティブ クライアント アプリケーションからコピーしたクライアント ID に置き換えます。

* **INSERT-REDIRECT-URI-HERE** を、HTTPS スキームを使用して、サイトの _/.auth/login/done_ エンドポイントに置き換えます。この値は、https://contoso.azurewebsites.net/.auth/login/done_ と同様です。

		private AuthenticationContext mContext;
		private void authenticate() {
		String authority = "INSERT-AUTHORITY-HERE";
		String resourceId = "INSERT-RESOURCE-ID-HERE";
		String clientId = "INSERT-CLIENT-ID-HERE";
		String redirectUri = "INSERT-REDIRECT-URI-HERE";
		try {
		    mContext = new AuthenticationContext(this, authority, true);
		    mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
		} catch (Exception exc) {
		    exc.printStackTrace();
		}
		}
		private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
		@Override
		public void onError(Exception exc) {
		    if (exc instanceof AuthenticationException) {
		        Log.d(TAG, "Cancelled");
		    } else {
		        Log.d(TAG, "Authentication error:" + exc.getMessage());
		    }
		}
		@Override
			public void onSuccess(AuthenticationResult result) {
		    if (result == null || result.getAccessToken() == null
		            || result.getAccessToken().isEmpty()) {
		        Log.d(TAG, "Token is empty");
		    } else {
		        try {
		            JSONObject payload = new JSONObject();
		            payload.put("access_token", result.getAccessToken());
		            ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
		            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
		                @Override
		                public void onFailure(Throwable exc) {
		                    exc.printStackTrace();
		                }
		                @Override
		                public void onSuccess(MobileServiceUser user) {
		            		Log.d(TAG, "Login Complete");
		                }
		            });
		        }
		        catch (Exception exc){
		            Log.d(TAG, "Authentication error:" + exc.getMessage());
		        }
		    }
		}
		};
		@Override
		protected void onActivityResult(int requestCode, int resultCode, Intent data) {
		super.onActivityResult(requestCode, resultCode, data);
		if (mContext != null) {
		    mContext.onActivityResult(requestCode, resultCode, data);
		}
		}


## 方法: アプリにプッシュ通知を追加する

Microsoft Azure Notification Hubs によるさまざまなプッシュ通知のサポート方法については、[概要](../notification-hubs/notification-hubs-overview.md#integration-with-app-service-mobile-apps)をご確認ください。

[このチュートリアル](app-service-mobile-android-get-started-push.md)では、レコードが挿入されるたびに、プッシュ通知が送信されます。

## 方法: アプリにオフライン同期を追加する
クイックスタート チュートリアルには、オフライン同期を実装するコードが含まれます。次のようなコメントの付いたコードを探してください。

	// Offline Sync

次のコード行のコメントを解除することによりオフライン同期を実装することができ、他の Mobile Apps コードに同様のコードを追加できます。

##<a name="customizing"></a>方法: クライアントをカスタマイズする

クライアントの既定の動作は、さまざまな方法でカスタマイズできます。

### <a name="headers"></a>方法: 要求ヘッダーをカスタマイズする

すべての出力要求にカスタム ヘッダーを接続する場合があります。次のような **ServiceFilter** を構成することによって実現できます。

	private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                	ServiceFilterRequest request,
					NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
	        		request.addHeader("My-Header", "Value");	                }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>方法: シリアル化をカスタマイズする

クライアントでは、バックエンドのテーブル名、列名、データ型がすべて、クライアントで定義されているデータ オブジェクトと正確に一致するものと想定されています。ただし、サーバーとクライアントとの間で名前が一致しない理由はいくつかあります。実際のシナリオでは、次の種類のカスタマイズを行うことができます。

- モバイル サービス テーブルで使用されている列名がクライアントで使用されている名前と一致しない。
- クライアントでマップされているクラスと異なる名前を持つモバイル サービス テーブルを使用する。
- プロパティの大文字の自動設定を有効にする。
- 複合プロパティをオブジェクトに追加する。

### <a name="columns"></a>方法: クライアントとサーバーの間で異なる名前をマップする

Java クライアント コードで、ToDoItem オブジェクト プロパティに次のような標準 Java 形式の名前を使用しているとします。

- mId
- mText
- mComplete
- mDuration


クライアントの名前は、サーバー上の*ToDoItem* テーブルの列名と一致する JSON 名にシリアル化する必要があります。[gson](http://go.microsoft.com/fwlink/p/?LinkId=290801) ライブラリを使用してこの操作を行うコードを次に示します。

	@com.google.gson.annotations.SerializedName("text")
	private String mText;

	@com.google.gson.annotations.SerializedName("id")
	private int mId;

	@com.google.gson.annotations.SerializedName("complete")
	private boolean mComplete;

	@com.google.gson.annotations.SerializedName("duration")
	private String mDuration;

### <a name="table"></a>方法: クライアントとバックエンドの間で異なるテーブル名をマップする

クライアントのテーブル名を異なるモバイル サービス テーブル名にマップするのは簡単です。次のコードに示すように、<a href="http://go.microsoft.com/fwlink/p/?LinkId=296840" target="_blank">getTable()</a> 関数のオーバーライドの 1 つを使用するだけです。

	mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);


### <a name="conversions"></a>方法: 列名のマッピングを自動化する

列が数個しかない狭いテーブルの列名のマッピングは、前のセクションに示したように簡単に行うことができます。ここで、テーブルに多数の列 (たとえば、20 ～ 30) があるとします。その場合は、<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> API を呼び出して、すべての列に適用される変換戦略を指定することにより、それぞれの列名に注釈を付けずに済みます。

そのためには、<a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用します。Android クライアント ライブラリは、このライブラリをバックグラウンドで使用して Java オブジェクトを Azure Mobile Services に送信される JSON データにシリアル化します。

次のコードでは、*FieldNamingStrategy()* メソッドが定義された *setFieldNamingStrategy()* メソッドを使用しています。このメソッドでは、すべてのフィールド名について、最初の文字 ("m") を削除し、その次の文字を小文字にすることを指定しています。さらに、このコードは、出力 JSON の再フォーマットを有効にしています。

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



このコードは、Mobile Services クライアント オブジェクトのすべてのメソッド呼び出しの前に実行する必要があります。

### <a name="complex"></a>方法: オブジェクトまたは配列プロパティをテーブルに保存する

これまでのシリアル化のすべての例には、JSON およびモバイル サービス テーブルに簡単にシリアル化できるプリミティブ型 (整数、文字列など) が使用されていました。ここで、JSON やテーブルに自動的にシリアル化されない複合オブジェクトをクライアント型に追加する場合を想定します。たとえば、文字列の配列をクライアント オブジェクトに追加するとします。この段階で、シリアル化を行う方法と配列をモバイル サービス テーブルに格納する方法は自由に選ぶことができます。

これを行う方法の例については、<a href="http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson" target="_blank">Mobile Services Android クライアントでの <a href=" http://go.microsoft.com/fwlink/p/?LinkId=290801" target="_blank">gson</a> ライブラリを使用したシリアル化のカスタマイズに関するブログ記事を参照してください。</a>

この汎用的な方法は、JSON やモバイル サービス テーブルに自動的にシリアル化されない複合オブジェクトがある場合にいつでも使用できます。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[すべての項目を返す]: #showAll
[返されるデータをフィルター処理する]: #filtering
[返されるデータを並べ替える]: #sorting
[ページにデータを返す]: #paging
[特定の列を選択する]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->



<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure ポータル]: https://portal.azure.com
[Android アプリに認証を追加する]: app-service-mobile-android-get-started-users.md
[認証の使用]: app-service-mobile-android-get-started-users.md

<!---HONumber=AcomDC_0727_2016-->