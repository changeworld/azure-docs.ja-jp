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


#ADM を Engagement に統合する方法

> [AZURE.IMPORTANT]このガイドの手順を実行する前に、「Engagement を Android に統合する方法」のドキュメントの統合手順を実行する必要があります。
>
> このドキュメントは、いつでも実施できるキャンペーンをサポートする Reach モジュールを統合した場合にのみ役立ちます。アプリケーションに Reach キャンペーンを統合するには、Engagement Reach を Android に統合する方法に関するドキュメントを先にお読みください。

##はじめに

ADM を統合すると、アプリケーションが実行されていないときもアプリケーションをプッシュすることができます。

キャンペーン データが ADM によって実際に送信されることはありません。それはアプリケーションに Engagement プッシュをフェッチするように通知する単なるバックグラウンド シグナルです。ADM プッシュの受信中にアプリケーションが実行されていない場合は、プッシュをフェッチするために Engagement サーバーへの接続がトリガーされ、Engagement との接続は、ユーザーがプッシュに応答してアプリケーションを起動した場合に備えて約 1 分間アクティブのままになります。

> [AZURE.IMPORTANT]Amazon Device Messaging によってサポートされるのは Android 4.0.3 以降を実行している Amazon Kindle デバイスのみですが、このコードはその他のデバイスでも安全に統合することができます。アプリケーションが ADM によって起動できない場合、Engagement 通知はアプリケーションの次回の起動時に受信されます。

##ADM にサインアップする

まだ実行してない場合は、ADM を Amazon アカウントで有効にする必要があります。

この手順の詳細は [<https://developer.amazon.com/sdk/adm/credentials.html>] にあります。

手順を完了すると、以下が取得されます。

-   Engagement がデバイスにプッシュできるようにする OAuth 資格情報 (クライアント ID とクライアント シークレット)。
-   アプリケーションに統合する必要がある API キー。

##SDK の統合

### デバイス登録の管理

各デバイスは ADM サーバーに登録コマンドを送信する必要があります。そうしないと、デバイスに接続できません。

[ADM クライアント ライブラリ]を既に使用し、[統合 ADM] が既にある場合、android sdk-adm-receive に直接移動できます。

ADM をまだ統合してしない場合は、Engagement では簡単な方法でアプリケーション内でそれを有効にできます。

`AndroidManifest.xml` ファイルを編集します。

-   Amazon 名前空間を追加します。ファイルは次のように始まります。

		<?xml version="1.0" encoding="utf-8"?>
		<manifest xmlns:android="http://schemas.android.com/apk/res/android"
		          xmlns:amazon="http://schemas.amazon.com/apk/res/android"

-   `<application/>` タグの中にこのセクションを追加します。

		<amazon:enable-feature
		   android:name="com.amazon.device.messaging"
		   android:required="false"/>
		
		<meta-data android:name="engagement:adm:register" android:value="true" />

-   amazon タグを追加した後、プロジェクトのビルド ターゲットが Android 2.1 より前の場合はビルド エラーが発生することがあります。**Android 2.1+** ビルド ターゲットを使用する必要があります (`minSdkVersion` は問題なく 4 に設定することができます)。
-   [この手順]に従い、ADM API キーをアセットとして統合します。

その後、次のセクションの指示に従います。

### 登録 ID を Engagement Push サービスに伝達し、通知を受信する

デバイスの登録 ID を Engagement Push サービスに伝達し、通知を受信するには、次を `AndroidManifest.xml` ファイルの `<application/>` タグ内に追加します (Engagement のない ADM を使用している場合でも)。

		<receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMEnabler"
		  android:exported="false">
		  <intent-filter>
		    <action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT"/>
		  </intent-filter>
		</receiver>
		
		 <receiver android:name="com.microsoft.azure.engagement.adm.EngagementADMReceiver"
		   android:permission="com.amazon.device.messaging.permission.SEND">
		  <intent-filter>
		    <action android:name="com.amazon.device.messaging.intent.REGISTRATION"/>
		    <action android:name="com.amazon.device.messaging.intent.RECEIVE"/>
		    <category android:name="<your_package_name>"/>
		  </intent-filter>
		</receiver>   

`AndroidManifest.xml` に次のアクセス許可があることを確認します (`</application>` タグの前にあります)。

		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE"/>
		<uses-permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE"/>
		<permission android:name="<your_package_name>.permission.RECEIVE_ADM_MESSAGE" android:protectionLevel="signature"/>

##Engagement OAuth 資格情報を与える

OAuth Credentials (クライアント ID とクライアントシークレット) を $/#application/YOUR_APPID/native-push で送信します。

これで、Reach 通知とポーリングを作成するときに [いつでも] を選択することができます。


[<https://developer.amazon.com/sdk/adm/credentials.html>]: https://developer.amazon.com/sdk/adm/credentials.html
[ADM クライアント ライブラリ]: https://developer.amazon.com/sdk/adm/setup.html
[統合 ADM]: https://developer.amazon.com/sdk/adm/integrating-app.html
[この手順]: https://developer.amazon.com/sdk/adm/integrating-app.html#Asset
 

<!---HONumber=July15_HO2-->