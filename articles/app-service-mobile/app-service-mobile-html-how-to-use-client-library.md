<properties
	pageTitle="Azure Mobile Apps 向け JavaScript SDK の使用方法"
	description="Azure Mobile Apps 向け JavaScript SDK の使用方法"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="06/29/2016"
	ms.author="adrianha;ricksal"/>

# Azure Mobile Apps 向け JavaScript クライアント ライブラリの使用方法

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

このガイドでは、最新の [Azure Mobile Apps 向け JavaScript SDK] を使用して一般的なシナリオを実行する方法について説明します。Azure Mobile Apps を初めて使用する場合は、まず、「[Apache Cordova アプリの作成]」を参照して、バックエンドおよびテーブルの作成を行ってください。このガイドでは、HTML/JavaScript Web アプリケーションでのモバイル バックエンドの使用に重点を置いています。

##<a name="Setup"></a>セットアップと前提条件

このガイドでは、バックエンドとテーブルを作成済みであることを前提としています。このガイドでは、テーブルのスキーマが、これらのチュートリアルのテーブルの場合と同じであることを前提とします。

Azure Mobile Apps JavaScript SDK のインストールは、次の `npm` コマンドで行うことができます。

```
npm install azure-mobile-apps-client --save
```

インストール後、ライブラリが `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js` に配置されます。このファイルを Web エリアにコピーします。

```
<script src="path/to/MobileServices.Web.min.js"></script>
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

###<a name="configure-external-redirect-urls"></a>方法: 外部リダイレクト URL 用に Mobile Apps サービスを構成する

いくつかの種類の JavaScript アプリケーションでは、Ionic Framework のライブ リロードを使ってサービスをローカルで実行したときや、認証を受ける目的で App Service にリダイレクトしたときなどの OAuth UI フローを処理するために、ループバック機能を使用します。これにより問題が発生する場合があります。既定では、App Service 認証は、モバイル アプリ バックエンドからのアクセスを許可するためだけに構成されているからです。

App Service 設定を変更して localhost からの認証を有効にするには、次の手順を実施します。

1. [Azure ポータル] にログインし、モバイル アプリ バックエンドに移動します。**[ツール]**、**[リソース エクスプローラー]**、**[Go (進む)]** の順にクリックし、モバイル アプリ バックエンド (サイト) 用の新しいリソース エクスプローラー ウィンドウを開きます。

2. アプリの**構成**ノードを展開し、**[authsettings (認証設定)]**、**[編集]** の順にクリックして、**allowedExternalRedirectUrls** 要素を探します。これは null になっているため、次のとおりに変更します。

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    配列内の URL をサービスの URL に置き換えます。この例では、ローカルの Node.js サンプル サービス用の `http://localhost:3000` となっています。アプリケーションの構成の仕方によっては、Ripple サービスや他の URL に `http://localhost:4400` を使用することもできます。
    
3. ページの上部で **[読み取り/書き込み]**、**[PUT]** の順にクリックして、更新を保存します。

    CORS のホワイトリスト設定にも、同じループバック URL を追加する必要があります。

4. モバイル アプリ バックエンドで [Azure ポータル] に戻り、**[すべての設定]**、**[CORS]** の順にクリックします。ホワイトリストにループバック URL を追加したら、**[保存]** をクリックします。

バックエンドの更新が済むと、アプリケーションで新しいループバック URL を使用できるようになります。

<!-- URLs. -->
[Apache Cordova アプリの作成]: app-service-mobile-cordova-get-started.md
[モバイル サービスでの認証の使用]: app-service-mobile-cordova-get-started-users.md
[アプリへの認証の追加]: app-service-mobile-cordova-get-started-users.md

[Azure Mobile Apps 向け JavaScript SDK]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/ja-JP/library/azure/jj613353.aspx

<!----HONumber=AcomDC_0629_2016-->