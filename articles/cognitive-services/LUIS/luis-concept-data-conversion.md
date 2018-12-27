---
title: LUIS でのデータ変換の概念 - Language Understanding
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) の予測の前に、発話の変更方法について説明します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 9324f7b4f7bed844f16d17b8960878892be4b165
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638394"
---
# <a name="data-conversion-concepts-in-luis"></a>LUIS におけるデータ変換の概念
LUIS では、Cognitive Services の音声サービスを使用して、予測の前に音声の発話からテキストの発話に発話を変換します。 

## <a name="speech-to-intent-conversion-concepts"></a>音声から意図への変換の概念
LUIS の音声テキスト変換を使用すると、音声の発話をエンドポイントに送信し、LUIS の予測応答を受け取ることができます。 このプロセスは、[音声](https://docs.microsoft.com/azure/cognitive-services/Speech)サービスと LUIS の統合です。 

### <a name="key-requirements"></a>主な要件
この統合のために **Bing Speech API** キーを作成する必要はありません。 この統合には、Azure portal で作成された **Language Understanding** キーが使用されます。 LUIS スターター キーは使用しないでください。この統合には使用できません。

### <a name="new-endpoint"></a>新しいエンドポイント 
この統合で、新しいエンドポイントと[価格](luis-boundaries.md#key-limits)モデルが作成されます。 エンドポイントは、[Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) を介して、音声とテキストの両方の発話を受信することができるので、単一のエンドポイントとして使用できます。 

### <a name="quota-usage"></a>クォータの使用量
詳細については、[主な制限](luis-boundaries.md#key-limits)に関するページを参照してください。 

### <a name="data-retention"></a>データの保持
音声かテキストかに関係なく、Speech SDK 経由でエンドポイントに送信されるデータは、そのユーザーの音声モデルの強化にのみ使用されます。 ユーザーのモデル以外に、汎用的なキャパシティで音声または LUIS を強化するために使用されることはありません。 LUIS アプリを削除すると、保持されているデータも削除されます。

<!-- TBD: Machine translation conversion concepts -->

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [音声テキスト変換の使用](luis-tutorial-speech-to-intent.md)

