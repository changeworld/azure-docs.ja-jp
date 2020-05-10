---
title: Language Understanding の事前構築済みモデル
titleSuffix: Azure Cognitive Services
description: LUIS には一連の事前構築済みモデルが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013560"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>一般的な使用シナリオ向けに事前構築済みモデルを追加する 

LUIS には一連の事前構築済みモデルが含まれており、一般的な会話形式のユーザー シナリオを簡単に追加できます。 これは会話型クライアント アプリケーションに機能を追加する迅速かつ簡単な方法であり、これらの機能のモデルを設計する必要はありません。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>事前構築済みのドメインの追加

1. **[My Apps]\(マイ アプリ\)** ページでアプリを選択します。 アプリの **[Build]\(ビルド\)** セクションが開きます。 

1. 左側のツール バーから **[Prebuilt Domains]\(事前構築済みドメイン\)** を選択します。 

1. アプリに追加するドメインを見つけて、 **[Add domain]\(ドメインの追加\)** ボタンを選択します。

    > [!div class="mx-imgBorder"]
    > ![カレンダー事前構築済みドメインを追加する](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>事前構築済み意図を追加する

1. **[My Apps]\(マイ アプリ\)** ページでアプリを選択します。 アプリの **[Build]\(ビルド\)** セクションが開きます。 

1. **[Intents]]\(意図\)** ページで、意図一覧の上にあるツール バーの **[Add prebuilt domain intent]\(事前構築済みドメインの意図の追加\)** を選択します。 

1. ポップアップ ダイアログから **Utilities.Cancel** 意図を選択します。 

    > [!div class="mx-imgBorder"]
    > ![事前構築済み意図を追加する](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. **[Done]\(完了\)** ボタンを選択します。

## <a name="add-a-prebuilt-entity"></a>作成済みエンティティの追加

1. **[My Apps]\(マイ アプリ\)** ページで名前をクリックしてアプリを開き、左側で **[Entities]\(エンティティ\)** をクリックします。 

1. **[Entities]\(エンティティ\)** ページで **[Add prebuilt entity]\(事前構築済みのエンティティを追加する\)** をクリックします。

1. **[Add prebuilt entities]\(事前構築済みエンティティの追加\)** ダイアログ ボックスで、事前構築済みエンティティを選択します。 

    > [!div class="mx-imgBorder"]
    > ![[事前構築済みエンティティの追加] ダイアログ ボックス](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. **[Done]\(完了\)** を選択します。 エンティティを追加した後、アプリをトレーニングする必要はありません。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>発行して予測エンドポイントからあらかじめ構築されるモデルを表示する

事前構築済みモデルの値を見る最も簡単な方法は、公開されているエンドポイントに対してクエリを行うことです。 

## <a name="entities-containing-a-prebuilt-entity-token"></a>事前構築済みエンティティのトークンを含むエンティティ
 
事前構築済みエンティティによって制約される機械学習エンティティがある場合は、機械学習エンティティにサブコンポーネントを追加し、事前構築済みエンティティの制約を追加します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [REST API を使用して .csv からモデルを構築する](./luis-tutorial-node-import-utterances-csv.md)
