---
title: 方法:カスタム コマンド パラメーターに検証を追加する (プレビュー)
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドで検証をパラメーターに追加する方法について説明します。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: cf6e4e4f0bfab43fb738f8415022e55fcbcbd05a
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156456"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>方法:カスタム コマンド パラメーターに検証を追加する (プレビュー)

この記事では、パラメーターに検証を追加し、修正を求める方法について説明します。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。

- [クイック スタート: カスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-new.md)
- [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature コマンドを作成する

検証を実演する目的で、ユーザーが気温を設定できる新しいコマンドを作成してみましょう。

1. [Speech Studio](https://speech.microsoft.com/) で前に作成したカスタム コマンド アプリケーションを開きます。
1. 新しいコマンド **SetTemperature** を作成する
1. ターゲット温度のパラメーターを追加する
1. 温度パラメーターの検証を追加する
   > [!div class="mx-imgBorder"]
   > ![範囲検証を追加する](media/custom-speech-commands/validations-add-temperature.png)

   | 設定           | 推奨値                                          | 説明                                                                                      |
   | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
   | Name              | 気温                                              | コマンド パラメーターのわかりやすい名前                                                    |
   | 必須          | true                                                     | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス |
   | 応答テンプレート | "- What temperature would you like?" (何度にしますか。)                     | 理解されていないとき、このパラメーターの値を問うプロンプト                              |
   | 種類              | Number                                                   | Number、String、Date Time など、パラメーターの型                                      |
   | 検証        | 最小値:60、最大値:80                             | Number パラメーターの場合、パラメーターに許容される値の範囲                             |
   | 応答テンプレート | "- Sorry, I can only set between 60 and 80 degrees" (申し訳ありません、設定できる範囲は 60 から 80 度までです)      | 検証が失敗した場合、別の値を要求するプロンプト                                       |

1. 例文をいくつか追加する

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 結果を確定する完了ルールを追加する

   | 設定    | 推奨値                                           | 説明                                        |
   | ---------- | --------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前  | 確認メッセージ                                      | ルールの目的を説明する名前          |
   | 条件 | 必須のパラメーター - Temperature                          | ルールを実行できるタイミングを決定する条件    |
   | アクション    | SpeechResponse - "- Ok, setting to {Temperature} degrees" (了解です。{Temperature} 度に設定します) | ルール条件が真のときに実行するアクション |

> [!TIP]
> この例では、音声応答を利用して結果を確定します。 クライアント アクションでコマンドを完了する例については、「[方法: Speech SDK を使用し、クライアントでコマンドを実行する (プレビュー)](./how-to-custom-speech-commands-fulfill-sdk.md)」をご覧ください

## <a name="try-it-out"></a>試してみる

テスト パネルを選択し、いくつか対話を試します。

- 次の内容を入力します。Set the temperature to 72 degrees (温度を 72 度に設定して)
- 出力:"Ok, setting to 72 degrees" (了解です。72 度に設定します)

- 次の内容を入力します。Set the temperature to 72 degrees (温度を 45 度に設定して)
- 出力:"Sorry, I can only set between 60 and 80 degrees" (申し訳ありません、設定できる範囲は 60 から 80 度までです)
- 入力: make it 72 degrees instead (それでは、72 度にして)
- 出力:"Ok, setting to 72 degrees" (了解です。72 度に設定します)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: カスタム コマンドに確認を追加する (プレビュー)](./how-to-custom-speech-commands-confirmations.md)
