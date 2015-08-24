<properties 
	pageTitle="Azure Mobile Engagement Android SDK の統合" 
	description="Android SDK for Azure Mobile Engagement の最新の更新情報と更新手順について"
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="piyushjo" />


#Android SDK for Azure Mobile Engagement

Azure Mobile Engagement を Android アプリに統合する方法の詳細について紹介します。まず試してみる場合は、「[15 分間チュートリアル](mobile-engagement-android-get-started.md)」をご覧ください

[SDK コンテンツ](mobile-engagement-android-sdk-content.md)について表示するにはここをクリックします。

##統合手順
1. ここから開始: [Engagement を Android アプリに統合する方法](mobile-engagement-android-integrate-engagement.md)

2. 通知: [リーチ (通知) を Android アプリに統合する方法](mobile-engagement-android-integrate-engagement-reach.md)
	1. Google Cloud Messaging (GCM): [GCM を Mobile Engagement に統合する方法](mobile-engagement-android-gcm-integrate.md)
	2. Amazon Device Messaging (ADM): [ADM を Mobile Engagement に統合する方法](mobile-engagement-android-adm-integrate.md)

3. タグ付けプランの実装: [Android アプリで高度なモバイル エンゲージメントのタグ付け API を使用する方法](mobile-engagement-android-use-engagement-api.md)


##リリース ノート

###4\.0.0 (07/06/2015)

-   分析を行い、より確実にプッシュするように内部プロトコルを変更しました。
-   ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。
-   大きい画像による通知を修正しました。これらの画像は、プッシュ後の 10 秒間のみ表示されていました。
-   Web アナウンス内で、既定のアクション URL が設定されたリンクをクリックした場合の動作を修正しました。
-   ローカル ストレージの管理に関連してまれに起きていたクラッシュを修正しました。
-   動的構成文字列の管理を修正しました。
-   EULA を更新しました。

以前のバージョンについては、「[完全リリース ノート](mobile-engagement-android-release-notes.md)」をご覧ください。

##アップグレードの手順

既に古いバージョンの SDK をアプリケーションに統合している場合は、SDK をアップグレードする際に次の点を考慮する必要があります。

SDK のいくつかのバージョンがない場合は、次の手順に従う必要があります。完全な「[アップグレード手順](mobile-engagement-android-upgrade-procedure.md)」をご覧ください。たとえば、1.4.0 から 1.6.0 に移行する場合、まず「1.4.0から 1.5.0」への手順を実行してから「1.5.0 から 1.6.0」への手順を実行する必要があります。

どのバージョンからアップグレードする場合でも、`mobile-engagement-VERSION.jar` を新しいバージョンのものに置き換える必要があります。

###3\.0.0 から 4.0.0 に移行

#### ネイティブ プッシュ通知

ネイティブ プッシュ通知 (GCM/ADM) がアプリ内通知にも使用されるようになったので、すべての種類のプッシュ キャンペーンについてネイティブのプッシュの資格情報を設定する必要があります。

設定が完了していない場合は、[こちらの手順](mobile-engagement-android-integrate-engagement-reach.md#native-push)に従ってください。

#### AndroidManifest.xml

``AndroidManifest.xml`` の Reach 統合が変更されました。

こちらに置き換えてください。

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
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

別

    <receiver
      android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver"
      android:exported="false">
      <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
        <action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
      </intent-filter>
    </receiver>
    <receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
      <intent-filter>
        <action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
      </intent-filter>
    </receiver>

アナウンス (テキスト/Web コンテンツを含む) またはポーリングをクリックすると、場合によって読み込み画面が表示されるようになりました。4.0.0 でこれらのキャンペーンを動作させるには、次を追加する必要があります。

    <activity
      android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity"
      android:theme="@android:style/Theme.Dialog">
      <intent-filter>
        <action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
        <category android:name="android.intent.category.DEFAULT"/>
      </intent-filter>
    </activity>

#### リソース

プロジェクトに新しい `res/layout/engagement_loading.xml` ファイルが埋め込まれました。

<!---HONumber=August15_HO7-->