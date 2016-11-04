---
title: Node.js で Azure Redis Cache を使用する方法 | Microsoft Docs
description: Node.js と node_redis を使用して Azure Redis Cache を使用します。
services: redis-cache
documentationcenter: ''
author: steved0x
manager: douge
editor: v-lincan

ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/16/2016
ms.author: sdanie

---
# Node.js で Azure Redis Cache を使用する方法
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.JS](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Azure Redis Cache を使用すると、Microsoft が管理している、セキュリティで保護された専用 Redis Cache にアクセスできます。キャッシュは、Microsoft Azure 内の任意のアプリケーションからアクセスできます。

このトピックでは、Node.js を使用して Azure Redis Cache を使用する方法を説明しています。Node.js で Azure Redis Cache を使用する別の例については、Azure Web サイトでの [Socket.IO を使用した Node.js チャット アプリケーションの構築](../app-service-web/web-sites-nodejs-chat-app-socketio.md)に関するページを参照してください。

## 前提条件
次のコマンドで、[node\_redis](https://github.com/mranney/node_redis) をインストールします。

    npm install redis

このチュートリアルでは、[node\_redis](https://github.com/mranney/node_redis) を使用します。他の Node.js クライアントを使用する例については、[Node.js Redis クライアント](http://redis.io/clients#nodejs)に関するセクションに記載されている Node.js クライアントの個々のドキュメントを参照してください。

## Azure で Redis Cache を作成する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## ホスト名とアクセス キーを取得する
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## SSL を使用してキャッシュに安全に接続する
[node\_redis](https://github.com/mranney/node_redis) の最新のビルドでは、SSL を使用した Azure Redis Cache への接続をサポートしています。次の例では、SSL エンドポイント 6380 を使用して Azure Redis Cache に接続する方法を示しています。`<name>` をキャッシュの名前に、`<key>` を前の「[ホスト名とアクセス キーを取得する](#retrieve-the-host-name-and-access-keys)」セクションで説明したプライマリまたはセカンダリ キーに置き換えます。

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});


## キャッシュに何か追加し、取得する
次の例では、Azure Redis Cache インスタンスに接続し、キャッシュから項目を格納および取得する方法を示しています。Redis と [node\_redis](https://github.com/mranney/node_redis) クライアントを使用する他の例については、[http://redis.js.org/](http://redis.js.org/) を参照してください。

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
* [キャッシュ診断の有効化](cache-how-to-monitor.md#enable-cache-diagnostics)によってキャッシュの正常性を[監視](cache-how-to-monitor.md)できるようにします。
* 公式の [Redis ドキュメント](http://redis.io/documentation)を読みます。

<!---HONumber=AcomDC_0817_2016-->