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
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: bf77616123f9311f7384fea515f250e47b354c8c
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853581"
---
# <a name="quickstart-create-a-custom-commands-application-with-parameters-preview"></a>クイック スタート:パラメーターを使用してカスタム コマンド アプリケーションを作成する (プレビュー)

[前の記事](./quickstart-custom-speech-commands-create-new.md)では、パラメーターを使用せず、単純なカスタム コマンド アプリケーションを作成しました。

この記事では、複数のデバイスのオンとオフを処理できるように、このアプリケーションを拡張し、パラメーターを使用します。

## <a name="create-parameters"></a>パラメーターを作成する

1. [前に作成した](./quickstart-custom-speech-commands-create-new.md)プロジェクトを開きます
1. 複数のデバイスのオンとオフを切り替えるように既存のコマンドを編集しましょう。
1. コマンドはオンとオフを処理するようになったので、コマンドの名前を `TurnOnOff` に変更します。
   - 左側のペインで `TurnOn` コマンドを選択し、ペインの上部にある `+ New command` の横にある `...` アイコンをクリックします。
   
   - `Rename` アイコンを選択します。 **[Rename command]\(コマンドの名前変更\)** ポップアップ アイテムで **[名前]** を `TurOnOff` に変更します。 次に、 **[保存]** を選択します。

1. 次に、ユーザーがデバイスをオンにしたいのか、オフにしたいのかを表わす新しいパラメーターを作成します。
   - 中央のペインの上部に表示されている `+ Add` アイコンを選択します。 ドロップダウンから **[パラメーター]** を選択します。
   - 右端のペインには、 **[パラメーター]** 構成セクションが表示されます。
   - **[名前]** の値を追加します。
   - **[必須]** チェックボックスにチェックマークを入れます。 **[Add response for a required parameter]\(必須パラメーターの応答追加\)** ウィンドウで **[Simple editor]\(シンプルなエディター\)** を選択し、 **[最初のバリエーション]** に次を追加します。
        ```
        On or Off?
        ```
   - **[Update]\(更新\)** を選択します。

       > [!div class="mx-imgBorder"]
       > ![必須パラメーターの応答を作成する](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
   - 次に、パラメーターの残りのプロパティを次のように構成し、`Save` を選択してパラメーターのすべての構成を保存しましょう。
       

       | 構成      | 推奨値     | 説明                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | 名前               | OnOff           | パラメーターのわかりやすい名前                                                                           |
       | Is Global          | チェック解除       | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス|
       | 必須           | チェック         | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス |
       | 必須パラメーターの応答      |シンプルなエディター -> オンまたはオフ?      | 理解されていないとき、このパラメーターの値を問うプロンプト |
       | Type               | String          | Number、String、Date Time、Geography などのパラメーターの型   |
       | 構成      | 内部カタログから事前定義済み入力値を受け取る | Strings の場合、これにより入力が一連の指定可能値に限定されます。 |
       | 定義済み入力値     | on、off             | 一連の入力可能値とその別名         |
       
        > [!div class="mx-imgBorder"]
        > ![パラメーターの作成](media/custom-speech-commands/create-on-off-parameter.png)

   - 次に、`+ Add` アイコンをもう一度選択し、次の構成が与えられたデバイスの名前を表わす 2 つ目のパラメーターを追加します。
   

       | 設定            | 推奨値       | 説明                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | 名前               | SubjectDevice         | パラメーターのわかりやすい名前                                                                     |
       | Is Global          | チェック解除             | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルで適用されるかどうかを示すチェックボックス |
       | 必須           | チェック               | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス          |
       | シンプルなエディター      | Which device? (どのデバイスですか?)    | 理解されていないとき、このパラメーターの値を問うプロンプト                                       |
       | Type               | String                | Number、String、Date Time、Geography などのパラメーターの型                                                |
       | 構成      | 内部カタログから事前定義済み入力値を受け取る | Strings の場合、String List により入力が一連の指定可能値に限定されます。       |
       | 定義済み入力値 | tv、fan               | 一連の入力可能値とその別名                               |
       | 別名 (tv)      | television、telly     | 定義済み入力値の任意の別名 (考えられる値別)                                 |

## <a name="add-example-sentences"></a>例文を追加する

パラメーターと共にコマンドを利用するとき、可能な組み合わせをすべて網羅する例文を追加すると役立ちます。 次に例を示します。

1. 完全なパラメーター情報 - `turn {OnOff} the {SubjectDevice}`
1. 部分パラメーター情報 - `turn it {OnOff}`
1. パラメーターなし情報 - `turn something`

情報の度合いの異なる例文を利用することで、カスタム コマンド アプリケーションでは、部分的な情報で 1 回限りの解決とマルチターンの解決の両方を解決できます。

そのことを念頭に置き、下の提案のようにパラメーターを使用するように例文を編集します。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 例文エディターでは、中かっこを使用してパラメーターを参照します。 - `turn {OnOff} the {SubjectDevice}` 前に作成したパラメーターでバックアップされるオートコンプリートには Tab キーを使用します。

## <a name="add-parameters-to-completion-rules"></a>完了ルールにパラメーターを追加する

[前のクイックスタート](./quickstart-custom-speech-commands-create-new.md)で作成した完了ルールを修正します。

1. **[条件]** セクションで **[+ 条件を追加する]** を選択し、新しい条件を追加します。
1. **[新しい条件]** というポップアップが新しく表示されたら、 **[種類]** ドロップダウンから `Required parameters` を選択します。 下のチェックリストで、`OnOff` と `SubjectDevice` の両方を選択します。
1. **[作成]** をクリックします。
1. **[アクション]** セクションで、アクションの上にカーソルを置き、編集アイコンをクリックすることで既存の [送信] 音声応答アクションを編集します。 今回、新しく作成した `OnOff` パラメーターと `SubjectDevice` パラメーターを使用してみましょう。

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>試してみる
1. 右側のペインの上部に表示されている `Train` アイコンを選択します。

1. トレーニングが完了したら、[`Test`] を選択します。
    - 新しい **[アプリケーションのテスト]** ウィンドウが表示されます。
    - いくつかやりとりしてみてください。

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
> [クイック スタート: カスタム音声でカスタム コマンドを使用する (プレビュー)](./quickstart-custom-speech-commands-select-custom-voice.md)
