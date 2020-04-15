---
title: LUIS リソースの作成
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2020
ms.author: aahi
ms.openlocfilehash: ebfe7e80577b163218a7bc501fa4e3e9b206fd62
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879210"
---
<a name="create-luis-resources"></a>

## <a name="create-luis-resources-in-azure-portal"></a>Azure portal で LUIS リソースを作成する

1. [このリンク](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)を使用して、Azure portal で LUIS リソースの作成を開始します。

1. すべての必須設定を入力します。

    |名前|目的|
    |--|--|
    |サブスクリプション名| リソースに対して課金されるサブスクリプション。|
    |Resource group| 選択または作成するカスタム リソース グループ名。 リソース グループを使用すると、アクセスと管理のために Azure リソースをグループ化できます。|
    |名前| 選択したカスタム名。作成および予測エンドポイント クエリのカスタム サブドメインとして使用されます。|
    |オーサリングの場所|モデルに関連付けられている領域。|
    |オーサリングの価格レベル|価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。|
    |ランタイムの場所|発行される予測エンドポイント ランタイムに関連付けられているリージョン。|
    |ランタイム価格レベル|価格レベルによって、1 秒および 1 か月あたりの最大トランザクション数が決まります。|

    > [!div class="mx-imgBorder"]
    > [![Language Understanding リソースを作成する](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png)](../media/luis-how-to-azure-subscription/create-resource-in-azure-small.png#lightbox)

1. **[確認と作成]** をクリックして、リソースが作成されるまで待ちます。
1. 両方のリソースが作成されたら、引き続き Azure portal で新しい作成リソースを選択します。次に、 **[クイックスタート]** を選択して、プログラムで作成するための作成**エンドポイント URL** と**キー**を取得します。

> [!TIP]
> リソースを使用するには、LUIS ポータルで[リソースを割り当て](../luis-how-to-azure-subscription.md#assign-an-authoring-resource-in-the-luis-portal-for-all-apps)ます。