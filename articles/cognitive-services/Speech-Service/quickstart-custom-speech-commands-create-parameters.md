---
title: クイック スタート:パラメーターを使用してカスタム コマンド プレビュー アプリを作成する - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、複数のデバイスのオンとオフを切り替えることができるようにカスタム コマンド アプリケーションにパラメーターを追加します。
services: cognitive-services
author: nitinme
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: nitinme
ms.openlocfilehash: 940b3604487fbef7736c0d8f4f9299563b0b86d5
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/08/2020
ms.locfileid: "84509306"
---
# <a name="quickstart-create-a-custom-commands-preview-application-with-parameters"></a>クイック スタート:パラメーターを使用してカスタム コマンド プレビュー アプリケーションを作成する

[前の記事](./quickstart-custom-speech-commands-create-new.md)では、パラメーターを使用せず、単純なカスタム コマンド アプリケーションを作成しました。

この記事では、複数のデバイスのオンとオフを切り替えることができるように、パラメーターを使用してそのアプリケーションを拡張します。

## <a name="create-parameters"></a>パラメーターの作成

1. [前の記事](./quickstart-custom-speech-commands-create-new.md)で作成したプロジェクトを開きます。

   複数のデバイスのオンとオフの切り替えに使用できるように既存のコマンドを編集します。
1. コマンドはオンとオフの両方を処理するようになるため、その名前を **TurnOnOff** に変更します。
   1. 左ペインで **TurnOn** コマンドを選択し、ペインの上部で **[新しいコマンド]** の横にある省略記号 ( **...** ) ボタンを選択します。
   
   1. **[Rename]\(名前の変更\)** を選択します。 **[Rename command]\(コマンドの名前変更\)** ウィンドウで、 **[名前]** を「**TurOnOff**」に変更します。 **[保存]** を選択します。

1. ユーザーがデバイスをオンにするかオフにするかを表すパラメーターを作成します。
   1. 中央のペインの上部にある **[追加]** を選択します。 ドロップダウン リストで、 **[パラメーター]** を選択します。
   1. 右ペインの **[パラメーター]** セクションで、 **[名前]** ボックスに値を追加します。
   1. **[必須]** を選択します。 **[Add response for a required parameter]\(必須パラメーターの応答追加\)** ウィンドウで **[Simple editor]\(シンプルなエディター\)** を選択します。 **[First variation]\(最初のバリエーション\)** ボックスに次のテキストを入力します。
        ```
        On or Off?
        ```
   1. **[Update]\(更新\)** を選択します。

       > [!div class="mx-imgBorder"]
       > ![必須パラメーターの応答を作成する](media/custom-speech-commands/add-required-on-off-parameter-response.png)
   
1. このパラメーターの残りのプロパティを次のように構成します。
       

    | 構成      | 推奨値     | 説明                                                      |
    | ------------------ | ----------------| ---------------------------------------------------------------------|
    | **名前**               | **OnOff**           | パラメーターのわかりやすい名前。                                                                  |
    | **Is Global (グローバル)**          | クリア       | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルに適用されるかどうかを示すチェック ボックス。|
    | **必須**           | Selected         | このパラメーターの値が必須であるかどうかを示すチェック ボックス。  |
    | **Response for required parameter (必須パラメーターの応答)**      |**[Simple editor]\(シンプルなエディター\) -> 「On or Off?」**      | 認識されない場合にパラメーターの値を求めるプロンプト。 |
    | **Type**               | **String**          | パラメーターの型。 たとえば、Number、String、Date Time、Geography があります。   |
    | **Configuration**      | **Accept predefined input values from internal catalog (内部カタログから定義済み入力値を受け取る)** | 文字列の場合、この設定により、入力が一連の指定可能な値に制限されます。 |
    | **Predefined input values (定義済み入力値)**     | **on**、**off**             | 一連の指定可能な値とその別名。         |
       


    > [!div class="mx-imgBorder"]
    > ![パラメーターの作成](media/custom-speech-commands/create-on-off-parameter.png)

1. **[保存]** を選択して設定を保存します。

 1. もう一度 **[追加]** を選択して 2 つ目のパラメーターを追加します。 このパラメーターは、デバイスの名前を表します。 次の設定を使用します。
   

       | 設定            | 推奨値       | 説明                                                                                               |
       | ------------------ | --------------------- | --------------------------------------------------------------------------------------------------------- |
       | **名前**               | **SubjectDevice**         | パラメーターのわかりやすい名前。                                                                     |
       | **Is Global (グローバル)**          | クリア             | このパラメーターの値がアプリケーションのすべてのコマンドにグローバルに適用されるかどうかを示すチェック ボックス。 |
       | **必須**           | Selected               | このパラメーターの値が必須であるかどうかを示すチェック ボックス。          |
       | **Simple editor (シンプルなエディター)**      | **Which device?**    | 認識されない場合にパラメーターの値を求めるプロンプト。                                       |
       | **Type**               | **String**                | パラメーターの型。 たとえば、Number、String、Date Time、Geography があります。                                                |
       | **Configuration**      | **Accept predefined input values from internal catalog (内部カタログから定義済み入力値を受け取る)** | 文字列の場合、この設定により、入力が一連の指定可能な値に制限されます。       |
       | **Predefined input values (定義済み入力値)** | **tv**、**fan**               | 一連の指定可能な値とその別名。                               |
       | **別名** (tv)      | **television**、**telly**     | 定義済み入力値それぞれの任意の別名。                                 |

## <a name="add-example-sentences"></a>例文を追加する

パラメーターを含むコマンドの場合、考えられる組み合わせすべてを網羅する例文を追加すると役立ちます。 次に例を示します。

- 完全なパラメーター情報: `turn {OnOff} the {SubjectDevice}`
- 部分的なパラメーター情報: `turn it {OnOff}`
- パラメーター情報なし: `turn something`

情報量の異なる例文を利用すると、カスタム コマンド アプリケーションでは、部分的な情報を含む 1 回限りの解決とマルチターンの解決の両方を解決できます。

そのことを念頭に置き、ここでの提案に従って、パラメーターを使用するよう例文を編集します。

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
> [!TIP]
> 例文エディターでは、中かっこを使用してパラメーターを参照します: `turn {OnOff} the {SubjectDevice}`。
>
> 以前に作成したパラメーターで定義されているオートコンプリートには Tab キーを使用します。

## <a name="add-parameters-to-completion-rules"></a>完了ルールにパラメーターを追加する

[前のクイックスタート](./quickstart-custom-speech-commands-create-new.md)で作成した完了ルールを変更します。

1. **[条件]** セクションで、 **[条件の追加]** を選択します。
1. **[新しい条件]** ウィンドウの **[種類]** リストで、 **[必要なパラメーター]** を選択します。 リストで **OnOff** と **SubjectDevice** の両方を選択します。
1. **［作成］** を選択します
1. **[アクション]** セクションで、既存の **[Send speech response]\(音声応答の送信\)** アクションにカーソルを移動して編集ボタンを選択することで、そのアクションを編集します。 今回は、新しい `OnOff` および `SubjectDevice` パラメーターを使用します。

    ```
    Ok, turning {OnOff} the {SubjectDevice}
    ```

## <a name="try-it-out"></a>試してみる
1. 右ペインの上部にある **[トレーニング]** を選択します。

1. トレーニングが完了したら、 **[テスト]** を選択します。
    
    **[Test your application]\(アプリケーションのテスト\)** ウィンドウが表示されます。

1. いくつかやりとりしてみてください。

        - Input: turn off the tv
        - Output: Ok, turning off the tv        
        - Input: turn off the television
        - Output: Ok, turning off the tv
        - Input: turn it off
        - Output: Which device?
        - Input: the tv
        - Output: Ok, turning off the tv

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [クイック スタート: カスタム音声でカスタム コマンドを使用する (プレビュー)](./quickstart-custom-speech-commands-select-custom-voice.md)
