---
title: メディア占有ユニット (MRU) をスケーリングする - CLI
description: このトピックでは、CLI を使用して、Azure Media Services でのメディア処理をスケーリングする方法について説明します。
services: media-services
author: jiayali-ms
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 08/25/2021
ms.author: inhenkel
ms.openlocfilehash: b8dc092104f3371263719adbec16305afb87113c
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867383"
---
# <a name="how-to-scale-media-reserved-units-legacy"></a>メディア占有ユニットをスケーリングする方法 (レガシ)

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、エンコードを高速化するためにメディア占有ユニット (MRU) をスケーリングする方法について説明します。

> [!WARNING]
> このコマンドは、API の 2020-05-01 バージョン以降で作成された Media Services アカウントでは機能しなくなります。 これらのアカウントでは、メディア占有ユニットは不要になりました。負荷に基づいてシステムによって自動的にスケールアップとスケールダウンが行われるためです。 Azure portal で MRU を管理するオプションが表示されない場合は、2020-05-01 API 以降で作成されたアカウントを使用しています。
> この記事の目的は、MRU を使用する従来のプロセスを文書化することです。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./account-create-how-to.md)

[メディア占有ユニット](concept-media-reserved-units.md)について理解する。

## <a name="scale-media-reserved-units-with-cli"></a>CLI を使用してメディア占有ユニットをスケーリングする

`mru` コマンドを実行します。

次の [az ams account mru](/cli/azure/ams/account/mru) コマンドでは、"amsaccount" アカウントのメディア占有ユニットを、**count** パラメーターと **type** パラメーターを使用して設定しています。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>課金

 以前はメディア占有ユニットに対して料金が発生しましたが、2021 年 4 月 17 日時点では、メディア占有ユニット用に構成されているアカウントには料金がかかりません。

## <a name="see-also"></a>関連項目

* [Media Services v2 から v3 への移行](migrate-v-2-v-3-migration-introduction.md)
