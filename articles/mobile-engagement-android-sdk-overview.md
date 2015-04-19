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


#Android SDK for Azure Mobile Engagement v3.0.0
Azure Mobile Engagement を Android アプリに統合する方法の詳細について紹介します。まずはマイクロソフトが提供する [15 分のチュートリアル]をご覧ください(mobile-engagement-android-get-started.md)。

クリックして [SDK コンテンツ]を表示(mobile-engagement-android-sdk-content.md)

##統合手順
1. はじめに:[Engagement を Android に統合する方法](mobile-engagement-android-integrate-engagement.md)

2. 通知:[Engagement Reach を Android に統合する方法](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM):[GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM):[ADM を Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)

3. タグ計画の実装:[Engagement API を Android で使用する方法](mobile-engagement-android-use-engagement-api.md)


##リリース ノート

###3.0.0 (2015 年 2 月 17 日)

-   Azure Mobile Engagement の初版リリース
-   appId 構成が接続文字列構成に置き換わりました。
-   任意の XMPP エンティティから任意の XMPP メッセージを送受信する API を削除しました。
-   デバイス間でメッセージを送受信する API を削除しました。
-   セキュリティが強化されました。
-   Google Play と SmartAd の追跡機能が削除されました。

これ以前のバージョンについては、[リリース ノート全文]をご覧ください。(mobile-engagement-android-release-notes.md)

##アップグレード手順

アプリケーションにマイクロソフトの古いバージョンの SDK が既に統合されている場合は、次の点を考慮して SDK をアップグレードする必要があります。

SDK のバージョンを飛ばしてアップグレードするには、複数の手順を実行する必要がある場合があります。詳細は「[アップグレード手順]」のページをご覧ください。(mobile-engagement-android-upgrade-procedure.md)。たとえば、バージョン 1.4.0 から 1.6.0 に移行するには、まず「1.4.0 から 1.5.0」の手順を実行し、その後「1.5.0 から 1.6.0」の手順を実行する必要があります。

どのバージョンからアップグレードする場合でも、`mobile-engagement-VERSION.jar` をすべて新しいバージョンのものに置き換える必要があります。

###2.4.0 から 3.0.0

次の手順は、Capptain SAS が提供する Capptain サービスの SDK 統合を Azure Mobile Engagement で実行されるアプリに移行する方法について説明します。 

>[AZURE.IMPORTANT] Capptain と Mobile Engagement は同じサービスではないため、以下の手順の説明はクライアント アプリを移行する方法にのみ焦点を当てます。アプリの SDK を移行しても、Capptain サーバーのデータは Mobile Engagement サーバーには移行されません。

#### JAR ファイル

`libs` フォルダーの `capptain.jar` を `mobile-engagement-VERSION.jar` に置き換えます。

#### リソース ファイル

提供されるすべてのリソース ファイル (`capptain_` で始まるファイル) を新しいファイル (`engagement_` で始まるファイル) に置き換える必要があります。

これらのファイルがカスタマイズされている場合、新しいファイルにもそのカスタマイズを再適用する必要があります。**また、リソース ファイル内すべての識別子の名前を変更する必要があります**。

#### アプリケーション ID

Engagement では、接続文字列を使用してアプリケーション ID などの SDK の識別子を構成します。

ランチャー アクティビティで、`EngagementAgent.init` メソッドを次のように実行します。

			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
			EngagementAgent.getInstance(this).init(engagementConfiguration);

アプリケーションの接続文字列が Azure ポータルに表示されます。

`CapptainAgent.configure` メソッドは `EngagementAgent.init` に置き換えられるため、このメソッドに対する呼び出しをすべて削除してください。

`appId` は `AndroidManifest.xml` を使用して構成できなくなりました。

まだ存在する場合は、`AndroidManifest.xml` からこのセクションを削除してください。

			<meta-data android:name="capptain:appId" android:value="<YOUR_APPID>"/>

#### Java API

SDK の Java クラスに対する呼び出しの名前をすべて変更する必要があります。たとえば、 `CapptainAgent.getInstance(this)` は  `EngagementAgent.getInstance(this)`、 `extends CapptainActivity` は  `extends EngagementActivity` などのように名前を変更します。

既定のエージェントの設定ファイルに統合されている場合、既定のファイル名は  `engagement.agent`、キーは  `engagement:agent` になります。

Web 通知を作成する場合、Javascript バインダーは  `engagementReachContent` になります。

#### AndroidManifest.xml

多数の変更が発生し、サービスが共有されなくなったため、多くの受信者をエクスポートできなくなりました。

サービスの宣言が簡単になり、インテント フィルターとその内部のすべてのメタデータが削除され、 `exportable=false` が追加されました。

また、すべての名前に engagement が追加されました。

次のようになります。

			<service
			  android:name="com.microsoft.azure.engagement.service.EngagementService"
			  android:exported="false"
			  android:label="<Your application name>Service"
			  android:process=":Engagement"/>

テスト ログを有効にする場合は次のようにします。メタデータはアプリケーション タグに移動され、名前が変更されています。

			<application>
			
			  <meta-data android:name="engagement:log:test" android:value="true" />
			
			  <service/>
			
			</application>

その他すべてのメタデータの名前も変更されています。一覧は次のとおりです (使用するメタデータの名前のみ変更します)。

			<meta-data
			  android:name="engagement:reportCrash"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:sessionTimeout"
			  android:value="10000"/>
			<meta-data
			  android:name="engagement:burstThreshold"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:connection:delay"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:locationReport:lazyArea"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:background"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:locationReport:realTime:fine"
			  android:value="false"/>
			<meta-data
			  android:name="engagement:agent:settings:name"
			  android:value="engagement.agent"/>
			<meta-data
			  android:name="engagement:agent:settings:mode"
			  android:value="0"/>
			<meta-data
			  android:name="engagement:gcm:sender"
			  android:value="<YOUR_PROJECT_NUMBER>\n"/>
			<meta-data
			  android:name="engagement:adm:register"
			  android:value="true"/>
			<meta-data
			  android:name="engagement:reach:notification:icon"
			  android:value="<DRAWABLE_NAME_WITHOUT_EXTENSION>"/>
			
			<activity android:name="SomeActivityWithoutReachOverlay">
			  <meta-data
			    android:name="engagement:notification:overlay"
			    android:value="false"/>
			</activity>

SDK から Google Play と SmartAd の追跡機能が削除されたため、この部分は置き換えではなく削除する必要があります。

			<meta-data 
				android:name="capptain:track:installReferrerForwardList"
				android:value="com.class1,com.class2"/>
			<meta-data
				android:name="capptain:track:adservers"
				android:value="smartad" />

Reach のアクティビティは次のように宣言します。

			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/plain"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			    <data android:mimeType="text/html"/>
			  </intent-filter>
			</activity>
			<activity
			  android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity"
			  android:theme="@android:style/Theme.Light">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
			    <category android:name="android.intent.category.DEFAULT"/>
			  </intent-filter>
			</activity>
			
Reach のアクティビティがカスタマイズされている場合は、インテント アクションを  `com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT` または  `com.microsoft.azure.engagement.reach.intent.action.POLL` のいずれかに一致するように変更します。

ブロードキャスト レシーバーの名前が変更され、 `exported=false` が追加されました。レシーバーの新しい詳細の一覧は次のとおりです (使用する受信者の名前のみ変更します)。

			<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
			    <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver"
			  android:permission="com.google.android.c2dm.permission.SEND">
			  <intent-filter>
			    <action android:name="com.google.android.c2dm.intent.REGISTRATION"/>
			    <action android:name="com.google.android.c2dm.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
			  android:permission="com.amazon.device.messaging.permission.SEND">
			  <intent-filter>
			    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
			    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
			    <category android:name="<your_package_name>"/>
			  </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.reach.EngagementReachDataPushReceiver>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.DATA_PUSH" />
			  </intent-filter>
			</receiver>
			
			<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			   <intent-filter>
			      <action android:name="android.intent.action.BOOT_COMPLETED" />
			   </intent-filter>
			</receiver>
			
			<receiver android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementConnectionReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.intent.action.CONNECTED"/>
			    <action android:name="com.microsoft.azure.engagement.intent.action.DISCONNECTED"/>
			  </intent-filter>
			</receiver>
			
			<receiver
			  android:name="<your_sub_class_of_com.microsoft.azure.engagement.EngagementMessageReceiver.java>"
			  android:exported="false">
			  <intent-filter>
			    <action android:name="com.microsoft.azure.engagement.reach.intent.action.MESSAGE"/>
			  </intent-filter>
			</receiver>

追跡のレシーバーは削除されたため、このセクションは削除する必要があります。

		  <receiver android:name="com.ubikod.capptain.android.sdk.track.CapptainTrackReceiver">
		    <intent-filter>
		      <action android:name="com.ubikod.capptain.intent.action.APPID_GOT" />
		      <!-- possibly <action android:name="com.android.vending.INSTALL_REFERRER" /> -->
		    </intent-filter>
		  </receiver>

ブロードキャスト レシーバーを実装する宣言 **EngagementMessageReceiver** は  `AndroidManifest.xml` では変更されているのでご注意ください。これは、任意の XMPP エンティティから任意の XMPP メッセージを送受信する API と、デバイス間でメッセージを送受信する API が削除されているためです。このため、**EngagementMessageReceiver** の実装から次のコールバックを削除する必要があります。

			protected void onDeviceMessageReceived(android.content.Context context, java.lang.String deviceId, java.lang.String payload)

と

			protected void onXMPPMessageReceived(android.content.Context context, android.os.Bundle message)

その後、**EngagementAgent** に対する呼び出しを削除します。

			sendMessageToDevice(java.lang.String deviceId, java.lang.String payload, java.lang.String packageName)

と

			sendXMPPMessage(android.os.Bundle msg)

#### Proguard

Proguard の構成はブランド変更の影響を受けるため、ルールは次のようになります。

			-dontwarn android.**
			-keep class android.support.v4.** { *; }
			
			-keep public class * extends android.os.IInterface
			-keep class com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity$EngagementReachContentJS {
			  <methods>;
			}

<!--HONumber=47-->
