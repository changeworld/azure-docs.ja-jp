<properties
	pageTitle="Azure Mobile Apps 向け JavaScript SDK の使用方法"
	description="Azure Mobile Apps 向け JavaScript SDK の使用方法"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Azure Mobile Apps 向け JavaScript クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、最新の「Azure Mobile Apps 向け JavaScript SDK」を使用して一般的なシナリオを実行する方法について説明します。Azure Mobile Apps を初めて使用する場合は、まず、「[Apache Cordova アプリの作成]」を参照して、バックエンドおよびテーブルの作成を行ってください。このガイドでは、HTML/JavaScript Web アプリケーションでのモバイル バックエンドの使用に重点を置いています。

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。

Azure Mobile Apps JavaScript SDK のインストールは、次の `npm` コマンドで行うことができます。

```
npm install azure-mobile-apps-client --save
```

インストール後、ライブラリが `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.js` に配置されます。このファイルを Web エリアにコピーします。

```
<script src="path/to/MobileServices.Web.js"></script>
```

ライブラリは、Browserify や Webpack などの CommonJS 環境では ES2015 モジュールとして使用できるほか、AMD ライブラリとしても使用できます。次に例を示します。

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>方法: ユーザーを認証する

Azure App Service は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「[モバイル サービスでの認証の使用]」を参照してください。

サーバー フローとクライアント フローの 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>方法: プッシュ通知に登録する

プッシュ通知を処理するには、[phonegap-plugin-push] をインストールします。これは、コマンド ラインで `cordova plugin add` コマンドを使用するか、または Visual Studio の Git プラグイン インストーラーを使用することで、簡単に追加できます。Apache Cordova アプリの次のコードによって、デバイスがプッシュ通知に登録されます。

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

プッシュ通知は、Notification Hubs SDK を使用してサーバーから送信します。プッシュ通知をクライアントから直接送信しないでください。この場合、Notification Hubs または PNS に対してサービス拒否攻撃をトリガーすることに利用されるおそれがあるからです。

<!-- URLs. -->
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[アプリへの認証の追加]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[query オブジェクト]: https://msdn.microsoft.com/ja-JP/library/azure/jj613353.aspx

<!----HONumber=AcomDC_0309_2016-->
