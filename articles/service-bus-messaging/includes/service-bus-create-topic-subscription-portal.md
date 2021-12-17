---
title: インクルード ファイル
description: インクルード ファイル
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 10/11/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 491abcae4bf73d6b2dcd8172fcb1b69cb7b60d32
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129807491"
---
## <a name="create-a-topic-using-the-azure-portal"></a>Azure Portal を使用したトピックの作成
1. **[Service Bus 名前空間]** ページで、左側のメニューの **[トピック]** を選択します。
2. ツール バーの **[+ トピック]** を選択します。 
4. トピックの **名前** を入力します。 他のオプションは既定値のままにしてください。
5. **[作成]** を選択します。

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-topic.png" alt-text="[トピックの作成] ページを示す画像。":::

## <a name="create-a-subscription-to-the-topic"></a>トピックに対するサブスクリプションの作成
1. 前のセクションで作成した **トピック** を選択します。 
    
    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/select-topic.png" alt-text="トピックの一覧から選択したトピックを示す画像。":::
2. **[Service Bus トピック]** ページで、ツール バーの **[+ サブスクリプション]** を選択します。 

    :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/add-subscription-button.png" alt-text="[サブスクリプションの追加] ボタンを示す画像。":::    
3. **[サブスクリプションの作成]** ページで、次の手順に従います。
    1. サブスクリプションの **名前** として「**S1**」と入力します。
    1. **[最大配信数]** に「**3**」と入力します。
    1. 次に、 **[作成]** を選択してサブスクリプションを作成します。 

        :::image type="content" source="./media/service-bus-create-topics-subscriptions-portal/create-subscription-page.png" alt-text="[サブスクリプションの作成] ページを示す画像。":::
