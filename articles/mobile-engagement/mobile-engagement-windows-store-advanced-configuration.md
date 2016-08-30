<properties
	pageTitle="Windows ユニバーサル アプリ Engagement SDK の詳細な構成"
	description="Windows ユニバーサル アプリを使用した Azure Mobile Engagement SDK の詳細構成オプション" 					
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2016"
	ms.author="piyushjo;ricksal" />

# Windows ユニバーサル アプリ Engagement SDK の詳細な構成

> [AZURE.SELECTOR]
- [ユニバーサル Windows](mobile-engagement-windows-store-advanced-configuration.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-advan.mdced-configuration.md)

この手順では、Azure Mobile Engagement Android アプリ向けのさまざまな構成オプションを構成する方法について説明します。

## 前提条件

[AZURE.INCLUDE [前提条件](../../includes/mobile-engagement-windows-store-prereqs.md)]

##詳細な構成

### 自動クラッシュ レポートを無効にする

Engagement の自動クラッシュ レポート機能を無効にできます。ハンドルされない例外が発生すると、Engagement は何も実行しません。

> [AZURE.WARNING] この機能を無効にすると、アプリでハンドルされない例外が発生しても、Engagement はクラッシュを報告せず、**さらに**セッションとジョブを終了しません。

自動クラッシュ レポートを無効にするには、宣言されている方法に基づいて構成をカスタマイズします。

#### `EngagementConfiguration.xml` ファイルを使用する場合

`<reportCrash>` タグと `</reportCrash>` タグの間で、report crash を `false` に設定します。

#### 実行時に `EngagementConfiguration` オブジェクトを使用する場合

EngagementConfiguration オブジェクトを使用して、report crash を false に設定します。

		/* Engagement configuration. */
		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

		/* Disable Engagement crash reporting. */
		engagementConfiguration.Agent.ReportCrash = false;

### リアルタイムのレポートを無効にする

既定では、エンゲージメント サービスはログをリアルタイムで報告します。アプリケーションがログを送信する回数が多い場合は、ログをバッファーに格納して、一定時間ごとにまとめて報告することをお勧めします。これは、"バースト モード" と呼ばれます。

そのためには、次のメソッドを呼び出します。

		EngagementAgent.Instance.SetBurstThreshold(int everyMs);

引数の値は**ミリ秒**単位です。リアルタイムのログ報告を再度有効にする場合は常に、パラメーターを指定しないか、値 0 を指定して、このメソッドを呼び出します。

バースト モードではわずかにバッテリーの寿命が延びますが、Engagement の監視に影響を与えます。すべてのセッションとジョブの実行時間は、バーストのしきい値に丸められます (つまり、バーストのしきい値よりも短いセッションとジョブは、認識されない場合があります)。バーストのしきい値は、30000 (30 秒) よりも長くしないことをお勧めします。保存されるログは 300 項目に制限されます。送信時間が長すぎる場合は、ログがいくつか失われる可能性があります。

> [AZURE.WARNING] バーストのしきい値を 1 秒よりも短くすることはできません。1 秒より短くすると、SDK はエラーのトレースを表示し、自動的に既定値 (0 秒) に再設定します。これにより、SDK はログをリアルタイムで報告するようになります。

[here]: http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]: http://docs.nuget.org/docs/start-here/overview

<!---HONumber=AcomDC_0817_2016-->