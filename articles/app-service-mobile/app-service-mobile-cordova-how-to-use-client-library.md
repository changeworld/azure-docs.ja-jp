<properties
	pageTitle="Azure Mobile Apps 向け Apache Cordova プラグインの使用方法"
	description="Azure Mobile Apps 向け Apache Cordova プラグインの使用方法"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="05/23/2016"
	ms.author="ggailey"/>

# Azure Mobile Apps 向け Apache Cordova クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、最新の [Azure Mobile Apps 向け Apache Cordova プラグイン]を使用して一般的なシナリオを実行する方法について説明します。Azure Mobile Apps を初めて使用する場合は、まず、「[Apache Cordova アプリの作成]」を参照して、バックエンドの作成、テーブルの作成、構築済みの Apache Cordova プロジェクトのダウンロードを行ってください。このガイドでは、クライアント側の Apache Cordova プラグインに重点を置いています。

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。さらに、Apache Cordova プラグインがコードに追加済みであることを前提とします。まだそれを行っていない場合は、コマンドラインで Apache Cordova プラグインをプロジェクトに追加することができます。

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Apache Cordova アプリを初めて作成する場合は、[こちら]のドキュメントを参照してください。

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>方法: ユーザーを認証する

Azure App Service は、Facebook、Google、Microsoft アカウント、Twitter などのさまざまな外部 ID プロバイダーを使用したアプリケーション ユーザーの認証と承認をサポートします。テーブルのアクセス許可を設定することにより、特定の操作へのアクセスを認証されたユーザーのみに制限できます。さらに、認証されたユーザーの ID を使用することにより、サーバー スクリプトで承認ルールを実装することもできます。詳細については、チュートリアル「[モバイル サービスでの認証の使用]」を参照してください。

Apache Cordova アプリで認証を使用する場合は、次の Cordova プラグインが使用できる状態になければなりません。

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

サーバー フローとクライアント フローの 2 つの認証フローがサポートされます。サーバー フローには、プロバイダーの Web 認証のインターフェイスを利用する、最も簡単な認証方法が用意されています。クライアント フローでは、プロバイダー固有およびデバイス固有の SDK を利用することから、シングル サインオンなどのデバイス固有の機能との統合がさらに進みます。

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

###<a name="configure-external-redirect-urls"></a>方法: 外部リダイレクト URL 用に Mobile App Service を構成する

いくつかの種類の Apache Cordova アプリケーションでは、ループバック機能を使用して OAuth UI フローを処理します。これにより問題が生じます。既定では認証サービスで認識されるのがサービスの利用方法のみであるためです。この例として、Ripple エミュレーターを使用し、サービスをローカルまたは別の Azure App Service で実行しているが、認証のために Azure App Service、または Live Reload (Ionic を使用) にリダイレクトする場合などがあります。ローカル設定を構成に追加するには、以下の手順に従います。

1. [Azure ポータル]にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
3. **[ツール]** をクリックします。
4. [監視] メニューの **[リソース エクスプローラー]** をクリックしてから、**[実行]** をクリックします。新しいウィンドウまたはタブが開きます。
5. 左側のナビゲーションで、サイトの **config**、**authsettings** ノードの順に展開します。
6. **[編集]** をクリックします。
7. "allowedExternalRedirectUrls" 要素を探します。これは null に設定されます。これを次のように変更します。

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    URL をご使用のサービスの URL に置き換えます。例には、"http://localhost:3000" (Node.js サンプル サービス用)、または "http://localhost:4400" (Ripple サービス用) が含まれています。ただし、これらは例にすぎません。例に示されているサービスを含め、状況に応じて異なる場合があります。
8. 画面の右上隅にある **[読み取り/書き込み]** ボタンをクリックします。
9. 緑の **[PUT]** ボタンをクリックします。

この時点で設定は保存されます。設定の保存が完了するまで、ブラウザー ウィンドウを閉じないでください。以下のように、CORS 設定にこれらのループバック URL を追加する必要もあります。

1. [Azure ポータル]にログインします。
2. **[すべてのリソース]** または **[App Services]** を選択し、モバイル アプリの名前をクリックします。
3. [設定] ブレードが自動的に開きます。開かない場合は、**[すべての設定]** をクリックします。
4. API メニューの **[CORS]** をクリックします。
5. 表示されたボックスに追加する URL を入力して、Enter キーを押します。
6. 必要に応じて、さらに URL を入力します。
7. **[保存]** をクリックして設定を保存します。

新しい設定が有効になるまで約 10 ～ 15 秒かかります。

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
[Azure ポータル]: https://portal.azure.com
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps 向け Apache Cordova プラグイン]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[こちら]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/ja-JP/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0525_2016-->