---
title: クイック スタート:パラメーターを含むカスタム コマンドを作成する (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンド アプリケーションにパラメーターを追加します。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 994ac88f78dfe5a5b0ee6fef3fa97d66d53c911b
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156694"
---
# <a name="quickstart-create-a-custom-command-with-parameters-preview"></a>クイック スタート:パラメーターを使用してカスタム コマンドを作成する (プレビュー)

[前の記事](./quickstart-custom-speech-commands-create-new.md)では、パラメーターのないコマンドに応答する新しいカスタム コマンド プロジェクトを作成しました。

この記事では、複数のデバイスのオンとオフを処理できるように、このアプリケーションをパラメーターで拡張します。

## <a name="create-parameters"></a>パラメーターを作成する

1. [前に作成した](./quickstart-custom-speech-commands-create-new.md)プロジェクトを開きます
1. コマンドはオンとオフを処理するようになったので、コマンドの名前を "TurnOnOff" に変更します
   - コマンドの名前の上にカーソルを置き、編集アイコンを選択して名前を変更します
1. ユーザーがデバイスをオンにしたいのか、オフにしたいのかを表わす新しいパラメーターを作成します
   - [パラメーター] セクションの隣にある `+` アイコンを選択します

   > [!div class="mx-imgBorder"]
   > ![パラメーターの作成](media/custom-speech-commands/create-on-off-parameter.png)

   | 設定            | 推奨値     | 説明                                                                                               |
   | ------------------ | ------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | OnOff               | パラメーターのわかりやすい名前                                                                     |
   | Is Global          | チェック解除           | このパラメーターの値がプロジェクトのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス |
   | 必須           | チェック             | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス          |
   | 応答テンプレート  | "- On or off? (付けますか、消しますか)"      | 理解されていないとき、このパラメーターの値を問うプロンプト                                       |
   | 種類               | String              | Number、String、Date Time など、パラメーターの型                                               |
   | 構成      | String List         | Strings の場合、String List により入力が一連の指定可能値に限定されます。                                      |
   | 文字列一覧値 | on、off             | String List パラメーターの場合、一連の指定可能値とそのシノニム                                |

   - 次に、`+` アイコンをもう一度選択し、デバイスの名前を表わす 2 つ目のパラメーターを追加します。 この例では、tv と fan です

   | 設定            | 推奨値       | 説明                                                                                               |
   | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
   | Name               | SubjectDevice         | パラメーターのわかりやすい名前                                                                     |
   | Is Global          | チェック解除             | このパラメーターの値がプロジェクトのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス |
   | 必須           | チェック               | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス          |
   | 応答テンプレート  | "- Which device? (どのデバイスですか?)"     | 理解されていないとき、このパラメーターの値を問うプロンプト                                       |
   | 種類               | String                | Number、String、Date Time など、パラメーターの型                                               |
   | 構成      | String List           | Strings の場合、String List により入力が一連の指定可能値に限定されます。                                      |
   | 文字列一覧値 | tv、fan               | String List パラメーターの場合、一連の指定可能値とそのシノニム                                |
   | シノニム (tv)      | television、telly     | String List パラメーターの指定可能値別の任意のシノニム                                      |

## <a name="add-sample-sentences"></a>例文を追加する

パラメーターを利用するとき、可能な組み合わせをすべて網羅する例文を追加すると役立ちます。 次に例を示します。

1. 完全パラメーター情報 - `"turn {OnOff} the {SubjectDevice}"`
1. 部分パラメーター情報 - `"turn it {OnOff}"`
1. パラメーターなし情報 - `"turn something"`

情報量の異なる例文を利用することで、カスタム コマンド アプリケーションでは、部分的な情報で 1 回限りの解決とマルチターンの解決の両方を解決できます。

そのことを念頭に置き、下の提案のようにパラメーターを使用するように例文を編集します。

> [!TIP]
> 例文エディターでは、中かっこを使用してパラメーターを参照します。 - `turn {OnOff} the {SubjectDevice}` 前に作成したパラメーターを参照するにはタブ補完を使用します。

> [!div class="mx-imgBorder"]
> ![例文とパラメーター](media/custom-speech-commands/create-parameter-sentences.png)

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```

## <a name="add-parameters-to-completion-rule"></a>完了ルールにパラメーターを追加する

[前のクイックスタート](./quickstart-custom-speech-commands-create-new.md)で作成した完了ルールを修正します。

1. 新しい条件を追加し、必須パラメーターを選択します。 `OnOff` と `SubjectDevice` の両方を選択します
1. `OnOff` と `SubjectDevice` を使用するように音声応答アクションを編集します。

   ```
   Ok, turning {OnOff} the {SubjectDevice}
   ```

## <a name="try-it-out"></a>試してみる

テスト チャット パネルを開き、いくつか対話を試します。

- 入力: turn off the tv (テレビを消して)
- 出力:Ok, turning off the tv (了解です。テレビを消します)

- 入力: turn off the television (テレビを消して)
- 出力:Ok, turning off the tv (了解です。テレビを消します)

- 入力: turn it off (それを消して)
- 出力:Which device? (どのデバイスですか?)
- 入力: the tv (テレビ)
- 出力:Ok, turning off the tv (了解です。テレビを消します)

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [クイック スタート: Speech SDK でカスタム コマンド アプリケーションに接続する (プレビュー)](./quickstart-custom-speech-commands-speech-sdk.md)
