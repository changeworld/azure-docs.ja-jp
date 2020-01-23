---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76122746"
---
## <a name="change-the-model-update-frequency"></a>モデルの更新頻度を変更する

Azure portal で、Personalizer リソースの **[構成]** ページで、 **[モデルの更新頻度]** を 10 秒に変更します。 この短い時間でサービスが迅速にトレーニングされるようになり、繰り返しのたびに上位のアクションが変化する様子を確認できます。

![モデルの更新頻度を変更する](../media/settings/configure-model-update-frequency-settings.png)

Personalizer ループを初めてインスタンス化したときには、トレーニング元となる Reward API 呼び出しがないため、モデルは存在しません。 Rank 呼び出しは、各項目に対して等しい確率を返します。 それでも、アプリケーションは、RewardActionId の出力を使用して、常にコンテンツをランク付けする必要があります。