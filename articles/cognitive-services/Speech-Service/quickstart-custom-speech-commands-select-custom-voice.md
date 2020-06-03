---
title: クイック スタート:Custom Voice でカスタム コマンドを使用する (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションの出力音声を指定します。
services: cognitive-services
author: anhoang
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: anhoang
ms.openlocfilehash: c2b9b4d51e89975d988ed94bf85695bd8a1cc770
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872475"
---
# <a name="quickstart-use-custom-commands-with-custom-voice-preview"></a>クイック スタート:Custom Voice でカスタム コマンドを使用する (プレビュー)

[前の記事](./quickstart-custom-speech-commands-create-parameters.md)では、パラメーターのあるコマンドに応答する新しいカスタム コマンド プロジェクトを作成しました。

この記事では、作成したアプリケーションのカスタム出力音声を選択します。

## <a name="select-a-custom-voice"></a>Custom Voice を選択する

1. [前に作成した](./quickstart-custom-speech-commands-create-parameters.md)プロジェクトを開きます
1. 左側のウィンドウで **[設定]** を選択します
1. 中央のウィンドウから **[Custom Voice]** を選択します
1. テーブルから目的のカスタムまたはパブリック音声を選択します
1. **[保存]** を選びます。

> [!div class="mx-imgBorder"]
> ![例文とパラメーター](media/custom-speech-commands/select-custom-voice.png)

> [!NOTE]
> - **パブリック音声**の場合、**ニューラル型**は特定のリージョンでのみ使用できます。 使用できるかどうかを確認するには、[リージョンまたはエンドポイントによる標準およびニューラル音声](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)に関するページを参照してください。
> - **カスタム音声**については、Custom Voice プロジェクト ページから作成できます。 「[Custom Voice の概要](./how-to-custom-voice.md)」を参照してください。

これで、アプリケーションは既定の音声ではなく、選択した音声で応答します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [クイック スタート: Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md)

