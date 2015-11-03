<properties
	pageTitle="Python で Azure Redis Cache を使用する方法 | Microsoft Azure"
	description="Python を使用して Azure Redis Cache を使用します"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="dwrede"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="10/23/2015"
	ms.author="sdanie"/>

# Python で Azure Redis Cache を使用する方法

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

このトピックでは、Python を使用して Azure Redis Cache を使用する方法を説明しています。


## 前提条件

[redis-py](https://github.com/andymccurdy/redis-py) をインストールします。


## Azure で Redis Cache を作成する

[Azure プレビュー ポータル](http://go.microsoft.com/fwlink/?LinkId=398536)で、**[新規]**、**[データ + ストレージ]**、**[Redis Cache]** の順にクリックします。

  ![][1]

DNS ホスト名を入力します。フォーム `<name>.redis.cache.windows.net` が表示されます。**[作成]** をクリックします。

  ![][2]

キャッシュを作成したら、プレビュー ポータルでそのキャッシュをクリックして設定を表示します。次が必要です。

- **ホスト名**。 キャッシュを作成したときに入力した名前です。
- **ポート**。 **[ポート]** の下のリンクをクリックしてポートを表示します。SSL ポートを使用します。
- **アクセス キー**。 **[キー]** の下のリンクをクリックして、プライマリ キーをコピーします。

## キャッシュに何か追加し、取得する

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

*&lt;name&gt;* はキャッシュ名、*&lt;key&gt;* はアクセス キーに置き換えます。


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=Nov15_HO1-->