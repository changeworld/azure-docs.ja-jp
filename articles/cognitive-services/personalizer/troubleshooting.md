---
title: トラブルシューティング - Personalizer
titleSuffix: Azure Cognitive Services
description: この記事では、Personalizer についてよく寄せられる質問とその回答を示します。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 5911cba54d7dd0eb1a5621112b41e2fc40fa68eb
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953175"
---
# <a name="personalizer-troubleshooting"></a>Personalizer のトラブルシューティング

この記事では、Personalizer についてよく寄せられる質問とその回答を示します。

## <a name="learning-loop"></a>学習ループ

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学習ループで学習が行われていないようです。 これをどのように修正すればよいですか?

学習ループでは、ランキング呼び出しによる優先順位の設定を効果的に行う前に、数千の報酬呼び出しが必要です。 

学習ループの現在の動作に不安がある場合は、[オフライン評価](concepts-offline-evaluation.md)を実行し、修正された学習ポリシーを適用してください。 

## <a name="next-steps"></a>次の手順

[モデルの更新頻度を構成する](how-to-settings.md#model-update-frequency)