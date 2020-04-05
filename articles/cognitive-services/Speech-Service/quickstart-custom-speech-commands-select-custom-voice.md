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
ms.openlocfilehash: b0ecbc4dc030fa8e7fbe362c1304c3c97278bdf5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75453334"
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
> カスタム音声は、Custom Voice プロジェクト ページから作成できます。 **[Speech Studio]** リンクを選択し、 **[Custom Voice]** を選択して開始します。

これで、アプリケーションは既定の音声ではなく、選択した音声で応答します。

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [クイック スタート: Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md)

