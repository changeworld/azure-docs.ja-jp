<properties
	pageTitle="Python で Azure Redis Cache を使用する方法 | Microsoft Azure"
	description="Python を使用して Azure Redis Cache を使用します"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="05/31/2016"
	ms.author="sdanie"/>

# Python で Azure Redis Cache を使用する方法

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

このトピックでは、Python を使用して Azure Redis Cache を使用する方法を説明しています。


## 前提条件

[redis-py](https://github.com/andymccurdy/redis-py) をインストールします。


## Azure で Redis Cache を作成する

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## ホスト名とアクセス キーを取得する

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## 非 SSL エンドポイントを有効にする

一部の Redis クライアントは SSL をサポートしていないため、既定では、[新しい Azure Redis Cache インスタンスに対して非 SSL ポートは無効になっています](cache-configure.md#access-ports)。この記事の執筆時には、[redis-py](https://github.com/andymccurdy/redis-py) クライアントが SSL をサポートしていません。

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## キャッシュに何か追加し、取得する


	>>> import redis
	>>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
	      port=6380, db=0, password='<key>', ssl=True)
	>>> r.set('foo', 'bar')
	True
	>>> r.get('foo')
	b'bar'


`<name>` を実際のキャッシュ名、`key` を実際のアクセス キーに置き換えます。


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0601_2016-->