---
title: カスタム コマンド プレビューで検証を追加する - 音声サービス
titleSuffix: Azure Cognitive Services
description: カスタム コマンド プレビュー アプリで検証をコマンド パラメーターに追加する方法について説明します。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 6686016f109fad4ee8b7f4e494b1374a6003658c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310412"
---
# <a name="add-validations-to-a-command-parameter-in-a-custom-commands-preview-application"></a>カスタム コマンド プレビュー アプリケーションで検証をコマンド パラメーターに追加する

この記事では、パラメーターに検証を追加し、修正を求める方法について説明します。

## <a name="prerequisites"></a>前提条件

以下の記事の手順を完了してください。

> [!div class="checklist"]
 
> * [クイック スタート: カスタム コマンド プレビュー アプリを作成する](./quickstart-custom-speech-commands-create-new.md)
> * [クイック スタート: パラメーターを使用してカスタム コマンド プレビュー アプリを作成する](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature コマンドを作成する

検証例を確認するために、ユーザーが温度を設定できる新しいコマンドを作成します。

1. [Speech Studio](https://speech.microsoft.com/) で、作成したカスタム コマンド プレビュー アプリを開きます。
1. 新しい **SetTemperature** コマンドを作成します。
1. 次のような構成の温度パラメーターを追加します。

   | パラメーターの構成           | 推奨値    |説明                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | **名前**              | **気温**                       | パラメーターのわかりやすい名前                                |
   | **必須**          | オン                           | コマンドを完了するにはこのパラメーターの値が必須かどうかを示すチェック ボックス |
   | **Response for required parameter (必須パラメーターの応答)**     | **Simple editor -> What temperature would you like? (シンプルなエディター -> 好みの温度は何度ですか?)**  | 理解されていないとき、このパラメーターの値を問うプロンプト |
   | **Type**              | **Number**                            | Number、String、DateTime、Geography などのパラメーターの型   |

1. 温度パラメーターの検証を追加します。

    1. 温度パラメーターの **[Parameters]\(パラメーター\)** 構成ページで、 **[Validations]\(検証\)** セクションの **[Add a validation]\(検証の追加\)** を選択します。

    1. **[New validation]\(新しい検証\)** ポップアップ ウィンドウで、検証を次のように構成します。
  
       | パラメーターの構成         | 推奨値                                          | 説明                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | **最小値**        | **60**               | Number パラメーターの場合、このパラメーターで想定できる最小値 |
       | **最大値**        | **80**               | 数値パラメーターの場合、このパラメーターで想定できる最大値 |
       | **Failure response - Simple editor (失敗応答 - シンプルなエディター)**| **First Variation - Sorry, I can only set between 60 and 80 degrees (最初の検証 - 申し訳ありません、設定できる範囲は 60 から 80 度までです)**      | 検証が失敗した場合に新しい値の入力を求めるプロンプトを表示します                                       |

       > [!div class="mx-imgBorder"]
       > ![範囲検証を追加する](media/custom-speech-commands/validations-add-temperature.png)

1. **［作成］** を選択します

1. いくつかの文例を追加します。

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 以下の構成の完了ルールを追加します。 このルールにより、結果が確認されます。

   | 設定    | 推奨値                                           |説明                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名前       | 確認メッセージ                                      |ルールの目的を説明する名前 |
   | **条件** | **必須のパラメーター - Temperature**                       |ルールを実行できるタイミングを決定する条件    |   
   | **アクション**    | **音声応答の送信 - Ok, setting temperature to {Temperature} degrees (了解です。{Temperature} 度に設定します)** | ルール条件が真のときに実行するアクション |

> [!TIP]
> この例では、音声応答を利用して結果を確定します。 クライアント アクションでコマンドを完了する例については、[Speech SDK を使用してクライアント上でコマンドを実行する方法](./how-to-custom-speech-commands-fulfill-sdk.md)に関するページを参照してください。

## <a name="try-it-out"></a>試してみる

1. **[Train]\(トレーニング\)** を選択します。

1. トレーニングが完了したら、 **[テスト]** を選択し、以下の操作を試します。

    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 72 度に設定して)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)
    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 45 度に設定して)
    - 出力:Sorry, I can only set between 60 and 80 degrees (申し訳ありません、設定できる範囲は 60 から 80 度までです)
    - 入力: make it 72 degrees instead (それでは、72 度にして)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [カスタム コマンド プレビュー アプリで確認をコマンドに追加する](./how-to-custom-speech-commands-confirmations.md)
