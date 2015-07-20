<properties 
	pageTitle="Azure Mobile Engagement Android SDK の統合" 
	description="Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Engagement を Android に統合する方法

> [AZURE.SELECTOR] 
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
- [iOS](mobile-engagement-ios-integrate-engagement.md) 
- [Android](mobile-engagement-android-integrate-engagement.md) 

この手順では、Engagement の分析機能と監視機能を Android アプリでアクティブ化するための最も簡単な方法について説明します。

> [AZURE.IMPORTANT]Android SDK の最小の API レベルは 10 以降 (Android 2.3.3 以降) である必要があります。
 
ユーザー、セッション、アクティビティ、クラッシュ、および技術に関するすべての統計を計算するために必要なログ レポートをアクティブ化するには、以下の手順で十分です。イベント、エラー、ジョブなどの他の統計情報を計算するのに必要なログのレポートについては、これらの統計がアプリケーションに依存しているので、エンゲージメント API を使用して手動で実行する必要があります ([Android で高度なモバイル エンゲージメント タグ付け API を使用する方法を参照](mobile-engagement-android-use-engagement-api.md))。

##Android プロジェクトに Engagement SDK とサービスを埋め込む

Android SDK を[こちら](http://go.microsoft.com/?linkid=9863935&clcid=0x409)からダウンロードします。`mobile-engagement-VERSION.jar` を選択して、Andorid プロジェクトの `libs` フォルダーに配置します (libs フォルダーが存在しない場合は作成します)。

> [AZURE.IMPORTANT]アプリケーション パッケージを ProGuard でビルドする場合は、いくつかのクラスを保持する必要があります。次の構成スニペットを使うことができます。
>
> 
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

ランチャー アクティビティ内で次のメソッドを呼び出すことで、Engagement の接続文字列を指定します。

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

-   次の Android のアクセス権限が不足している場合は (`<application>` タグの前に) 追加します。

			<uses-permission android:name="android.permission.INTERNET"/>
			<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>

-   ANDROID_ID からEngagement デバイス ID を生成できないデバイス モデルが少数あります (バグが多い、または使えない可能性があります)。その場合、SDK は、他の Engagement アプリケーションも同じデバイス識別子を共有できるように、ランダムなデバイス識別子を生成してデバイスの外部ストレージに保存しようとします (さらに、外部ストレージに問題が発生した場合でも、アプリケーション自体が常に同じデバイス識別子を使うことを保証するために共有設定としても保存されます)。このメカニズムを適切に機能させるには、次のアクセス権限が不足している場合は (`<application>` タグの前に) 追加する必要があります。

			<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>

-   次のセクションを (`<application>` タグと `</application>` タグの間に) 追加します。

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

-   `<Your application name>` を自分のアプリケーションの名前に変更します。

> [AZURE.TIP]`android:label` 属性を使って、エンド ユーザーの電話の "実行中のサービス" 画面に表示される Engagement サービスの名前を選択することができます。この属性には `"<Your application name>Service"` を設定することをお勧めします (例: `"AcmeFunGameService"`)。

`android:process` 属性を指定すると、Engagement サービスが独自のプロセスで実行されることが保証されます (Engagement をアプリケーションと同じプロセス内で実行すると、メイン/UI スレッドの反応が遅くなる可能性があります)。

> [AZURE.NOTE]`Application.onCreate()` に配置したすべてのコードとその他のアプリケーション コールバックは、Engagement サービスを含むすべてのアプリケーションのプロセスで実行されます。望ましくない副作用が発生する可能性があります (Engagement のプロセス内の不要なメモリとスレッドの割り当て、重複するブロードキャストの受信側またはサービスなど)。

`Application.onCreate()` をオーバーライドする場合は、次のコード スニペットを `Application.onCreate()` 関数の先頭に追加することをお勧めします。

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;
			
			   ... Your code...
			 }

同じことを `Application.onTerminate()`、`Application.onLowMemory()`、`Application.onConfigurationChanged(...)` に対しても行うことができます。

`EngagementApplication` を `Application` の代わりに拡張することもできます。コールバック `Application.onCreate()` は、プロセス チェックを実行し、現在のプロセスが Engagement サービスをホストするプロセスでない場合のみ `Application.onApplicationProcessCreate()` を呼び出します。

##基本的なレポート

### 推奨される方法: `Activity` クラスをオーバーロードします

ユーザー、セッション、アクティビティ、クラッシュ、および技術に関する統計を計算するために Engagement が必要とするすべてのログ レポートをアクティブ化するには、すべての `*Activity` サブ クラスが対応する `Engagement*Activity` クラスから継承されるようにする必要があります (例: レガシー アクティビティが `ListActivity` を拡張している場合は、`EngagementListActivity` も拡張します)。

**エンゲージメントを使用しない場合:**

			package com.company.myapp;
			
			import android.app.Activity;
			import android.os.Bundle;
			
			public class MyApp extends Activity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

**エンゲージメントを使用する場合:**

			package com.company.myapp;
			
			import com.microsoft.azure.engagement.activity.EngagementActivity;
			import android.os.Bundle;
			
			public class MyApp extends EngagementActivity
			{
			  @Override
			  public void onCreate(Bundle savedInstanceState)
			  {
			    super.onCreate(savedInstanceState);
			    setContentView(R.layout.main);
			  }
			}

> [AZURE.IMPORTANT]`EngagementListActivity` または `EngagementExpandableListActivity` を使う場合は、`requestWindowFeature(...);` に対するすべての呼び出しが `super.onCreate(...);` に対する呼び出しの前に行われることを確認します。それ以外の場合、クラッシュが発生します。

サブクラスの `FragmentActivity` と `MapActivity` が用意されていますが、**ProGuard** を使うアプリケーションで発生する問題を避けるために、`engagement.jar` には含まれていません。

これらのクラスは `src` フォルダーで見つけることができ、プロジェクトにコピーできます。クラスは **JavaDoc** にも用意されています。

### 別の方法: `startActivity()` と `endActivity()` を手動で呼び出す

`Activity` クラスをオーバーロードできない、またはそうしたくない場合は、代わりに `EngagementAgent` のメソッドを直接呼び出すことによって、アクティビティの開始と終了を実行できます。

> [AZURE.IMPORTANT]Android SDK は、アプリケーションが閉じられる場合でも `endActivity()` メソッドを呼び出すことはありません (Android では、アプリケーションは実際には閉じられることはありません)。このため、*すべて*のアクティビティの `onResume` コールバック内で `startActivity()` メソッドを呼び出し、*すべて*のアクティビティの `onPause()` コールバック内で `endActivity()` メソッドを呼び出すことを*強く*お勧めします。これは、セッションがリークしないことを保証する唯一の方法です。セッションがリークした場合、(セッションが保留中である限り、サービスが接続されたままになるため)、Engagement サービスが Engagement バックエンドから切断されることはありません。

たとえば次のようになります。

			public class MyActivity extends Some3rdPartyActivity
			{
			  @Override
			  protected void onResume()
			  {
			    super.onResume();
			    String activityNameOnEngagement = EngagementAgentUtils.buildEngagementActivityName(getClass()); // Uses short class name and removes "Activity" at the end.
			    EngagementAgent.getInstance(this).startActivity(this, activityNameOnEngagement, null);
			  }
			
			  @Override
			  protected void onPause()
			  {
			    super.onPause();
			    EngagementAgent.getInstance(this).endActivity();
			  }
			}

この例は、`EngagementActivity` クラスとそのバリアントによく似ています。ソース コードは `src` フォルダーに提供されています。

##テスト

エミュレーターまたはデバイスでモバイル アプリを実行し、このアプリによって [監視] タブにセッションが登録されることを確認して、統合を検証してください。

この後のセクションはオプションです。

##場所レポート

場所を報告する場合は、数行の構成行を (`<application>` タグと `</application>` タグの間に) 追加する必要があります。

### 遅延エリアの場所レポート

遅延エリアの場所レポートでは、デバイスに関連付けられた国、リージョン、地域をレポートできます。このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。デバイス エリアがセッションごとに最大 1 回レポートされます。GPS を使用しないため、このタイプの場所レポートでは、まったくとは言わないまでも、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計を計算します。また、リーチ キャンペーンの条件としても使用されます。デバイスに関する最新の既知のエリアは[デバイス API] を利用して取得できます。

レイジー エリア ロケーション レポートを有効にするには、以下を追加します。

			<meta-data android:name="engagement:locationReport:lazyArea" android:value="true"/>

さらに、次のアクセス権限が不足している場合は追加する必要があります。

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

### リアル タイム場所レポート

リアル タイム場所レポートでは、デバイスに関連付けられた緯度と経度をレポートできます。既定では、このタイプの場所レポートでは (セル ID または WIFI に基づいた) ネットワークの場所のみが使用されます。レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。

リアル タイム場所レポートは、統計の計算に使用することは*できません*。その唯一の目的は、リーチ キャンペーンでリアルタイム ジオフェンシング <Reach-Audience-geofencing> 基準の利用を可能にすることです。

リアルタイム ロケーション レポートを有効にするには、以下を追加します。

			<meta-data android:name="engagement:locationReport:realTime" android:value="true" />

さらに、次のアクセス権限が不足している場合は追加する必要があります。

			<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

#### GPS ベースのレポート

既定では、リアル タイム場所レポートでは、ネットワーク ベースの場所のみを使用します。GPS ベースの場所 (正確性が格段に優れています) を使用できるようにするには、以下を追加します。

			<meta-data android:name="engagement:locationReport:realTime:fine" android:value="true" />

さらに、次のアクセス権限が不足している場合は追加する必要があります。

			<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### 背景レポート

既定では、リアル タイム場所レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。バックグウランドでのレポートも有効にするには、以下を追加します。

			<meta-data android:name="engagement:locationReport:realTime:background" android:value="true" />

> [AZURE.NOTE]アプリケーションを背景で実行した場合、GPS を有効にしても、ネットワーク ベースの場所のみがレポートされます。

バックグラウンド ロケーション レポートは、ユーザーがデバイスを再起動すると停止します。以下を追加することで、起動時に自動的に再開されるように設定できます。

			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>

さらに、次のアクセス権限が不足している場合は追加する必要があります。

			<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

##詳細な報告

オプションとして、アプリケーション固有のイベント、エラー、ジョブをレポートする必要がある場合は、`EngagementAgent` クラスのメソッドを通じて Engagement API を使用します。このクラスのオブジェクトは、`EngagementAgent.getInstance()` 静的メソッドを呼び出すことで取得できます。

Engagement API では、Engagement の高度な機能をすべて利用できます。詳細は「Engagement API を Android で使用する方法」にあります(`EngagementAgent` クラスの技術文書にもあります)。

##高度な構成 (AndroidManifest.xml 内)

WIFI を使っているとき、または画面がオフのときにに統計がリアルタイムで送信されることを保証する場合は、次のオプションのアクセス権限を追加します。

			<uses-permission android:name="android.permission.WAKE_LOCK"/>

クラッシュ レポートを無効にする場合は、以下を (`<application>` タグと `</application>` タグの間) に追加します。

			<meta-data android:name="engagement:reportCrash" android:value="false"/>

既定では、エンゲージメント サービスはログをリアルタイムで報告します。アプリケーションがログを送信する回数が非常に多い場合は、ログをバッファーに格納して、一定時間ごとにまとめて報告することをお勧めします (これは "バースト モード" と呼ばれます) 。これを行うには、以下を (`<application>` タグと `</application>` タグの間に) 追加します。

			<meta-data android:name="engagement:burstThreshold" android:value="<interval between too bursts (in milliseconds)>"/>

バースト モードではわずかにバッテリーの寿命が延びますが、エンゲージメントの監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。

既定では、Engagement サービスは、ネットワークが使用可能になると、すぐにサーバーとの接続を確立します。この接続を延期する場合は、以下を (`<application>` タグと `</application>` タグの間) に追加します。

			<meta-data android:name="engagement:connection:delay" android:value="<delay (in milliseconds)>"/>

既定では、セッションは最後のアクティビティが終了した後、10 秒経過した時点で終了します (アクティビティの終了は、通常は、[ホーム] または [戻る] キーを押す、電話をアイドル状態に設定する、または他のアプリケーションに移動することで発生します)。これは、ユーザーがアプリケーションを終了した後、非常に短時間で戻ってくるたびにセッションが分割されることを避けるためです (このユーザーの行動は、画像の選択や通知の確認などを行うときに発生する可能性があります)。このパラメーターを変更することができます。これを行うには、以下を (`<application>` タグと `</application>` タグの間に) 追加します。

			<meta-data android:name="engagement:sessionTimeout" android:value="<session timeout (in milliseconds)>"/>

##ログ レポートの無効化

### メソッド呼び出しを使用した場合

Engagement でログの送信を停止したい場合は、以下を呼び出します。

			EngagementAgent.getInstance(context).setEnabled(false);

この呼び出しは永続的であり、共有設定ファイルを使います。

この関数を呼び出したときに Engagement がアクティブの場合、サービスが停止するまで 1 分ほどかかることがあります。ただし、次にアプリケーションが起動したときにサービスが起動することはありません。

ログ レポートは、同じ関数を `true` でもう一度呼び出すことによって有効にすることができます。

### 独自の `PreferenceActivity` での統合

上記の関数を呼び出す代わりに、その設定を既存の `PreferenceActivity` の中に直接統合することもできます。

`AndroidManifest.xml` ファイル内の設定ファイルを (目的のモードで) `application meta-data` と共に使うように Engagement を構成できます。

-   `engagement:agent:settings:name` キーを使って、共有設定ファイルの名前を定義します。
-   `engagement:agent:settings:mode` キーを使って、共有設定ファイルのモードを定義します。`PreferenceActivity` と同じモードを使う必要があります。モードは数値として渡す必要があります。コード内で定数フラグの組み合わせを使っている場合は、合計値を確認します。

Engagement では、この設定を管理するために設定ファイル内で常に `engagement:key` ブール キーを使います。

次の `AndroidManifest.xml` の例は、既定値を示しています。

			<application>
			    [...]
			    <meta-data
			      android:name="engagement:agent:settings:name"
			      android:value="engagement.agent" />
			    <meta-data
			      android:name="engagement:agent:settings:mode"
			      android:value="0" />

次のような `CheckBoxPreference` を設定レイアウトに追加できます。

			<CheckBoxPreference
			  android:key="engagement:enabled"
			  android:defaultValue="true"
			  android:title="Use Engagement"
			  android:summaryOn="Engagement is enabled."
			  android:summaryOff="Engagement is disabled." />

<!-- URLs. -->
[デバイス API]: http://go.microsoft.com/?linkid=9876094
 

<!---HONumber=July15_HO2-->