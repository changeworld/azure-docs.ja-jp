---
title: メディア占有ユニット (MRU) をスケーリングする - CLI
description: このトピックでは、CLI を使用して、Azure Media Services でのメディア処理をスケーリングする方法について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 06c0c6333b84697415ef598d4c5e853d5c006f08
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870162"
---
# <a name="how-to-scale-media-reserved-units"></a>メディア占有ユニットをスケーリングする方法

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、エンコードを高速化するためにメディア占有ユニット (MRU) をスケーリングする方法について説明します。

> [!WARNING]
> このコマンドは、API の 2020-05-01 バージョン以降で作成された Media Services アカウントでは機能しなくなります。 これらのアカウントでは、メディア占有ユニットは不要になりました。負荷に基づいてシステムによって自動的にスケールアップとスケールダウンが行われるためです。 Azure portal で MRU を管理するオプションが表示されない場合は、2020-05-01 API 以降で作成されたアカウントを使用しています。

## <a name="prerequisites"></a>前提条件

[Media Services アカウントを作成する](./create-account-howto.md)

[メディア占有ユニット](concept-media-reserved-units.md)について理解する。

## <a name="scale-media-reserved-units-with-cli"></a>CLI を使用してメディア占有ユニットをスケーリングする

`mru` コマンドを実行します。

次の [az ams account mru](/cli/azure/ams/account/mru) コマンドでは、"amsaccount" アカウントのメディア占有ユニットを、**count** パラメーターと **type** パラメーターを使用して設定しています。

```azurecli
az ams account mru set -n amsaccount -g amsResourceGroup --count 10 --type S3
```

## <a name="billing"></a>課金

アカウントにメディア占有ユニットがプロビジョニングされた時間 (分) に基づいて課金されます。 課金は、アカウントで実行中のジョブがあるかどうかとは無関係に発生します。 詳しくは、[Media Services の価格詳細](https://azure.microsoft.com/pricing/details/media-services/)ページの FAQ のセクションをご覧ください。   

## <a name="next-step"></a>次のステップ

[ビデオを分析する](analyze-videos-tutorial-with-api.md)

## <a name="see-also"></a>関連項目

* [クォータと制限](limits-quotas-constraints.md)
