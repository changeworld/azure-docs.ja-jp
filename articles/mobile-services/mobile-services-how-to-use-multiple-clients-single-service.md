<properties
	pageTitle="1 つのモバイル サービス バックエンドで複数のクライアントを使用する方法 | Microsoft Azure"
	description="Windows ストアや Windows Phone など、さまざまなモバイル プラットフォームを対象とする複数のクライアント アプリから単一の Mobile Services バックエンドを使用する方法について説明します。"
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor="mollybos"/>
<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/16/2015"
	ms.author="glenga"/>

# シングル モバイル サービスから複数のデバイス プラットフォームをサポートする

モバイル アプリ開発で Azure モバイル サービスを使用する主なメリットの 1 つに、複数のクライアント プラットフォームでアプリをサポートするシングル バックエンド サービスの使用があります。モバイル サービスでは、すべての主要デバイス プラットフォームに対してネイティブ クライアント ライブラリを提供するため、シングル バックエンド サービスとクロスプラットフォーム開発者ツールを使用して簡単にアプリが開発できます。このトピックではシングル モバイル サービス バックエンドを使用しながら複数のクライアント プラットフォームでアプリを実行させるための考慮事項について説明します。

##<a id="push"></a>クロスプラットフォーム プッシュ通知

モバイル サービスでは、すべての主要デバイス プラットフォームのクライアント アプリへのプッシュ通知の送信に Azure 通信ハブを使用しています。通知ハブによって提供される一貫性のある統一されたインフラストラクチャで、デバイスの登録を作成、管理したり、クロスプラットフォーム プッシュ通知を送信したりできます。通知ハブでは、次のプラットフォーム固有の通知サービスを使用してプッシュ通知の送信をサポートしています。

+ iOS アプリ向け Apple Push Notification Service (APNS)
+ Android アプリ向け Google Cloud Messaging (GCM) サービス
+ Windows ストア、Windows Phone 8.1 ストア、ユニバーサル Windows アプリ向け Windows Notification Service (WNS)
+ Windows Phone Silverlight アプリ向け Microsoft Push Notification Service (MPNS)

>[AZURE.NOTE]Notification Hubs では、WNS を使用した Windows Phone Silverlight 8.1 アプリへのプッシュ通知の送信は現在サポートしていません。Silverlight、Windows Phone 8.0 および 7.0 アプリへの通知の送信には MPNS を使用する必要があります。

詳細については、「[Azure 通知ハブ]」を参照してください。

クライアントの登録は、プラットフォーム固有のモバイル サービス クライアント ライブラリで register 関数か、モバイル サービス REST API を使用して作成されます。通知ハブでは次の 2 種類のデバイス登録をサポートします。

+ **ネイティブ登録**<br/>ネイティブ登録はプラットフォーム固有のプッシュ通知サービスに合わせて調整されます。ネイティブ登録を使用して登録したデバイスに通知を送信する場合、モバイル サービスでプラットフォーム固有の API を呼び出す必要があります。複数のプラットフォーム上のデバイスに通知を送信するには、複数のプラットフォーム固有の呼び出しが必要になります。

+ **テンプレート登録**<br/>通知ハブではプラットフォーム固有のテンプレート登録もサポートします。テンプレート登録を使用すると、1 回の API 呼び出しを使用して登録したデバイス上で稼働するアプリに通知を送信できます。詳細については、「[ユーザーへのクロスプラットフォーム通知の送信]」を参照してください。

>[AZURE.NOTE]デバイスの登録がされていないネイティブ デバイス プラットフォームにメッセージの送信を試みると、エラーが発生します。このエラーはテンプレート通知を送信する場合には発生しません。

次のセクションのテーブルはクライアント固有のチュートリアルにリンクしており、.NET と JavaScript バックエンド両方のモバイル サービスからプッシュ通知を実装する方法を示しています。

###.NET バックエンド

.NET バックエンド モバイル サービスでは、[ApiServices.Push] プロパティから取得した [PushClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx) オブジェクトで [SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx) メソッドを呼び出して通知を送信します。送信したプッシュ通知 (ネイティブまたはテンプレート) は次のテーブルで示すように、[SendAsync](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) メソッドに渡された特定の [IPushMessage] 派生オブジェクトによって異なります。

|プラットフォーム |[APNS](mobile-services-dotnet-backend-ios-get-started-push.md)|[GCM](mobile-services-dotnet-backend-android-get-started-push.md) |[WNS](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-dotnet-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|ネイティブ|[ApplePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx) |[GooglePushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx) |[WindowsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

次のコードは .NET バックエンド サービスからすべての iOS および Windows ストア デバイス登録にプッシュ通知を送信します。

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +
                         @"<text id=""1"">" + item.Text + @"</text>" +
                         @"</binding></visual></toast>";

	// Send push notifications to all registered iOS and Windows Store devices.
    await Services.Push.SendAsync(apnsMessage);
	await Services.Push.SendAsync(wnsMessage);

その他のネイティブ クライアント プラットフォームにプッシュ通知を送信する方法の例は、上記のテーブルのヘッダーにあるプラットフォームのリンクをクリックしてください。

ネイティブ クライアント登録ではなく、テンプレート クライアント登録を使用する場合、次のように [SendAsync] への 1 回の呼び出しで同じ通知を送信し、[TemplatePushMessage] オブジェクトを提供します。

	// Create a new template message and add the 'message' parameter.
	var templatePayload = new TemplatePushMessage();
    templatePayload.Add("message", item.Text);

	// Send a push notification to all template registrations.
    await Services.Push.SendAsync(templatePayload);

###JavaScript バックエンド

JavaScript バックエンド モバイル サービスでは、次のテーブルで示すようにグローバル **push オブジェクト**から取得したプラットフォーム固有のオブジェクト上で [send] メソッドを呼び出して通知を送信します。

|プラットフォーム |[APNS](mobile-services-javascript-backend-ios-get-started-push.md)|[GCM](mobile-services-javascript-backend-android-get-started-push.md) |[WNS](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) |[MPNS](mobile-services-javascript-backend-windows-phone-get-started-push.md)|
|-----|-----|----|----|-----|
|ネイティブ|[apns オブジェクト](http://msdn.microsoft.com/library/azure/jj839711.aspx) |[gcm オブジェクト](http://msdn.microsoft.com/library/azure/dn126137.aspx) |[wns オブジェクト](http://msdn.microsoft.com/library/azure/jj860484.aspx) | [mpns オブジェクト](http://msdn.microsoft.com/library/azure/jj871025.aspx) |

次のコードはすべての Android および Windows Phone 登録にプッシュ通知を送信します。

	// Define a push notification for GCM.
	var gcmPayload =
    '{"data":{"message" : item.text }}';

	// Define the payload for a Windows Phone toast notification.
	var mpnsPayload = '<?xml version="1.0" encoding="utf-8"?>' +
    '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
    '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text +
    '</wp:Text2></wp:Toast></wp:Notification>';

	// Send push notifications to all registered Android and Windows Phone 8.0 devices.
	push.mpns.send(null, mpnsPayload, 'toast', 22, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });
    push.gcm.send(null, gcmPayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

その他のネイティブ クライアント プラットフォームにプッシュ通知を送信する方法の例は、上記のテーブルのヘッダーにあるプラットフォームのリンクをクリックしてください。

ネイティブ クライアント登録ではなく、テンプレート クライアント登録を使用する場合、次のようにグローバル **push オブジェクト**上で [send] 関数を 1 回の呼び出して同じ通知を送信し、テンプレート メッセージ ペイロードを提供します。

	// Create a new template message with the 'message' parameter.
	var templatePayload = { "message": item.text };

	// Send a push notification to all template registrations.
    push.send(null, templatePayload, {
            success: function(pushResponse) {
                // Push succeeds.
                },
                error: function (pushResponse) {
                    // Push fails.
                    }
                });

##<a id="xplat-app-dev"></a>クロスプラットフォーム アプリ開発
すべての主要モバイル デバイス プラットフォームでネイティブ モバイル デバイス アプリを開発するには、少なくとも Objective-C、Java、C#、または JavaScript プログラミング言語の専門知識が必要になります。こうした異種プラットフォームにおける開発費用の問題から、中にはアプリに完全な Web ブラウザ ベースの操作環境を選ぶ開発者もいます。しかし、こうした Web ベースの操作環境では、ユーザーがモバイル デバイスで期待する高度な操作環境を提供するネイティブ リソースにはほとんどアクセスできません。

クロスプラットフォーム ツールでは、シングル コード ベース (通常は JavaScript) を共有しながら、モバイル デバイスでより高度なネイティブ操作環境を提供します。モバイル サービスでは、次の開発プラットフォームにおいてクイックスタート チュートリアルを提供し、クロスプラットフォームのアプリ開発プラットフォームのバックエンド サービスを簡単に作成し、管理できます。

+ [**Appcelerator**](http://go.microsoft.com/fwlink/p/?LinkId=509987)<br/>Appcelerator では、JavaScript を使用してすべてのモバイル デバイス プラットフォームでネイティブとして実行するようにコンパイルされたシングル アプリを開発できます。これにより、UI における高度な操作環境、すべてのネイティブ デバイス リソースへのアクセス、ネイティブ アプリ パフォーマンスが実現します。詳細については、「[Appcelerator チュートリアル][Appcelerator]」を参照してください。

+ [**PhoneGap**](https://go.microsoft.com/fwLink/p/?LinkID=390707)**/**[**Cordova**](http://cordova.apache.org/)<br/>PhoneGap (Apache Cordova プロジェクトのディストリビューション) は無料のオープン ソース フレームワークで、標準化された Web API、HTML、JavaScript を使用して Android、iOS、Windows デバイスで実行するシングル アプリを開発できます。PhoneGap では Web 表示ベースの UI を提供しますが、プッシュ通知や加速度計、カメラ、ストレージ、位置情報、アプリ内ブラウザなどデバイスのネイティブ リソースへのアクセスによって操作環境が強化されます。詳細については、「[PhoneGap クイックスタート チュートリアル][PhoneGap]」を参照してください。

	Visual Studio では Visual Studio の Multi-Device Hybrid アプリ拡張機能 (プレリリース ソフトウェア) を使用してクロスプラットフォームの Cordova アプリを構築できます。詳細については、「[HTML および JavaScript を使用した Multi-Device Hybrid アプリの概要](http://msdn.microsoft.com/library/dn771545.aspx)」を参照してください。

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch では、1 つの HTML および JavaScript コード ベースからさまざまなモバイル デバイスにネイティブのような操作環境を提供する、タッチ画面用に最適化された一連のコントロールを提供します。Sencha Touch は PhoneGap や Cordova ライブラリと併用し、ネイティブ デバイス リソースへのアクセスをユーザーに提供できます。詳細については、「[Sencha Touch クイックスタート チュートリアル][Sencha]」を参照してください。

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin では、iOS および Android デバイス向けに完全なネイティブ UIとすべてのデバイス リソースへのアクセスを持つ、完全にネイティブなアプリを作成できます。Xamarin アプリは Objective-C と Java ではなく、 C# でコード化されています。これにより、.NET 開発者は iOS と Android にアプリを発行し、Windows プロジェクトからのコードを共有できます。Xamarin では iOS と Android デバイスの両方に C# コードから完全にネイティブの操作環境を提供します。これにより、Windows アプリからのモバイル サービス コードを iOS や Android デバイスで再利用できるようになります。詳細については、以下の「[Xamarin 開発](#xamarin)」を参照してください。

	Xamarin アプリは、Xamarin Studio または Visual Studio 2013 を使用して構築できます。詳細については、「[Visual Studioにおけるクロスプラットフォーム開発](http://msdn.microsoft.com/library/dn771552.aspx)」を参照してください。


##<a id="shared-vs"></a>Visual Studio プロジェクトにおける共有とコードの再利用

モバイル サービスには .NET クライアント ライブラリが含まれています。これはすべての Windows プラットフォームの開発をサポートする .NET Framework ポータブル クラス ライブラリ (PCL) です。詳細については、「[モバイル サービスで .NET を使用する方法]」を参照してください。これにより、複数の C# プロジェクトにおいてデータ アクセスや認証などの同じモバイル サービス コードを簡単に再利用できます。

プロジェクト間で C# コードを共有し、再利用する上での一般的なアプローチの 1 つは、Model-View-ViewModel (MVVM) パターンを実装し、複数のプラットフォームでアセンブリを共有することです。Visual Studio のポータブル クラス ライブラリ プロジェクトでモデル クラスとビュー モデル クラスを実装し、各種プラットフォーム用にカスタマイズされたビューを作成することができます。プラットフォーム間の共通モデル コードでは、(例として) モバイル サービスなどのソースからプラットフォームに依存しない方法でデータを取得できます。MSDN ライブラリでは、<a href="http://msdn.microsoft.com/library/gg597391(v=vs.110)">概要と例</a>、<a href="http://msdn.microsoft.com/library/gg597392(v=vs.110)">API の相違点</a>の説明、<a href="http://msdn.microsoft.com/library/hh563947(v=vs.110)">ポータブル クラス ライブラリを使用した MVVM パターンの実装</a>の例、その他の<a href="http://msdn.microsoft.com/library/windowsphone/develop/jj714086(v=vs.105).aspx">規範的なガイダンス</a>、およびポータブル クラス ライブラリ プロジェクトでの<a href="http://msdn.microsoft.com/library/hh871422(v=vs.110)">リソースの管理</a>に関する情報が提供されています。

この一般的なガイダンスのほか、Visual Studio ではモバイル サービス コードを複数のクライアント アプリ プロジェクトで再利用するための特別機能を提供します。この機能については以下のセクションで説明します。Visual Studio 2013 を使用してクロスプラットフォーム アプリを構築するための一般情報については、「[Visual Studioにおけるクロスプラットフォーム開発](http://msdn.microsoft.com/library/dn771552.aspx)」を参照してください。

### ユニバーサル Windows アプリ

Visual Studio 2013 Update 2 ではユニバーサル Windows アプリ プロジェクトのサポートが追加されました。ユニバーサル アプリは Windows ストア8.1 および Windows Phone Store 8.1 アプリ プロジェクトの両方に、共有コード プロジェクトとともに含まれるソリューションです。この種のプロジェクトでは、共有コードは Windows ストア と Windows Phone プロジェクトの両方の一部として処理されます。詳細については、「[すべての Windows デバイスでユニバーサル Windows アプリを構築する]」を参照してください。ユニバーサル Windows アプリは C#/XAML と JavaScript/HTML の両方で書き込むことができます。

既定では、[Azureの管理ポータル]の [モバイル サービス クイックスタート] タブで開始用の TodoList サンプル アプリのユニバーサル Windows アプリ バージョンが生成されます。ダウンロードするプロジェクトのバージョンは C#/XAML または C#/XAML のいずれかから選択できます。詳細については、「[モバイル サービスの使用](../mobile-services-windows-store-get-started.md)」を参照してください。

>[AZURE.NOTE]ポータルからのクイックスタート アプリ プロジェクトの C# バージョンは、MainPage.xaml.cs コード ビハインド ページを共有しますが、ビュー モデルは使用しません。C# のユニバーサル アプリ プロジェクトとして MVVM を使用する TodoList アプリの例は、「[MVVM を使用した Azure モバイル サービスのユニバーサル Windows アプリ プロジェクト]」を参照してください。

###<a id="xamarin"></a>Xamarin 開発

Visual Studio と C# 開発経験を活用し、Xamarin と Visual Studio または Xamarin Studio を使用して iOS および Android 向けのアプリを開発できます。Xamarin では .NET Framework のクロスプラットフォーム実装を使用するため、C# コードで iOS および Android アプリを開発できます。Xamarin を使用すると、モバイル サービス NET クライアント ライブラリを使用した プロジェクトから既存のコードを活用してモバイル サービスにアクセスできます。詳細については、「[Visual Studioにおけるクロスプラットフォーム開発](http://msdn.microsoft.com/library/dn771552.aspx)」を参照してください。

モバイル サービスを使用する Xamarin アプリの構築を開始するには、Xamarin クイックスタート チュートリアル ([iOS](mobile-services-ios-get-started.md) / [Android](mobile-services-android-get-started.md)) を参照してください。


### Windows ストアおよび Windows Phone Silverlight アプリ

Windows Phone 8.1 では、以前の Silverlight ベースの XAML を使用してアプリを開発するか、ユニバーサル Windows アプリの開発ができる Windows ランタイム ベースの XAML を使用することができます。Windows Phone 8.1 Silverlight アプリおよび Windows Phone ストア 8.1 アプリの詳細については、「[Windows Phone 8 開発者の次の手順]」を参照してください。

モバイル サービス .NET クライアント ライブラリでは、Windows Phone ストア 8.1 および Windows Phone Silverlight 8.1 アプリの両方をサポートします。Windows ランタイムと Windows Phone Silverlight アプリは同じプロジェクトから構築できないため、上述したように PCL や MVVM などのコード再利用計画の使用を考慮する必要があります。

>[AZURE.NOTE]Windows ランタイムと Windows Phone Silverlight の両方のアプリで Microsoft アカウントを使用したシングル サインオン クライアント認証を利用するには、まず Windows ストア ダッシュボードで Windows ランタイム アプリを登録する必要があります。Windows Phone 用に Live Connect 登録を作成した後は、Windows ストア用に登録を作成できないためです。この方法の詳細については、「**Live Connect シングル サインオンによる Windows ストア アプリケーションの認証**」([Windows ストア][SSO Windows Store]/[Windows Phone][SSO Windows Phone]) を参照してください。


<!-- URLs -->
[Azureの管理ポータル]: https://manage.windowsazure.com
[Azure 通知ハブ]: /develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows Store]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /develop/mobile/tutorials/single-sign-on-wp8/
[Tutorials and resources]: /develop/mobile/resources/
[Get started with Notification Hubs]: /manage/services/notification-hubs/getting-started-windows-dotnet/
[ユーザーへのクロスプラットフォーム通知の送信]: /manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet]: /develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js]: /develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Get started with push Windows Phone]: /develop/mobile/tutorials/get-started-with-push-wp8/
[Get started with push iOS]: /develop/mobile/tutorials/get-started-with-push-ios/
[Get started with push Android]: /develop/mobile/tutorials/get-started-with-push-android/
[Dynamic schema]: http://msdn.microsoft.com/library/windowsazure/jj193175.aspx
[モバイル サービスで .NET を使用する方法]: documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[send]: http://msdn.microsoft.com/library/windowsazure/jj554217.aspx
[TemplatePushMessage]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: mobile-services-javascript-backend-phonegap-get-started.md
[Sencha]: partner-sencha-mobile-services-get-started.md
[Appcelerator]: ../partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started.md
[SendAsync]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[ApiServices.Push]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[IPushMessage]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Windows Phone 8 開発者の次の手順]: http://msdn.microsoft.com/library/windows/apps/dn655121(v=vs.105).aspx
[すべての Windows デバイスでユニバーサル Windows アプリを構築する]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[MVVM を使用した Azure モバイル サービスのユニバーサル Windows アプリ プロジェクト]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!---HONumber=September15_HO1-->