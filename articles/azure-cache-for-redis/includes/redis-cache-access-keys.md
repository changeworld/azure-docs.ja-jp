---
title: インクルード ファイル
description: インクルード ファイル
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: c1f5aae79daf4cf8fb3a447eba5538212e3dc327
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533798"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Azure portal からホスト名、ポート、アクセス キーを取得する

Azure Cache for Redis のインスタンスに接続するには、キャッシュ クライアントにキャッシュのホスト名、ポート、およびキーが必要です。 クライアントによっては、これらの項目を参照するための名前が若干異なる場合があります。 ホスト名、ポート、およびキーは [Azure portal](https://portal.azure.com) から取得できます。

- アクセス キーを取得するには、キャッシュの左側のナビゲーションから **[アクセス キー]** を選択します。 
  
  ![Azure Cache for Redis のキー](media/redis-cache-access-keys/redis-cache-keys.png)

- ホスト名とポートを取得するには、キャッシュの左側のナビゲーションから **[プロパティ]** を選択します。 ホスト名は、 *\<DNS name>.redis.cache.windows.net* の形式になります。

  ![Azure Cache for Redis のプロパティ](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

