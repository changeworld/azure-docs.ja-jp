<properties 
	pageTitle="Engagement API を Android で使用する方法" 
	description="最新の Android SDK - Engagement API を Android で使用する方法"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kapiteir" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="01/24/2015" 
	ms.author="kapiteir" />

#Engagement API を Android で使用する方法

このドキュメントは、ドキュメント「[Engagement を Android に統合する方法]」の追加ドキュメントです。(mobile-engagement-android-integrate-engagement.md)Engagement API を使用してアプリケーションの統計情報のレポートを作成する方法について詳しく説明します。

Engagement を使用してアプリケーションのセッション、アクティビティ、クラッシュ、および技術情報に関するレポートのみを作成する場合、すべての  `Activity` サブクラスを対応する  `EngagementActivity` クラスから継承するように設定する方法が簡単です。

アプリケーション固有のイベント、エラー、ジョブなどの情報が必要な場合や、 `EngagementActivity` クラスに実装されている以外の方法でアプリケーションのアクティビティに関するレポートを作成する場合は、Engagement API を使用します。

Engagement API は  `EngagementAgent` クラスで提供されます。このクラスのインスタンスは、 `EngagementAgent.getInstance(Context)` 静的メソッドを呼び出すことで取得できます (返される  `EngagementAgent` オブジェクトはシングルトンです)。

##Engagement の概念

次の部分は共通の [Mobile Engagement の概念]を(mobile-engagement-concepts.md)Android プラットフォーム用に改良します。

### `セッション`と `アクティビティ`

2 つの *アクティビティ*間で数秒の休止が発生する場合、 *アクティビティ*のシーケンスは 2 つの別個の *セッション*に分割されます。この数秒間のことを「セッション タイムアウト」と呼びます。

通常、1 つの *アクティビティ*はアプリケーションの 1 つの画面と関連付けられます。つまり、 *アクティビティ*は画面が表示されると開始し、画面が閉じられると終了します。Engagement SDK が  `EngagementActivity` クラスを使用して統合されるのはこのようなケースです。

ただし、 *アクティビティ*は Engagement API を使用して手動で制御することもできます。これにより、指定の画面を複数のサブパーツに分割して、画面の詳しい用途を把握できます (たとえば、画面内でダイアログが使用される頻度と時間を把握できます)。

##アクティビティのレポート作成

> [AZURE.IMPORTANT] 「Engagement を Android に統合する方法」のドキュメントで説明しているとおり、 `EngagementActivity` クラスとそのバリアントを使用している場合は、このセクションで説明するような方法でアクティビティに関するレポートを作成する必要はありません。

### ユーザーが新しいアクティビティを開始する

			EngagementAgent.getInstance(this).startActivity(this, "MyUserActivity", null);
			// Passing the current activity is required for Reach to display in-app notifications, passing null will postpone such announcements and polls.

ユーザー アクティビティを変更するたびに  `startActivity()` を呼び出す必要があります。この関数の 1 回目の呼び出しは、新しいユーザー セッションを開始します。

この関数は、各アクティビティの  `onResume` コールバックで呼び出すのが最適です。

### ユーザーが現在のアクティビティを終了する

			EngagementAgent.getInstance(this).endActivity();

ユーザーが最後のアクティビティを終了する際には、 `endActivity()` を少なくとも 1 回呼び出す必要があります。これにより、ユーザーが現在休止状態にあり、セッション タイムアウトの期限が終了したタイミングでユーザー セッションを閉じる必要があることを Engagement SDK に通知します (セッション タイムアウトの期限が終了する前に  `startActivity()` を呼び出すと、そのセッションが再開されます)。

この関数は、各アクティビティの  `onPause` コールバックで呼び出すのが最適です。

##イベントのレポート作成

### セッション イベント

セッション イベントは通常、セッション中にユーザーが実行するアクションについてレポートを作成するために使用します。

**追加データなしの例:**

			public MyActivity extends EngagementActivity {
			   [...]
			   @Override
			   public boolean onPrepareOptionsMenu(Menu menu) {
			      getEngagementAgent().sendSessionEvent("menu_shown", null);
			   }
			   [...]
			}

**追加データありの例:**

			public MyActivity extends EngagementActivity {
			  [...]
			  @Override
			  public boolean onMenuItemSelected(int featureId, MenuItem item) {
			    Bundle extras = new Bundle();
			    extras.putInt("id", item.getItemId());
			    getEngagementAgent().sendSessionEvent("menu_selected", extras);
			  }
			  [...]
			}

### スタンドアロン イベント

セッション イベントとは逆に、スタンドアロン イベントはセッション外でも発生する場合があります。

**例:**

ブロードキャスト受信者がトリガーされたときに発生するイベントについてレポートを作成する場合は、次のとおりです。

			/** Triggered by Intent.ACTION_BATTERY_LOW */
			public BatteryLowReceiver extends BroadcastReceiver {
			  [...]
			  @Override
			  public void onReceive(Context context, Intent intent) {
			    EngagementAgent.getInstance(context).sendEvent("battery_low", null);
			  }
			  [...]
			}

##エラーのレポート作成

### セッション エラー

セッション エラーは通常、セッション中にユーザーに及ぼすエラーについてレポートを作成するために使用します。

**例:**

			/** The user has entered invalid data in a form */
			public MyActivity extends EngagementActivity {
			  [...]
			  public void onMyFormSubmitted(MyForm form) {
			    [...]
			    /* The user has entered an invalid email address */
			    getEngagementAgent().sendSessionError("sign_up_email", null);
			    [...]
			  }
			  [...]
			}

### スタンドアロン エラー

セッション エラーとは逆に、スタンドアロン エラーはセッション外でも発生する場合があります。

**例:**

次の例は、アプリケーション プロセスの実行中に携帯電話のメモリーが少なくなったタイミングで出力されるエラーについてレポートを作成する方法を示します。

			public MyApplication extends EngagementApplication {
			
			  @Override
			  protected void onApplicationProcessLowMemory() {
			    EngagementAgent.getInstance(this).sendError("low_memory", null);
			  }
			}

##ジョブのレポート作成

### 例

ログイン プロセスの経過時間についてレポートを作成する場合は、次のとおりです。
			
			[...]
			public void signIn(Context context, ...) {
			
			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has started */
			  engagementAgent.startJob("sign_in", null);
			
			  [... sign in ...]
			
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### ジョブ中のエラーに関するレポートを作成する

現在のユーザー セッションに関連付けられるエラーの代わりに、実行中のジョブに関連付けられるエラーであってもかまいません。

**例:**

ログイン プロセス中に発生したエラーについてレポートを作成する場合は、次のとおりです。

[...]
public void signIn(Context context, ...) {

			  /* We need an Android context to call the Engagement API, if you are extending Activity, Service, you can pass "this" */
			  EngagementAgent engagementAgent = EngagementAgent.getInstance(context);
			
			  /* Report sign in job has been started */
			  engagementAgent.startJob("sign_in", null);
			
			  /* Try to sign in */
			  while(true)
			    try {
			      trySignin();
			      break;
			    }
			    catch(Exception e) {
			      /* Report the error to Engagement */
			      engagementAgent.sendJobError("sign_in_error", "sign_in", null);
			
			      /* Retry after a moment */
			      sleep(2000);
			    }
			  [...]
			  /* Report sign in job is now ended */
			  engagementAgent.endJob("sign_in");
			}
			[...]

### ジョブ中のイベントに関するレポートを作成する

現在のユーザー セッションに関連付けられるイベントの代わりに、実行中のジョブに関連付けられるイベントであってもかまいません。

**例:**

ソーシャル ネットワークを活用しており、ジョブを使用してユーザーがサーバーに接続している合計時間についてレポートを作成するとします。ユーザーは別のアプリケーションを使用している場合や携帯電話がスリープ状態にある場合でもバックグラウンドで接続されていることがあるため、セッションはありません。

ユーザーは友人からメッセージを受け取ることができます。これはジョブ イベントです。
			
			[...]
			public void signin(Context context, ...) {
			  [...Sign in code...]
			  EngagementAgent.getInstance(context).startJob("connection", null);
			}
			[...]
			public void signout(Context context) {
			  [...Sign out code...]
			  EngagementAgent.getInstance(context).endJob("connection");
			}
			[...]
			public void onMessageReceived(Context context) {
			  [...Notify in status bar...]
			  EngagementAgent.getInstance(context).sendJobEvent("message_received", "connection", null);
			}
			[...]

##追加のパラメーター

任意のデータをイベント、エラー、アクティビティ、およびジョブにアタッチできます。

このデータは構造化可能で、Android の Bundle クラスを使用します (実際は Android Intent の追加パラメーターとして機能します)。Bundle にはアレイや他の Bundle インスタンスが含まれる場合があります。

> [AZURE.IMPORTANT] Parcelable パラメーターや Serializable パラメーターを追加する際には、 `toString()` メソッドが実装されていることを確認し、人が読める文字列を返すようにします。Serializable クラスにシリアライズできない永続的なフィールドが含まれている場合、`bundle.putSerializable("key",value);` を呼び出すと Android がクラッシュします。

> [AZURE.WARNING] 追加パラメーターのスパース アレイはサポートされないため、アレイとしてシリアライズされません。追加パラメーターで使用する前に標準アレイに変換してください。

### 例

			Bundle extras = new Bundle();
			extras.putString("video_id", 123);
			extras.putString("ref_click", "http://foobar.com/blog");
			EngagementAgent.getInstance(context).sendEvent("video_clicked", extras);

### 制限事項

#### 構成する

 `Bundle` 内の各キーは次の正規表現に一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

これは、1 文字以上の英字で始まり、後ろに英数字またはアンダースコア (\_) が続くキーを指します。

#### サイズ

追加分は 1 回の呼び出しにつき **1024** 文字に制限されます (Engagement サービスによって一度 JSON にエンコードされます)。

前の例では、サーバーに送信された JSON は 58 文字です。

			{"ref_click":"http:\/\/foobar.com\/blog","video_id":"123"}

##アプリケーション情報のレポート作成

追跡情報 (またはその他のアプリケーション固有の情報) に関するレポートは、 `sendAppInfo()` 関数を使用して手動で作成できます。

これらの情報はインクリメンタルに送信することはできないのでご注意ください。指定したキーの最新の値のみが指定のデバイスに格納されます。

イベントの追加分などでは、アプリケーション情報の抽出に Bundle クラスが使用され、アレイやサブバンドルが (JSON シリアル化を使用して) フラットな文字列として扱われるのでご注意ください。

### 例

ユーザーの性別や誕生日を送信するサンプル コードはこちらです。

			Bundle appInfo = new Bundle();
			appInfo.putString("status", "premium");
			appInfo.putString("expiration", "2016-12-07"); // December 7th 2016
			EngagementAgent.getInstance(context).sendAppInfo(appInfo);

### 制限事項

#### 構成する

 `Bundle` 内の各キーは次の正規表現に一致する必要があります。

`^[a-zA-Z][a-zA-Z_0-9]*`

これは、1 文字以上の英字で始まり、後ろに英数字またはアンダースコア (\_) が続くキーを指します。

#### サイズ

アプリケーション情報は 1 回の呼び出しにつき **1024** 文字に制限されます (Engagement サービスによって一度 JSON にエンコードされます)。

前の例では、サーバーに送信された JSON は 44 文字です。

			{"expiration":"2016-12-07","status":"premium"}

<!--HONumber=47-->
