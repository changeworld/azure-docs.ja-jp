---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "73720378"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Azure portal からホスト名、ポート、アクセス キーを取得する

Azure Cache for Redis のインスタンスに接続するには、キャッシュ クライアントにキャッシュのホスト名、ポート、およびキーが必要です。 クライアントによっては、これらの項目を参照するための名前が若干異なる場合があります。 ホスト名、ポート、およびキーは [Azure portal](https://portal.azure.com) から取得できます。

- アクセス キーを取得するには、キャッシュの左側のナビゲーションから **[アクセス キー]** を選択します。 
  
  ![Azure Cache for Redis のキー](media/redis-cache-access-keys/redis-cache-keys.png)

- ホスト名とポートを取得するには、キャッシュの左側のナビゲーションから **[プロパティ]** を選択します。 ホスト名は、 *\<DNS 名>.redis.cache.windows.net* の形式になります。

  ![Azure Cache for Redis のプロパティ](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

