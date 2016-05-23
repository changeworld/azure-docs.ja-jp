<properties
	pageTitle="Android Azure Mobile Engagement SDK の詳細レポート オプション"
	description="Android Azure Mobile Engagement SDK の詳細レポート オプション"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="05/04/2016"
	ms.author="piyushjo;ricksal" />

# Android での Engagement によるレポート オプション

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-advanced-reporting.md)

このトピックでは、Android アプリケーションでの他のレポート シナリオについて説明します。これらのオプションを選択して、[作業開始](mobile-engagement-android-get-started.md)チュートリアルで作成されたアプリに適用できます。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/mobile-engagement-android-prereqs.md)]

完了したチュートリアルは意図的に直接的かつシンプルになっていましたが、選択できるオプションがいくつかあります。

## ProGuard でのビルド

アプリケーション パッケージを ProGuard でビルドする場合は、いくつかのクラスを保持する必要があります。次の構成スニペットを使うことができます。


			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			<methods>;
		 	}

## AndroidManifest.xml ファイルのタグ

AndroidManifest.xml ファイルのサービス タグでは、`android:label` 属性を使って、エンド ユーザーの電話の "実行中のサービス" 画面に表示される Engagement サービスの名前を選択できます。この属性には `"<Your application name>Service"` を設定することをお勧めします (例: `"AcmeFunGameService"`)。

`android:process` 属性を指定すると、Engagement サービスが独自のプロセスで実行されることが保証されます (Engagement をアプリケーションと同じプロセス内で実行すると、メイン/UI スレッドの反応が遅くなる可能性があります)。

## Application.onCreate() の使用

`Application.onCreate()` およびその他のアプリケーション コールバックに配置したすべてのコードは、Engagement サービスを含むすべてのアプリケーションのプロセスに対して実行されます。Engagement のプロセス内の不要なメモリの割り当てとスレッド、重複するブロードキャスト レシーバーやサービスなど、望ましくない副作用が発生する可能性があります。

`Application.onCreate()` をオーバーライドする場合は、次のコード スニペットを `Application.onCreate()` 関数の先頭に追加することをお勧めします。

			 public void onCreate()
			 {
			   if (EngagementAgentUtils.isInDedicatedEngagementProcess(this))
			     return;

			   ... Your code...
			 }

同じことを `Application.onTerminate()`、`Application.onLowMemory()`、`Application.onConfigurationChanged(...)` に対しても行うことができます。

`EngagementApplication` を `Application` の代わりに拡張することもできます。コールバック `Application.onCreate()` は、プロセス チェックを実行し、現在のプロセスが Engagement サービスをホストするプロセスでない場合のみ `Application.onApplicationProcessCreate()` を呼び出します。

## `Activity` クラスの変更

[作業開始チュートリアル](mobile-engagement-android-get-started.md)で必要だったのは、`*Activity` サブクラスが、対応する `Engagement*Activity` クラスから継承するようにすることだけでした。(たとえば、レガシー アクティビティが `ListActivity` を拡張した場合は、`EngagementListActivity` も拡張されるようにします)。

> [AZURE.IMPORTANT] `EngagementListActivity` または `EngagementExpandableListActivity` を使う場合は、`requestWindowFeature(...);` に対するすべての呼び出しが `super.onCreate(...);` に対する呼び出しの前に行われることを確認します。それ以外の場合、クラッシュが発生します。

これらのクラスは `src` フォルダーで見つけることができ、プロジェクトにコピーできます。クラスは **JavaDoc** にも用意されています。

## 別の方法: `startActivity()` と `endActivity()` を手動で呼び出す

`Activity` クラスをオーバーロードできない、またはそうしたくない場合は、代わりに `EngagementAgent` のメソッドを直接呼び出すことによって、アクティビティの開始と終了を実行できます。

> [AZURE.IMPORTANT] Android SDK は、アプリケーションが閉じられる場合でも `endActivity()` メソッドを呼び出すことはありません (Android では、アプリケーションは実際には閉じられることはありません)。このため、*すべて*のアクティビティの `onResume` コールバック内で `startActivity()` メソッドを呼び出し、*すべて*のアクティビティの `onPause()` コールバック内で `endActivity()` メソッドを呼び出すことを*強く*お勧めします。これは、セッションがリークしないことを保証する唯一の方法です。セッションがリークした場合、(セッションが保留中である限り、サービスが接続されたままになるため)、Engagement サービスが Engagement バックエンドから切断されることはありません。

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

<!---HONumber=AcomDC_0511_2016-->