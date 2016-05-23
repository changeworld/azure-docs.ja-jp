<properties
	pageTitle="Android Mobile Engagement の場所レポート"
	description="Azure Mobile Engagement の Android の場所レポート"
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
	ms.date="04/20/2016"
	ms.author="piyushjo;ricksal" />

# Android での Engagement の場所レポート

> [AZURE.SELECTOR]
- [Android](mobile-engagement-android-integrate-engagement.md)

このトピックでは、Android アプリケーションで Engegement の場所レポートを操作する方法について説明します。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/mobile-engagement-android-prereqs.md)]


## 場所レポート

場所を報告する場合は、数行の構成行を (`<application>` タグと `</application>` タグの間に) 追加する必要があります。

### 遅延エリアの場所レポート

大まかなエリアの位置報告では、デバイスに関連付けられた国、リージョン、地域をレポートできます。このタイプの場所レポートでは、セル ID または WIFI に基づいたネットワークの場所のみを使用します。デバイス エリアがセッションごとに最大 1 回レポートされます。GPS を使用しないため、このタイプの場所レポートでは、まったくとは言わないまでも、電力消費量にほとんど影響がありません。

報告されたエリアを基に、ユーザー、セッション、イベント、エラーに関する地理的な統計をコンピューティングします。また、リーチ キャンペーンの条件としても使用されます。

大まかなエリアの位置報告を有効にするには、この手順の前の構成を利用できます。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setLazyAreaLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

あるいは、アプリケーションで ``ACCESS_FINE_LOCATION`` を使用している場合、引き続きそれを利用できます。

### リアル タイム場所レポート

リアル タイム場所レポートでは、デバイスに関連付けられた緯度と経度をレポートできます。既定では、このタイプの場所レポートでは (セル ID または WIFI に基づいた) ネットワークの場所のみが使用されます。レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。

リアル タイム場所レポートは、統計のコンピューティングに使用することは*できません*。その唯一の目的は、リーチ キャンペーンでリアルタイム ジオフェンシング <Reach-Audience-geofencing> 基準の利用を可能にすることです。

リアルタイム場所レポートを有効にするには、ランチャー アクティビティの Engegement 接続文字列を設定した場所にコード行を追加します。結果は次のようになります。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

		<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>

あるいは、アプリケーションで ``ACCESS_FINE_LOCATION`` を使用している場合、引き続きそれを利用できます。

#### GPS ベースのレポート

既定では、リアル タイム場所レポートでは、ネットワーク ベースの場所のみを使用します。GPS ベースの場所 (正確性が格段に優れています) を使用できるようにするには、構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setFineRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

さらに、次のアクセス権限が不足している場合は追加する必要があります。

		<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>

#### 背景レポート

既定では、リアル タイム場所レポートは、アプリケーションを前景で実行した場合 (セッション中) にのみアクティブになります。バックグラウンドでのレポートも有効にするには、この構成オブジェクトを使用します。

    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
    engagementConfiguration.setRealtimeLocationReport(true);
    engagementConfiguration.setBackgroundRealtimeLocationReport(true);
    EngagementAgent.getInstance(this).init(engagementConfiguration);

> [AZURE.NOTE] アプリケーションを背景で実行した場合、GPS を有効にしても、ネットワーク ベースの場所のみがレポートされます。

バックグラウンド ロケーション レポートは、ユーザーがデバイスを再起動すると停止します。以下を追加することで、起動時に自動的に再開されるように設定できます。

		<receiver android:name="com.microsoft.azure.engagement.EngagementLocationBootReceiver"
			   android:exported="false">
			<intent-filter>
			    <action android:name="android.intent.action.BOOT_COMPLETED" />
			</intent-filter>
		</receiver>

さらに、次のアクセス権限が不足している場合は追加する必要があります。

		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />

### Android M アクセス許可

Android M より、一部のアクセス許可が実行時に管理され、ユーザーの承認を必要とします。

Android API レベル 23 が対象の場合、新しいアプリのインストールでは既定で実行時のアクセス許可がオフになります。それ以外の場合、既定でオンになります。

デバイスの設定メニューからアクセス許可の有効/無効を設定できます。システム メニューでアクセス許可をオフにした場合、アプリケーションのバックグラウンド プロセスが停止します。これはシステム動作であり、バックグラウンドでプッシュを受信する機能には影響を与えません。

Mobile Engagement の場所レポートの場合、次のアクセス許可で実行時に承認が必要になります。

- `ACCESS_COARSE_LOCATION`
- `ACCESS_FINE_LOCATION`

標準のシステム ダイアログを利用してユーザーにアクセス許可を要求する必要があります。ユーザーが承認した場合、その変更をリアルタイムで考慮するように ``EngagementAgent`` に通知する必要があります (認識されない場合、ユーザーが次回アプリケーションを起動したときに変更が処理されます)。

ここにコード サンプルがあります。これはアクセス許可を要求し、``EngagementAgent`` が肯定された場合、結果を転送するアプリケーション アクティビティで使用されます。

    @Override
    public void onCreate(Bundle savedInstanceState)
    {
      /* Other code... */

      /* Request permissions */
      requestPermissions();
    }

    @TargetApi(Build.VERSION_CODES.M)
    private void requestPermissions()
    {
      /* Avoid crashing if not on Android M */
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M)
      {
        /*
         * Request location permission, but this won't explain why it is needed to the user.
         * The standard Android documentation explains with more details how to display a rationale activity to explain the user why the permission is needed in your application.
         * Putting COARSE vs FINE has no impact here, they are part of the same group for runtime permission management.
         */
        if (checkSelfPermission(android.Manifest.permission.ACCESS_FINE_LOCATION) != PackageManager.PERMISSION_GRANTED)
          requestPermissions(new String[] { android.Manifest.permission.ACCESS_FINE_LOCATION }, 0);

      }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode, String[] permissions, int[] grantResults)
    {
      /* Only a positive location permission update requires engagement agent refresh, hence the request code matching from above function */
      if (requestCode == 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED)
        getEngagementAgent().refreshPermissions();
    }

<!---HONumber=AcomDC_0511_2016-->