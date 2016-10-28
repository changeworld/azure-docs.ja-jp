<properties
	pageTitle="Azure Mobile Engagement Android SDK の詳細な構成"
	description="Azure Mobile Engagement Android SDK の詳細な構成オプション (Android マニフェストを含む) について説明します"
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
	ms.date="08/02/2016"
	ms.author="piyushjo;ricksal" />

# Azure Mobile Engagement Android SDK の詳細な構成

> [AZURE.SELECTOR]
- [ユニバーサル Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-logging.md)

この手順では、Azure Mobile Engagement Android アプリ向けのさまざまな構成オプションを構成する方法について説明します。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/mobile-engagement-android-prereqs.md)]

## アクセス許可の要件
オプションの一部で特定のアクセス許可 (以下の一覧を参照) が必要です。また、特定の機能ではインラインである必要があります。次のアクセス許可をプロジェクトの AndroidManifest.xml の `<application>` タグの直前または直後に追加します。

アクセス許可のコードを次に示します。このコードに、以下の表から適切なアクセス許可を選択して入力します。

	<uses-permission android:name="android.permission.[specific permission]"/>


| アクセス許可 | 使用する場合 |
| ---------- | --------- |
| INTERNET | 必須。基本的なレポートを作成する場合 |
| ACCESS\_NETWORK\_STATE | 必須。基本的なレポートを作成する場合 |
| RECEIVE\_BOOT\_COMPLETED | 必須。デバイスの再起動後に通知センターを表示する場合 |
| WAKE\_LOCK | 推奨。WiFi を使っているときまたは画面がオフのときにデータの収集を有効にする場合 |
| VIBRATE | 省略可能。通知を受け取ったときに振動を有効にする場合 |
| DOWNLOAD\_WITHOUT\_NOTIFICATION | 省略可能。Android Big Picture Notification を有効にする場合 |
| WRITE\_EXTERNAL\_STORAGE | 省略可能。Android Big Picture Notification を有効にする場合 |
| ACCESS\_COARSE\_LOCATION | 省略可能。リアルタイムの位置報告を有効にする場合 |
| ACCESS\_FINE\_LOCATION | 省略可能。GPS ベースの位置報告を有効にする場合 |

Android M 以降では、[一部のアクセス許可が実行時に管理されます](mobile-engagement-android-location-reporting.md#Android-M-Permissions)。

``ACCESS_FINE_LOCATION`` を既に使用している場合は、``ACCESS_COARSE_LOCATION`` を一緒に使用する必要はありません。

## Android のマニフェスト構成オプション

### クラッシュ レポート

クラッシュ レポートを無効にするには、このコードを `<application>` タグと `</application>` タグの間に追加します。

	<meta-data android:name="engagement:reportCrash" android:value="false"/>

### バーストのしきい値

既定では、エンゲージメント サービスはログをリアルタイムで報告します。アプリケーションがログを送信する回数が非常に多い場合は、ログをバッファーに格納して、一定時間ごとにまとめて報告すること ("バースト モード" と呼ばれます) をお勧めします。これを行うには、次のコードを `<application>` タグと `</application>` タグの間に追加します。

	<meta-data android:name="engagement:burstThreshold" android:value="{interval between too bursts (in milliseconds)}"/>

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。バーストのしきい値は、30000 (30 秒) 以内にする必要があります。

### セッションのタイムアウト

 **[ホーム]** または **[戻る]** キーを押したり、電話をアイドル状態に設定したり、別のアプリケーションに移動したりすると、アクティビティを終了できます。既定では、セッションは、その最後のアクティビティの終了後、10 秒で終了します。これにより、ユーザーがアプリケーションを終了した後、非常に短時間で戻ってくる (画像の選択や通知の確認などを行うときに、このような行動が発生する可能性があります) たびにセッションが分割されないようになります。このパラメーターを変更することができます。これを行うには、次のコードを `<application>` タグと `</application>` タグの間に追加します。

	<meta-data android:name="engagement:sessionTimeout" android:value="{session timeout (in milliseconds)}"/>

## ログ レポートの無効化

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
-   `engagement:agent:settings:mode` キーを使って、共有設定ファイルのモードを定義します。`PreferenceActivity` と同じモードを使用してください。モードは数値として渡す必要があります。コード内で定数フラグの組み合わせを使っている場合は、合計値を確認します。

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

<!---HONumber=AcomDC_0817_2016-->