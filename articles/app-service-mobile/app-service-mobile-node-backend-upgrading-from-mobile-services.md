<properties
	pageTitle="Mobile Services から Azure App Service (Node.js) へのアップグレード"
	description="簡単に Mobile Services アプリケーションを App Service モバイル アプリにアップグレードする方法について説明します。"
	services="app-service\mobile"
	documentationCenter=""
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile"
	ms.devlang="node"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="chrande"/>

# App Service への既存の Node.js Azure Mobile Service のアップグレード

App Service Mobile は、Microsoft Azure を使用してモバイル アプリケーションを構築する新しい方法です。詳細については、「[Mobile Apps とは]」を参照してください。

このトピックでは、既存の Node.js バックエンド アプリケーションを Azure Mobile Services から新しい App Service Mobile Apps にアップグレードする方法について説明します。このアップグレードの実行中も、既存の Mobile Services アプリケーションの動作を続行できます。

Azure App Service にアップグレードされたモバイル バックエンドは、App Service のすべての機能にアクセスでき、Mobile Services の価格ではなく [App Service の価格]に従って課金されます。

## 移行とアップグレード

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]アップグレードする前に、[移行する](app-service-mobile-migrating-from-mobile-services.md)ことをお勧めします。そうすることで、同じ App Service プランに両方のバージョンのアプリケーションを指定できるため、追加コストが発生しません。

### Mobile Apps Node.js サーバー SDK の機能強化

新しい [Mobile Apps SDK](https://www.npmjs.com/package/azure-mobile-apps) にアップグレードすると、次のような多くの機能が強化されます。

- [Express フレームワーク](http://expressjs.com/en/index.html)に基づく新しい軽量の Node SDK は、リリースされる新しい Node のバージョンに対応できるように設計されています。Express ミドルウェアでアプリケーションの動作をカスタマイズすることができます。

- Mobile Services SDK に比べ、パフォーマンスが大幅に向上します。

- モバイル バックエンドと共に Web サイトをホストできるようになりました。同様に、既存の expressv4 アプリケーションに Azure Mobile SDK を簡単に追加することができます。

- クロスプラットフォームとローカル開発用に構築されており、Mobile Apps SDK を開発して Windows、Linux、および OSX プラットフォーム上でローカルに実行できます。デプロイメントの前の [Mocha](https://mochajs.org/) テストの実行などの一般的な Node 開発手法を簡単に使用できるようになりました。

- [hiredis](https://www.npmjs.com/package/hiredis) などのネイティブ モジュールと共に、Redis を使用できます。App Service によって npm パッケージがインストールされるため、デプロイ パッケージにバイナリを含める必要はありません。

## <a name="overview"></a>基本的なアップグレードの概要

.NET Mobile Apps SDK とは異なり、Node バックエンドを Mobile Services から Mobile Apps にアップグレードすることは、パッケージ交換のように単純なものではありません。Azure で制御するのではなく、ユーザーがアプリケーション スタック全体を所有するようになったため、基本的な Express アプリを作成してモバイル バックエンドをホストする必要があります。テーブルと API コントローラーについては、概念は似ていますが、テーブル オブジェクトのエクスポートが必要になり、関数 API が多少変わりました。この記事では、アップグレードの基本的な戦略について説明しますが、移行を開始する前に [Node バックエンドの使用方法](app-service-mobile-node-backend-how-to-use-server-sdk.md)に関する記述を参照してください。

>[AZURE.TIP]アップグレードを開始する前に、このトピックの残りの部分を読み、よく理解しておいてください。下に列記した機能のうち、使用する機能をすべてメモしてください。

Mobile Services クライアント SDK と新しい Mobile Apps サーバー SDK の間に互換性は**ありません**。アプリ用にサービスを接続する場合は、発行されたクライアントを現在使用しているサイトに変更を発行しないでください。代わりに、複製として機能する新しいモバイル アプリを作成する必要があります。このアプリケーションを同じ App Service プランに指定することで、追加の財務費用が発生しないようにすることができます。

アプリケーションには 2 つのバージョンがあります。1 つは変わらず、発行されたアプリとしてそのままの状態で機能し、もう 1 つはユーザーがアップグレードし、新しいクライアント リリースのターゲットにすることができます。自分のペースでコードを移動し、テストできますが、バグの修正が両方に適用されることを確認する必要があります。必要な数の (そのままの状態の) クライアント アプリを最新バージョンに更新したら、必要に応じて、元の移行されたアプリを削除することができます。モバイル アプリと同じ App Service プランでホストされている場合は、追加の金銭的コストが発生することはありません。

このアップグレード プロセスの全概要は、次のとおりです。

1. 新しいモバイル アプリを作成する。
2. 新しい Server SDK を使用するようにプロジェクトを更新する。
3. 新しいモバイル アプリのプロジェクトを発行する。
4. 新しいモバイル アプリを使用するクライアント アプリケーションの新しいバージョンをリリースする。
5. (省略可能) 元の移行されたモバイル サービス アプリを削除する。

削除は、元の移行されたモバイル サービス アプリのトラフィックがないときに実行されます。

## <a name="mobile-app-version"></a> アップグレードの開始
アップグレードの最初のステップは、新しいバージョンのアプリケーションをホストするモバイル アプリ リソースを作成することです。既存のモバイル サービスを既に移行している場合は、同じホスティング プランでこのバージョンを作成します。[Azure ポータル]を開き、移行済みのアプリケーションに移動します。実行されている App Service プランをメモしてをおきます。

### 2 番目のアプリケーション インスタンスの作成
次に、2 番目のアプリケーション インスタンスを作成します。App Service プランまたは "ホスティング プラン" を選択するよう求められたら、移行済みアプリケーションのプランを選択します。

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

Mobile Services で使用したのと同じデータベースと通知ハブを使用することもできます。[Azure ポータル]を開き、元のアプリケーションに移動してから、**[設定]**、**[アプリケーションの設定]** の順にクリックして、これらの値をコピーできます。**[接続文字列]** の `MS_NotificationHubConnectionString` と `MS_TableConnectionString` をコピーします。新しいアップグレード サイトに移動し、接続文字列を貼り付けて既存の値を上書きします。アプリに必要な他のアプリケーション設定について、このプロセスを繰り返します。移行したサービスを使用しない場合は、[Azure ポータル]の [Mobile Services] セクションにある **[構成]** タブで接続文字列とアプリ設定を参照できます。

### Node を持つ基本的なモバイル アプリ バックエンドを作成する

Azure App Service モバイル アプリの Node.js バックエンドはすべて ExpressJS アプリケーションとして開始されます。次のように、基本的な [Express](http://expressjs.com/en/index.html) アプリケーションを作成できます。

1. コマンドまたは PowerShell ウィンドウで、プロジェクト用の新しいディレクトリを作成します。

        mkdir basicapp

2. npm init を実行して、パッケージの構造を初期化します。

        cd basicapp
        npm init

    npm init コマンドでは、プロジェクトを初期化するための一連の質問が示されます。以下の出力例を参照してください。

    ![npm init の出力][0]

3. npm リポジトリから express および azure-mobile-apps ライブラリをインストールします。

        npm install --save express azure-mobile-apps

4. app.js ファイルを作成して、基本的なモバイル サーバーを実装します。

        var express = require('express'),
            azureMobileApps = require('azure-mobile-apps');

        var app = express(),
            mobile = azureMobileApps();

        // Important all tables in the 'tables' directory
        mobile.tables.import('./tables');
        mobile.api.import('./api');

        // Provide initialization of any tables that are statically defined
        mobile.tables.initialize().then(function () {
           // Add the mobile API so it is accessible as a Web API
           app.use(mobile);

           // Start listening on HTTP
           var port = process.env.PORT || 3000;
           app.listen(port, function () {
               console.log('Now listening on ', port)
           });
        });

他のサンプルについては、[GitHub リポジトリ](https://github.com/Azure/azure-mobile-apps-node/tree/master/samples)を参照してください。

## サーバー プロジェクトの更新

Mobile Apps は、Mobile Services ランタイムとほぼ同じ機能を持つ新しい [Mobile App Server SDK] を提供しますが、ユーザーがフル ランタイムを所有するようになったため、Mobile Apps では Node のバージョンやコードの更新を強制しません。上記の手順に従った場合、基本バージョンの Node モバイル ランタイムを使用できます。これで、テーブルと API ロジックの Mobile Service からモバイル アプリへの移動、サーバー構成のカスタマイズ、プッシュの有効化、認証構成などを開始できます。

### 基本構成

サーバーには多数の構成設定がありますが、さまざまな既定値を使用することで作業を簡単に開始できます。設定の多くは、[Azure ポータル]の **[データ]**、**[認証/承認]**、および **[プッシュ]** 設定メニューを介して、自動的に設定されます。ローカル開発では、データ、認証、およびプッシュを使用する場合に、ローカル開発環境の構成が必要になることがあります。

モバイル アプリ バックエンドの [アプリケーション設定] を使用して設定できる環境変数でサーバーを構成できます。

[構成オブジェクト](http://azure.github.io/azure-mobile-apps-node/global.html#configuration)を初期化子に渡すか、プロジェクトのルートに [azureMobile.js という名前のファイルを作成する](app-service-mobile-node-backend-how-to-use-server-sdk/#howto-config-localdev)ことで、Mobile Apps SDK をさらにカスタマイズできます。

### データとテーブルの操作

SDK には、作業を素早く簡単に開始できるようにするインメモリ データ プロバイダーが付属しています。インメモリ プロバイダーでは再始動時にすべてのデータが失われ、複数のインスタンス間の整合性が保たれないため、早い段階で SQL DB の使用に切り替える必要があります。

モバイル サービスから Mobile Apps へのビジネス ロジックの移動を開始するには、まず、使用するテーブルの名前に ".js" を付加した名前のファイルを `./tables` ディレクトリに作成します。モバイル アプリ テーブルの完全な例については、[GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/todo/tables/TodoItem.js) を参照してください。最も単純なバージョンは次のとおりです。

    var azureMobileApps = require('azure-mobile-apps');

    // Create a new table definition
    var table = azureMobileApps.table();

    module.exports = table;

`<tablename>.<operation>.js` ごとにロジックの一部の移植を開始するには、テーブルの関数が必要になります。たとえば、read 関数を追加するとします。

Mobile Service で、TodoItem テーブルと読み取り操作を使用して、ユーザー ID に基づいて項目をフィルター処理する場合は、以下のようになります。

    function(query, user, request) {
        query.where({ userId: user.userId});
        request.execute();
    }

Azure Mobile Apps テーブル コードに追加する関数は以下のようになります。

    table.read(function (context) {
        context.query.where({ userId: context.user.id });
        return context.execute();
    });

クエリ、ユーザー、要求は、コンテキストに結合されます。コンテキスト オブジェクト内では、以下のフィールドを使用できます。

| フィールド | 型 | 説明 |
| :------ | :--------------------- | :---------- |
| query | queryjs/Query | 解析された OData クエリ |
| id | string または number | 要求に関連付けられた ID |
| item | オブジェクト | 挿入または削除される項目 |
| req | express.Request | 現在の express 要求オブジェクト |
| res | express.Response | 現在の express 応答オブジェクト |
| data | data | 構成されたデータ プロバイダー |
| tables | function | 文字列テーブル名を受け取り、テーブル アクセス オブジェクトを返す関数 |
| ユーザー | auth/user | 認証されたユーザー オブジェクト |
| results | オブジェクト | 実行操作の結果 |
| push | NotificationHubService | Notification Hubs Service (構成されている場合) |

詳細については、[現在の API ドキュメント](http://azure.github.io/azure-mobile-apps-node)をご覧ください。

### CORS

SDK の [CORS 構成設定](http://azure.github.io/azure-mobile-apps-node/global.html#corsConfiguration)を使用して、CORS を有効にすることができます。

CORS を使用する場合の主な懸念事項は、クライアント SDK が適切に機能するように `eTag` と `Location` ヘッダーを許可する必要があることです。

### プッシュ通知

Mobile Services 以降、Azure Notification Hubs SDK では大幅に更新されているものがいくつかあるため、一部の Notification Hubs 関数シグネチャが異なる場合があります。それ以外の場合、機能は Mobile Services と似ています。Azure Mobile SDK は、Notifications Hubs の [アプリケーション設定] が存在する場合、Notifications Hubs インスタンスをプロビジョニングし、`context.push` でそれを公開します。サンプルは [GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/push-on-insert/tables/TodoItem.js) にありますが、該当するセクションを以下に示します。

    table.insert(function (context) {
        // For details of the Notification Hubs JavaScript SDK,
        // see https://azure.microsoft.com/ja-JP/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/
        logger.silly('Running TodoItem.insert');

        // This push uses a template mechanism, so we need a template/
        var payload = '<toast><visual><binding template="Toast01"><text id="1">INSERT</text></binding></visual></toast>';

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    context.push.wns.send(null, payload, 'wns/toast', function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.silly('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
    });


### スケジュールされたジョブ
スケジュールされたジョブは、Mobile Apps には組み込まれません。そのため、Mobile Service バックエンド内にある既存のジョブは個別にアップグレードする必要があります。1 つの方法としては、スケジュールされた [Web ジョブ]をモバイル アプリ コード サイトで作成します。また、ジョブ コードを保持する API をセットアップし、予期したスケジュールどおりにそのエンドポイントをヒットするように [Azure Scheduler] を構成することもできます。

## <a name="authentication"></a>認証に関する考慮事項

Mobile Services の認証コンポーネントは、App Service の認証/承認機能に移動されました。これをサイトに対して有効にする方法の詳細については、[モバイル アプリへの認証の追加](app-service-mobile-ios-get-started-users.md)に関するトピックを参照してください。

AAD、Facebook、Google などの一部のプロバイダーでは、コピー アプリケーションから既存の登録を利用できます。ID プロバイダーのポータルに移動して、新しいリダイレクト URL を登録に追加するだけです。その後、クライアント ID とシークレットを使用して、App Service の認証/承認を構成します。

### コントローラー アクションの認証とユーザー ID

テーブルへのアクセスを制限する場合、`table.access = 'authenticated';` を使用してテーブル レベルで設定することができます。完全な例については、[GitHub](https://github.com/Azure/azure-mobile-apps-node/blob/master/samples/authentication/tables/TodoItem.js) を参照してください。

[ここ](http://azure.github.io/azure-mobile-apps-node/module-azure-mobile-apps_auth_user.html#~getIdentity)で説明されている `user.getIdentity` メソッドを使用すれば、ユーザー ID 情報にアクセスできます。

## <a name="updating-clients"></a>クライアントの更新
モバイル アプリ バックエンドを運用している場合は、それを利用する新しいバージョンのクライアント アプリケーションを使用できます。Mobile Apps には新しいバージョンのクライアント SDK も含まれ、上記のサーバー アップグレードと同様に、Mobile Apps バージョンをインストールする前に Mobile Services SDK へのすべての参照を削除する必要があります。

バージョン間での主な変更の 1 つは、コンストラクターでアプリケーション キーが不要になったことです。現在は、モバイル アプリの URL を渡すだけです。たとえば、.NET クライアントの場合、現在の `MobileServiceClient` コンストラクターは以下のようになります。

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

新しい SDK のインストールおよび新しい構造の使用については、以下のリンクを介して参照できます。

- [iOS バージョン 3.0.0 以降](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) バージョン 2.0.0 以降](app-service-mobile-dotnet-how-to-use-client-library.md)

アプリケーションでプッシュ通知を使用する場合は、変更されている内容もあるため、プラットフォームごとに固有の登録手順をメモしてください。

新しいクライアント バージョンの準備ができたら、アップグレードしたサーバー プロジェクトで試してみます。機能することを確認したら、新しいバージョンのアプリケーションを顧客にリリースできます。最後に、顧客がこれらの更新プログラムを受け取ったら、Mobile Services バージョンのアプリを削除できます。これで、最新の Mobile Apps サーバー SDK を使用する App Service モバイル アプリに完全にアップグレードできました。

<!-- Images -->
[0]: ./media/app-service-mobile-node-backend-how-to-use-server-sdk/npm-init.png

<!-- URLs. -->

[Azure portal]: https://portal.azure.com/
[Azure classic portal]: https://manage.windowsazure.com/
[Mobile Apps とは]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ja-JP/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: https://www.npmjs.com/package/azure-mobile-apps
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /ja-JP/documentation/services/scheduler/
[Web ジョブ]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service の価格]: https://azure.microsoft.com/ja-JP/pricing/details/app-service/
[.NET server SDK overview]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

[Azure ポータル]: https://portal.azure.com/
[OData]: http://www.odata.org
[Promise]: https://developer.mozilla.org/ja-JP/docs/Web/JavaScript/Reference/Global_Objects/Promise
[basicapp sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/basic-app
[todo sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/todo
[samples directory on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples
[static-schema sample on GitHub]: https://github.com/azure/azure-mobile-apps-node/tree/master/samples/static-schema
[QueryJS]: https://github.com/Azure/queryjs
[Node.js Tools 1.1 for Visual Studio]: https://github.com/Microsoft/nodejstools/releases/tag/v1.1-RC.2.1
[mssql Node.js package]: https://www.npmjs.com/package/mssql
[Microsoft SQL Server 2014 Express]: http://www.microsoft.com/ja-JP/server-cloud/Products/sql-server-editions/sql-server-express.aspx
[ExpressJS Middleware]: http://expressjs.com/guide/using-middleware.html
[Winston]: https://github.com/winstonjs/winston

<!---HONumber=AcomDC_1223_2015-->