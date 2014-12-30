<properties pageTitle="シングル モバイル サービス バックエンドで複数のクライアントを使用する方法 | Azure Mobile" metaKeywords="mobile services single backend" description="Learn how to use a single mobile service back-end from multiple client apps that target different mobile platforms, including Windows Store and Windows Phone." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" manager="dwrede" editor="mollybos" />
<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="08/15/2014" ms.author="glenga" />

# シングル モバイル サービスから複数のデバイス プラットフォームをサポートする
 
モバイル アプリ開発で Azure モバイル サービスを使用する主なメリットの 1 つに、複数のクライアント プラットフォームでアプリをサポートするシングル バックエンド サービスの使用があります。モバイル サービスでは、すべての主要デバイス プラットフォームに対してネイティブ クライアント ライブラリを提供するため、シングル バックエンド サービスとクロスプラットフォーム開発者ツールを使用して簡単にアプリが開発できます。このトピックではシングル モバイル サービス バックエンドを使用しながら複数のクライアント プラットフォームでアプリを実行させるための次の考慮事項について説明します。

+ [クロスプラットフォーム プッシュ通知](#push)
+ [クロスプラットフォーム アプリ開発](#xplat-app-dev)
+ [Visual Studio プロジェクトでのコードの共有](#shared-vs)

Mobile Services についての一般情報については「[モバイル サービス デベロッパー センター](/ja-jp/documentation/services/mobile-services/)」を参照してください。

##<a id="push"></a>クロスプラットフォーム プッシュ通知

モバイル サービスでは、すべての主要デバイス プラットフォームのクライアント アプリへのプッシュ通知の送信に Azure 通信ハブを使用しています。通知ハブによって提供される一貫性のある統一されたインフラストラクチャで、デバイスの登録を作成、管理したり、クロスプラットフォーム プッシュ通知を送信したりできます。通知ハブでは、次のプラットフォーム固有の通知サービスを使用してプッシュ通知の送信をサポートしています。

+ iOS アプリ向け Apple Push Notification Service (APNS)
+ Android アプリ向け Google Cloud Messaging (GCM) サービス
+ Windows ストア、Windows Phone 8.1 ストア、ユニバーサル Windows アプリ向け Windows Notification Service (WNS) 
+ Windows Phone Silverlight アプリ向け Microsoft Push Notification Service (MPNS)

>[WACOM.NOTE]Notification Hubs では、WNS を使用した Windows Phone Silverlight 8.1 アプリへのプッシュ通知の送信は現在サポートしていません。Silverlight、Windows Phone 8.0 および 7.0 アプリへの通知の送信には MPNS を使用する必要があります。

詳細については、「[Azure Notification Hubs]」を参照してください。

クライアントの登録は、プラットフォーム固有のモバイル サービス クライアント ライブラリで register 関数か、モバイル サービス REST API を使用して作成されます。通知ハブでは次の 2 種類のデバイス登録をサポートします。

+ **ネイティブ登録**<br/>ネイティブ登録はプラットフォーム固有のプッシュ通知サービスに合わせて調整されます。ネイティブ登録を使用して登録したデバイスに通知を送信する場合、モバイル サービスでプラットフォーム固有の API を呼び出す必要があります。複数のプラットフォーム上のデバイスに通知を送信するには、複数のプラットフォーム固有の呼び出しが必要になります。   
  
+ **テンプレート登録**<br/>Notification Hubs ではプラットフォーム固有のテンプレート登録もサポートします。テンプレート登録を使用すると、1 回の API 呼び出しを使用して登録したデバイス上で稼働するアプリに通知を送信できます。詳細については、「[ユーザーへのクロスプラットフォーム通知の送信]」を参照してください。

>[WACOM.NOTE]デバイスの登録がされていないネイティブ デバイス プラットフォームにメッセージの送信を試みると、エラーが発生します。このエラーはテンプレート通知を送信する場合には発生しません。

次のセクションのテーブルはクライアント固有のチュートリアルにリンクしており、.NET と JavaScript バックエンド両方のモバイル サービスからプッシュ通知を実装する方法を示しています。  

###.NET バックエンド

.NET バックエンド モバイル サービスでは、[SendAsync] メソッドを [PushClient](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.notifications.pushclient.aspx)  オブジェクト ([ApiServices.Push](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.apiservices.push.aspx)  プロパティから取得) で呼び出すことで、通知を送信します。プッシュ通知の送信 (ネイティブまたはテンプレート) は特定の [IPushMessage](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.notifications.ipushmessage.aspx) から派生したオブジェクトに依存します。このオブジェクトは、次の表に示すように、[SendAsync] メソッドに渡されます。 

|プラットフォーム|[APNS](/ja-jp/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push)|[GCM](/ja-jp/documentation/articles/mobile-services-dotnet-backend-android-get-started-push) |[WNS](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) |[MPNS](/ja-jp/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push)|
|-----|-----|----|----|-----|
|ネイティブ|[ApplePushMessage](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.applepushmessage.aspx)   |[GooglePushMessage](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.googlepushmessage.aspx)     |[WindowsPushMessage](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.windowspushmessage.aspx) | [MpnsPushMessage](http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.mpnspushmessage.aspx) |

次のコードは .NET バックエンド サービスからすべての iOS および Windows ストア デバイス登録にプッシュ通知を送信します。 

	// Define a push notification for APNS.
	ApplePushMessage apnsMessage = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));    

	// Define a push notification for WNS.
	WindowsPushMessage wnsMessage = new WindowsPushMessage();
    wnsMessage.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                         @"<toast><visual><binding template=""ToastText01"">" +

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

JavaScript バックエンド モバイル サービスでは、次のテーブルで示すようにグローバル [push オブジェクト]から取得したプラットフォーム固有のオブジェクト上で **send** メソッドを呼び出して通知を送信します。 

|プラットフォーム|[APNS](/ja-jp/documentation/articles/mobile-services-javascript-backend-ios-get-started-push)|[GCM](/ja-jp/documentation/articles/mobile-services-javascript-backend-android-get-started-push) |[WNS](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push) |[MPNS](/ja-jp/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push)|
|-----|-----|----|----|-----|
|ネイティブ|[apns object](http://msdn.microsoft.com/ja-jp/library/azure/jj839711.aspx)   |[gcm object](http://msdn.microsoft.com/ja-jp/library/azure/dn126137.aspx)     |[wns object](http://msdn.microsoft.com/ja-jp/library/azure/jj860484.aspx) | [mpns object](http://msdn.microsoft.com/ja-jp/library/azure/jj871025.aspx) |

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

ネイティブ クライアント登録ではなく、テンプレート クライアント登録を使用する場合、次のようにグローバル [push オブジェクト]上で **send** 関数を 1 回呼び出して同じ通知を送信し、テンプレート メッセージ ペイロードを提供します。 

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
	
	Visual Studio では Visual Studio の Multi-Device Hybrid アプリ拡張機能 (プレリリース ソフトウェア) を使用してクロスプラットフォームの Cordova アプリを構築できます。詳細については、「[HTML および JavaScript を使用した Multi-Device Hybrid アプリの概要](http://msdn.microsoft.com/ja-jp/library/dn771545.aspx)」を参照してください。 

+ [**Sencha Touch**](http://go.microsoft.com/fwlink/p/?LinkId=509988)<br/>Sencha Touch では、1 つの HTML および JavaScript コード ベースからさまざまなモバイル デバイスにネイティブのような操作環境を提供する、タッチ画面用に最適化された一連のコントロールを提供します。Sencha Touch は PhoneGap や Cordova ライブラリと併用し、ネイティブ デバイス リソースへのアクセスをユーザーに提供できます。詳細については、「[Sencha Touch クイックスタート チュートリアル][Sencha]」を参照してください。

+ [**Xamarin**](https://go.microsoft.com/fwLink/p/?LinkID=330242)<br/>Xamarin では、iOS および Android デバイス向けに完全なネイティブ UI とすべてのデバイス リソースへのアクセスを持つ、完全にネイティブなアプリを作成できます。Xamarin アプリは Objective-C と Java ではなく、 C# でコード化されています。これにより、.NET 開発者は iOS と Android にアプリを発行し、Windows プロジェクトからのコードを共有できます。Xamarin では iOS と Android デバイスの両方に C# コードから完全にネイティブの操作環境を提供します。これにより、Windows アプリからのモバイル サービス コードを iOS や Android デバイスで再利用できるようになります。詳細については、以下の「[Xamarin 開発](#xamarin) 」を参照してください。 

	Xamarin アプリは、Xamarin Studio または Visual Studio 2013 を使用して構築できます。詳細については、「[Visual Studio におけるクロスプラットフォーム開発](http://msdn.microsoft.com/ja-jp/library/dn771552.aspx)」を参照してください。


##<a id="shared-vs"></a>Visual Studio プロジェクトにおける共有とコードの再利用

モバイル サービスには .NET クライアント ライブラリが含まれています。これはすべての Windows プラットフォームの開発をサポートする .NET Framework ポータブル クラス ライブラリ (PCL) です。詳細については、「[モバイル サービスで .NET を使用する方法]」を参照してください。これにより、複数の C# プロジェクトにおいてデータ アクセスや認証などの同じモバイル サービス コードを簡単に再利用できます。

プロジェクト間で C# コードを共有し、再利用する上での一般的なアプローチの 1 つは、Model-View-ViewModel (MVVM) パターンを実装し、複数のプラットフォームでアセンブリを共有することです。Visual Studio のポータブル クラス ライブラリ プロジェクトでモデル クラスとビュー モデル クラスを実装し、各種プラットフォーム用にカスタマイズされたビューを作成することができます。プラットフォーム間の共通モデル コードでは、(例として) モバイル サービスなどのソースからプラットフォームに依存しない方法でデータを取得できます。MSDN ライブラリでは、 <a href="http://msdn.microsoft.com/ja-jp/library/gg597391(v=vs.110)">概要と例</a>、 <a href="http://msdn.microsoft.com/ja-jp/library/gg597392(v=vs.110)">API の相違点</a>の説明、 <a href="http://msdn.microsoft.com/ja-jp/library/hh563947(v=vs.110)">ポータブル クラス ライブラリを使用した MVVM パターンの実装の例</a>、その他の <a href="http://msdn.microsoft.com/ja-jp/library/windowsphone/develop/jj714086(v=vs.105).aspx">規範的なガイダンス</a>、およびポータブル クラス ライブラリ プロジェクトでの <a href="http://msdn.microsoft.com/ja-jp/library/hh871422(v=vs.110)">リソースの管理</a> に関する情報が提供されています。

この一般的なガイダンスのほか、Visual Studio ではモバイル サービス コードを複数のクライアント アプリ プロジェクトで再利用するための特別機能を提供します。この機能については以下のセクションで説明します。Visual Studio 2013 を使用してクロスプラットフォーム アプリを構築するための一般情報については、「[Visual Studio におけるクロスプラットフォーム開発](http://msdn.microsoft.com/ja-jp/library/dn771552.aspx)」を参照してください。  

### ユニバーサル Windows アプリ

Visual Studio 2013 Update 2 ではユニバーサル Windows アプリ プロジェクトのサポートが追加されました。ユニバーサル アプリは Windows ストア8.1 および Windows Phone Store 8.1 アプリ プロジェクトの両方に、共有コード プロジェクトとともに含まれるソリューションです。この種のプロジェクトでは、共有コードは Windows ストア と Windows Phone プロジェクトの両方の一部として処理されます。詳細については、「[すべての Windows デバイスでユニバーサル Windows アプリを構築する]」を参照してください。ユニバーサル Windows アプリは C#/XAML と JavaScript/HTML の両方で書き込むことができます。 

既定では、[Azure の管理ポータル]の [Mobile Service クイックスタート] タブで開始用の TodoList サンプル アプリのユニバーサル Windows アプリ バージョンが生成されます。ダウンロードするプロジェクトのバージョンは C#/XAML または C#/XAML のいずれかから選択できます。詳細については、「[モバイル サービスの使用](/ja-jp/documentation/articles/mobile-services-windows-store-get-started/)」を参照してください。 

>[WACOM.NOTE]ポータルからのクイックスタート アプリ プロジェクトの C# バージョンは、MainPage.xaml.cs コード ビハインド ページを共有しますが、ビュー モデルは使用しません。C# のユニバーサル アプリ プロジェクトとして MVVM を使用する TodoList アプリの例は、「[MVVM を使用した Azure Mobile Services のユニバーサル Windows アプリ プロジェクト]」を参照してください。 

###<a id="xamarin"></a>Xamarin 開発

Visual Studio と C# 開発経験を活用し、Xamarin と Visual Studio または Xamarin Studio を使用して iOS および Android 向けのアプリを開発できます。Xamarin では .NET Framework のクロスプラットフォーム実装を使用するため、C# コードで iOS および Android アプリを開発できます。Xamarin を使用すると、モバイル サービス NET クライアント ライブラリを使用した プロジェクトから既存のコードを活用してモバイル サービスにアクセスできます。詳細については、「[Visual Studio におけるクロスプラットフォーム開発](http://msdn.microsoft.com/ja-jp/library/dn771552.aspx)」を参照してください。

Mobile Services を使用する Xamarin アプリの構築を開始するには、Xamarin クイックスタート チュートリアル ([iOS](/ja-jp/documentation/articles/partner-xamarin-mobile-services-ios-get-started) /[Android](/ja-jp/documentation/articles/partner-xamarin-mobile-services-android-get-started)) を参照してください。


### Windows ストアおよび Windows Phone Silverlight アプリ

Windows Phone 8.1 では、以前の Silverlight ベースの XAML を使用してアプリを開発するか、ユニバーサル Windows アプリの開発ができる Windows ランタイム ベースの XAML を使用することができます。Windows Phone 8.1 Silverlight アプリおよび Windows Phone ストア 8.1 アプリの詳細については、「[Windows Phone 8 開発者の次の手順]」を参照してください。 

モバイル サービス .NET クライアント ライブラリでは、Windows Phone ストア 8.1 および Windows Phone Silverlight 8.1 アプリの両方をサポートします。Windows ランタイムと Windows Phone Silverlight アプリは同じプロジェクトから構築できないため、上述したように PCL や MVVM などのコード再利用計画の使用を考慮する必要があります。

>[WACOM.NOTE]Windows ランタイムと Windows Phone Silverlight の両方のアプリで Microsoft アカウントを使用したシングル サインオン クライアント認証を利用するには、まず Windows ストア ダッシュボードで Windows ランタイム アプリを登録する必要があります。Windows Phone 用に Live Connect 登録を作成した後は、Windows ストア用に登録を作成できないためです。この方法の詳細については、「**Live Connect シングル サインオンによる Windows ストア アプリケーションの認証**」([Windows ストア][SSO Windows Store]/[Windows Phone][SSO Windows Phone]) を参照してください。


<!-- URLs -->
[Azure の管理ポータル]: https://manage.windowsazure.com
[Azure Notification Hubs]: /ja-jp/develop/net/how-to-guides/service-bus-notification-hubs/
[SSO Windows ストア]: /ja-jp/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[SSO Windows Phone]: /ja-jp/develop/mobile/tutorials/single-sign-on-wp8/
[チュートリアルとリソース]: /ja-jp/develop/mobile/resources/
[通知ハブの使用]: /ja-jp/manage/services/notification-hubs/getting-started-windows-dotnet/
[ユーザーへのクロスプラットフォーム通知の送信]: /ja-jp/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Get started with push Windows dotnet (Mobile Services アプリへのプッシュ通知の追加: Windows dotnet)]: /ja-jp/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012/
[Get started with push Windows js (Mobile Services アプリへのプッシュ通知の追加: Windows js)]: /ja-jp/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Mobile Services アプリへのプッシュ通知の追加: Windows Phone]: /ja-jp/develop/mobile/tutorials/get-started-with-push-wp8/
[Mobile Services アプリへのプッシュ通知の追加: iOS]: /ja-jp/develop/mobile/tutorials/get-started-with-push-ios/
[Mobile Services アプリへのプッシュ通知の追加: Android]: /ja-jp/develop/mobile/tutorials/get-started-with-push-android/
[動的スキーマ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj193175.aspx
[How to use a .NET client with Mobile Services (Mobile Services での .NET クライアントの使用方法)]: ja-jp/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[push オブジェクト]: http://msdn.microsoft.com/ja-jp/library/windowsazure/jj554217.aspx
[TemplatePushMessage (TemplatePushMessage クラス)]:http://msdn.microsoft.com/ja-jp/library/azure/microsoft.windowsazure.mobile.service.templatepushmessage.aspx
[PhoneGap]: /ja-jp/documentation/articles/mobile-services-javascript-backend-phonegap-get-started/
[Sencha (Sencha)]: /ja-jp/documentation/articles/partner-sencha-mobile-services-get-started/
[Appcelerator]: /ja-jp/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
[SendAsync (PushClientSendAsync メソッド)]: http://msdn.microsoft.com/ja-jp/library/microsoft.windowsazure.mobile.service.notifications.pushclient.sendasync.aspx
[Windows Phone 8 開発者にとっての選択肢]: http://msdn.microsoft.com/ja-jp/library/windows/apps/dn655121(v=vs.105).aspx
[Building universal Windows apps for all Windows devices (すべての Windows デバイスに対応するユニバーサル Windows アプリの構築)]: http://go.microsoft.com/fwlink/p/?LinkId=509905
[Universal Windows app project for Azure Mobile Services using MVVM (MVVM を使用した Azure Mobile Services 用ユニバーサル Windows アプリ プロジェクト)]: http://code.msdn.microsoft.com/Universal-Windows-app-for-db3564de

<!--HONumber=35_1-->
