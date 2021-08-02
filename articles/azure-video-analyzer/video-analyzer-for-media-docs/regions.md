---
title: Azure Video Analyzer for Media (旧称 Video Indexer) が利用可能なリージョン - Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: この記事では、Azure Video Analyzer for Media (旧称 Video Indexer) が利用可能な Azure リージョンについて説明します。
services: media-services
author: Juliako
manager: femila
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 900a8b1b8fdc35997e0ad7ee09430f4eb33a4d9e
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110387616"
---
# <a name="azure-regions-in-which-video-analyzer-for-media-exists"></a>Video Analyzer for Media が存在する Azure リージョン

Azure Video Analyzer for Media (旧称 Video Indexer) API には、呼び出しがルーティングされる Azure リージョンを設定する必要がある **location** パラメーターがあります。 これは、[Video Analyzer for Media が利用可能な Azure リージョン](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all)にする必要があります。

## <a name="locations"></a>場所

`location` パラメーターには、その値として Azure リージョンのコード名を指定する必要があります。 Video Analyzer for Media をプレビュー モードで使用する場合は、値として `"trial"` を指定する必要があります。 `trial` は、`location` パラメーターの既定値です。 それ以外の場合は、お使いのアカウントが存在し、呼び出しがルーティングされる Azure リージョンのコード名を取得するために、Azure portal を使用するか、[Azure CLI](/cli/azure) コマンドを実行できます。

### <a name="azure-portal"></a>Azure portal

1. [Video Analyzer for Media](https://www.videoindexer.ai/) Web サイトにサインインします。
1. ページの右上隅にある **[ユーザー アカウント]** を選択します。
1. 右上隅で、アカウントの場所を見つけます。  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="場所":::
    
###  <a name="cli-command"></a>CLI コマンド

```azurecli-interactive
az account list-locations
```

上記の行を実行すると、すべての Azure リージョンの一覧が取得されます。 探している *displayName* がある Azure リージョンに移動し、その *name* 値を **location** パラメーターで使用します。

たとえば、Azure リージョンの米国西部 2 の場合 (下に示されています)、"westus2" を **location** パラメーターで使用します。

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>次のステップ

- [API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)
- [API を使用してブランド モデルをカスタマイズする](customize-brands-model-with-api.md)
- [API を使用して人物モデルをカスタマイズする](customize-person-model-with-api.md)
