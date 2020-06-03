---
title: 方法:カスタム コマンド パラメーターに検証を追加する
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
ms.openlocfilehash: 2b7fd608156ab269cfc0c85c6c508fa9d5eebc83
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857199"
---
# <a name="how-to-add-validations-to-custom-command-parameters-preview"></a>方法:カスタム コマンド パラメーターに検証を追加する (プレビュー)

この記事では、パラメーターに検証を追加し、修正を求めるプロンプトを表示します。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。

> [!div class="checklist"]
> * [クイック スタート: カスタム コマンドを作成する](./quickstart-custom-speech-commands-create-new.md)
> * [クイック スタート: パラメーターを使用してカスタム コマンドを作成する](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-settemperature-command"></a>SetTemperature コマンドを作成する

検証例を確認するために、ユーザーが温度を設定できる新しいコマンドを作成しましょう。

1. [Speech Studio](https://speech.microsoft.com/) で前に作成したカスタム コマンド アプリケーションを開きます。
1. 新しいコマンド `SetTemperature` を作成します
1. ターゲットの温度のパラメーターを追加します。

   | パラメーターの構成           | 推奨値    |説明                 |                                    
   | ----------------- | ----------------------------------| -------------|
   | 名前              | 気温                       | パラメーターのわかりやすい名前                                |
   | 必須          | チェック                           | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス |
   | 必須パラメーターの応答     | Simple editor -> What temperature would you like? (シンプルなエディター -> 好みの温度は何度ですか?)  | 理解されていないとき、このパラメーターの値を問うプロンプト |
   | Type              | Number                            | Number、String、Date Time、Geography などのパラメーターの型   |

1. 温度パラメーターの検証を追加します。

    - `Temperature` パラメーターの **[Parameters]\(パラメーター\)** の構成ページで、[Validations]\(検証\) セクションから `Add a validation` を選択します。
    - **[New validation]\(新しい検証\)** ポップアップの値を次のように入力し、 **[Create]\(作成\)** を選択します。

  
       | パラメーターの構成         | 推奨値                                          | 説明                                                                        |
       | ----------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------ |
       | Min Value (最小値)        | 60               | Number パラメーターの場合、このパラメーターで想定できる最小値 |
       | Max Value (最大値)        | 80               | 数値パラメーターの場合、このパラメーターで想定できる最大値 |
       | Failure response - Simple editor (失敗応答 - シンプルなエディター)| First Variation - Sorry, I can only set between 60 and 80 degrees (最初の検証 - 申し訳ありません、設定できる範囲は 60 から 80 度までです)      | 検証が失敗した場合に新しい値の入力を求めるプロンプトを表示します                                       |

       > [!div class="mx-imgBorder"]
       > ![範囲検証を追加する](media/custom-speech-commands/validations-add-temperature.png)

1. いくつかの文例を追加します

   ```
   set the temperature to {Temperature} degrees
   change the temperature to {Temperature}
   set the temperature
   change the temperature
   ```

1. 結果を確定する完了ルールを追加する

   | 設定    | 推奨値                                           |説明                                     |
   | ---------- | --------------------------------------------------------- |-----|
   | 名前       | 確認メッセージ                                      |ルールの目的を説明する名前 |
   | 条件 | 必須のパラメーター - `Temperature`                       |ルールを実行できるタイミングを決定する条件    |   
   | Actions    | 音声応答の送信 - `Ok, setting temperature to {Temperature} degrees` | ルール条件が真のときに実行するアクション |

> [!TIP]
> この例では、音声応答を利用して結果を確定します。 クライアント アクションでコマンドを完了する例については、「[方法: Speech SDK を使用してクライアント上でコマンドを実行する](./how-to-custom-speech-commands-fulfill-sdk.md)


## <a name="try-it-out"></a>試してみる
1. 右側のペインの上部に表示されている `Train` アイコンを選択します。

1. トレーニングが完了したら [`Test`] を選択して、いくつか会話を試してみてください。

    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 72 度に設定して)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)
    - 次の内容を入力します。Set the temperature to 72 degrees (温度を 45 度に設定して)
    - 出力:Sorry, I can only set between 60 and 80 degrees (申し訳ありません、設定できる範囲は 60 から 80 度までです)
    - 入力: make it 72 degrees instead (それでは、72 度にして)
    - 出力:Ok, setting temperature to 72 degrees (はい、温度を 72 度に設定します)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: カスタム コマンドに確認を追加する (プレビュー)](./how-to-custom-speech-commands-confirmations.md)
