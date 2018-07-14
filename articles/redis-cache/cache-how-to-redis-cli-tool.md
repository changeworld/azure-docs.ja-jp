---
title: Azure Redis Cache で redis-cli を使用する方法 | Microsoft Docs
description: Azure Redis Cache で redis-clis を使用する方法を説明します｡
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299093"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Azure Redis Cache で Redis コマンドライン ツールを使用する方法

*redis-cli.exe* は､クライアントとして Redis Cache と対話するための一般的なツールです｡ このツールは､Azure Redis Cache で利用することもできます｡

Windows プラットフォームでこのツールを利用するには､[Redis command-line tools for Windows](https://github.com/MSOpenTech/redis/releases/) をダウンロードします｡ 

別のプラットフォームからこのコマンドライン ツールを実行するには､[http://redis.io/download](https://redis.io/download) から Redis Cache をダウンロードします｡

## <a name="gather-cache-access-information"></a>キャッシュ アクセス情報を収集する

キャッシュにアクセスするために必要な情報を収集する方法は 3 通りあります｡

1. Azure CLI から [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) を使用する
2. Azure PowerShell から [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1) を使用する
3. Azure Portal を使用する

このセクションでは､Azure Portal からキーを取得します｡

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>redis-cli.exe に対するアクセスを有効化する

Azure Redis Cache では､既定では SSL ポート (6380) のみ有効化されています｡ `redis-cli.exe` コマンドライン ツールは SSL をサポートしていません｡ SSL を使用するための構成上の選択肢は 2 通りあります｡

1. [Enable the non-SSL port (6379)](cache-configure.md#access-ports) -  **アクセス キーがクリア テキスト形式で TCP を使って送信されるため､** この構成はお勧めしません｡ この変更を行うと､キャッシュに対するアクセスが危うくなる可能性があります｡ この構成を検討する唯一のシナリオは､テスト用 キャッシュにアクセスするだけの場合です｡

2. [stunnel](https://www.stunnel.org/downloads.html) ダウンロードして､インストールします｡

    **stunnel GUI Start** を実行してサーバーを起動する

    タスクバーにある stunnel サーバーのアイコンを右クリックして､**Show Log Window** をクリックします｡

    stunnel ログ ウィンドウのメニューで **Configuration** > **Edit Configuration** をクリックして現在の構成ファイルを開きます｡

    **Service definitions** の部分に以下の *redis-cli.exe* 用のエントリを追加します｡ `yourcachename` の部分は実際のキャッシュ名に置き換えてください｡ 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    構成ファイルを保存して閉じます｡ 
  
    stunnel ログ ウィンドウのメニューで **Configuration** > **Reload Configuration** をクリックします｡


## <a name="connect-using-the-redis-command-line-tool"></a>Redis コマンドライン ツールで接続する

stunnel を使用してキャッシュに接続するには､*redis-cli.exe* を起動して､*ポート* と *アクセス キー* (プライマリかセカンダリ) のに指定します｡

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel と redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

**安全度の低い**非 SSL ポート経由でテスト用キャッシュを使用する場合は､`redis-cli.exe` を実行して､*ホスト名*と*ぽーと*､*アクセス キー* (プライマリかセカンダリ) を指定することでテスト用キャッシュに接続できます｡

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel と redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>次の手順

[Redis Console](cache-configure.md#redis-console) を使用してコマンドを発行する

