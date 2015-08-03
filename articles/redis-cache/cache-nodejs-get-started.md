<properties
   pageTitle="Node.js で Azure Redis Cache を使用する方法"
   description="Node.js と node_redis を使用して Azure Redis Cache を使用します。"
   services="redis-cache"
   documentationCenter=""
   authors="MikeWasson"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="nodejs"
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Node.js で Azure Redis Cache を使用する方法

Azure Redis Cache を使用すると、Microsoft が管理している、セキュリティで保護された専用 Redis Cache にアクセスできます。キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Node.js を使用して Azure Redis Cache を使用する方法を説明しています。Node,js で Azure Redis Cache を使用する別の例については、Azure Web サイトでの [Socket.IO を使用した Node.js チャット アプリケーションの構築][]に関するページを参照してください。


## 前提条件

次のコマンドで、[node_redis](https://github.com/mranney/node_redis) をインストールします。

    npm install redis

このチュートリアルでは [node_redis](https://github.com/mranney/node_redis) を使用しますが、[http://redis.io/clients](http://redis.io/clients) に記載されている任意の Node.js クライアントを使用できます。

## Azure で Redis Cache を作成する

[Azure の管理ポータル](http://go.microsoft.com/fwlink/?LinkId=398536)で、**[新規]**、**[データ + ストレージ]** をクリックし、**[Redis Cache]** を選択します。

  ![][1]

DNS ホスト名を入力します。フォーム `<name>.redis.cache.windows.net` が表示されます。**[作成]** をクリックします。

  ![][2]


キャッシュが作成されたら、ポータルでそのキャッシュをクリックして設定を表示します。**[キー]** の下のリンクをクリックして、プライマリ キーをコピーします。このキーは、要求を認証するのに必要になります。

  ![][4]


## 非 SSL エンドポイントを有効にする


**[ポート]** の下のリンクをクリックし、[SSL 経由でのみアクセスを許可する] に対して **[いいえ]** をクリックします。これにより、キャッシュに対して非 SSL ポートが有効になります。現在、node_redis クライアントは SSL をサポートしていません。

  ![][3]


## キャッシュに何か追加し、取得する

	var redis = require("redis");

    // Put in your cache name and access key.
	var client = redis.createClient(6379,'<name>.redis.cache.windows.net', {auth_pass: '<key>' });

	client.set("foo", "bar", function(err, reply) {
	    console.log(reply);
	});

	client.get("foo",  function(err, reply) {
	    console.log(reply);
	});


出力:

	OK
	bar


## 次のステップ

- [キャッシュ診断の有効化](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics)によってキャッシュの正常性を[監視](https://msdn.microsoft.com/library/azure/dn763945.aspx)できるようにします。
- 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。


<!--Image references-->
[1]: ./media/cache-nodejs-get-started/cache01.png
[2]: ./media/cache-nodejs-get-started/cache02.png
[3]: ./media/cache-nodejs-get-started/cache03.png
[4]: ./media/cache-nodejs-get-started/cache04.png

[Socket.IO を使用した Node.js チャット アプリケーションの構築]: ../app-service-web/web-sites-nodejs-chat-app-socketio.md

<!---HONumber=July15_HO4-->