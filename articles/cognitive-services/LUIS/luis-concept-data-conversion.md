---
title: LUIS におけるデータ変換の概念の概要 - Azure | Microsoft Docs
description: Language Understanding (LUIS) の予測の前に、発話の変更方法について説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/27/2018
ms.author: diberry
ms.openlocfilehash: 05a02191af171936dd9f5c4bf469b9c84aa144a3
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39223703"
---
# <a name="data-conversion-concepts-in-luis"></a>LUIS におけるデータ変換の概念
LUIS には、予測の前に、音声の発話からテキストの発話へと変換する機能があります。 

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

