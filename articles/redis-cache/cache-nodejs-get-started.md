<properties
	pageTitle="Node.js で Azure Redis Cache を使用する方法 | Microsoft Azure"
	description="Node.js と node_redis を使用して Azure Redis Cache を使用します。"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# Node.js で Azure Redis Cache を使用する方法

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Azure Redis Cache を使用すると、Microsoft が管理している、セキュリティで保護された専用 Redis Cache にアクセスできます。キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Node.js を使用して Azure Redis Cache を使用する方法を説明しています。Node.js で Azure Redis Cache を使用する別の例については、Azure Web サイトでの [Socket.IO を使用した Node.js チャット アプリケーションの構築](../app-service-web/web-sites-nodejs-chat-app-socketio.md)に関するページを参照してください。


## 前提条件

次のコマンドで、[node\_redis](https://github.com/mranney/node_redis) をインストールします。

    npm install redis

このチュートリアルでは [node\_redis](https://github.com/mranney/node_redis) を使用しますが、[http://redis.io/clients](http://redis.io/clients) に記載されている任意の Node.js クライアントを使用できます。

## Azure で Redis Cache を作成する

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## ホスト名とアクセス キーを取得する

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 非 SSL エンドポイントを有効にする

一部の Redis クライアントは SSL をサポートしていないため、既定では、[新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています](cache-configure.md#access-ports)。この記事の執筆時には、[node\_redis](https://github.com/mranney/node_redis) クライアントが SSL をサポートしていません。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## キャッシュに何か追加し、取得する

	  var redis = require("redis");
	
	  // Add your cache name and access key.
	var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});
	
	client.set("key1", "value", function(err, reply) {
		    console.log(reply);
		});
	
	client.get("key1",  function(err, reply) {
		    console.log(reply);
		});

出力:

	OK
	value


## 次のステップ

- [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。
- 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。

<!---HONumber=AcomDC_0601_2016-->