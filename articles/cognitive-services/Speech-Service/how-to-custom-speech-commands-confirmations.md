---
title: 方法:カスタム コマンドに確認を追加する (プレビュー)
titleSuffix: Azure Cognitive Services
description: この記事では、カスタム コマンドでコマンドに対する確認を実装する方法を説明します。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: bf1b79c1b5d7b9dfd93b354c6b6ff5a512bb74a5
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858243"
---
# <a name="how-to-add-a-confirmation-to-a-custom-command-preview"></a>方法:カスタム コマンドに確認を追加する (プレビュー)

この記事では、コマンドに確認を追加する方法について説明します。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。
> [!div class="checklist"]
> *  [クイック スタート: カスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-new.md)
> * [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)

## <a name="create-a-setalarm-command"></a>SetAlarm コマンドを作成する

検証のため、ユーザーがアラームを設定できるようにする新しいコマンドを作成しましょう。

1. [Speech Studio](https://speech.microsoft.com/) で前に作成したカスタム コマンド アプリケーションを開きます。
1. 新しいコマンド `SetAlarm` を作成します。
1. 以下の構成で `DateTime` というパラメーターを追加します。

   | 設定                           | 推奨値                     |  説明                 |
   | --------------------------------- | -----------------------------------------------------| ------------|
   | 名前                              | DateTime                                | パラメーターのわかりやすい名前                                |
   | 必須                          | チェック                                 | コマンドを完了する前にこのパラメーターの値を必須とするかどうかを示すチェックボックス |
   | 必須パラメーターの応答   | Simple editor -> What time? (シンプルなエディター -> 何時ですか?)                              | 理解されていないとき、このパラメーターの値を問うプロンプト |
   | Type                              | DateTime                                | Number、String、Date Time、Geography などのパラメーターの型   |
   | 日付の既定値                     | 日付が指定されていない場合、今日に設定します            | ユーザーが指定しなかった場合に使用する変数の既定値。  |  
   | 時間の既定値                     | 時間が指定されていない場合、一日の始まりに設定します     |  ユーザーが指定しなかった場合に使用する変数の既定値。|

1. いくつかの文例を追加します。
   
    ```
    set an alarm for {DateTime}
    set alarm {DateTime}
    alarm for {DateTime}
   ```

1. 結果を確定する完了ルールを追加します。

   | 設定    | 推奨値                               |説明                                     |
   | ---------- | ------------------------------------------------------- |-----|
   | 規則の名前  | アラームの設定                                               |    ルールの目的を説明する名前 |
   | Actions    | 音声応答の送信 - Ok, alarm set for {DateTime} (はい、{DateTime} にアラームを設定しました)    |ルール条件が真のときに実行するアクション

## <a name="try-it-out"></a>試してみる

1. 右側のペインの上部に表示されている `Train` アイコンを選択します。

1. トレーニングが完了したら、[`Test`] を選択します。
    - 次の内容を入力します。Set alarm for tomorrow at noon
    - 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
    - 次の内容を入力します。アラームを設定する
    - 出力:What time? (いつに設定しますか?)
    - 次の内容を入力します。5 時
    - 出力:Ok, alarm set for 2020-05-01 17:00:00 (はい、アラームを 2020-05-01 17:00:00 に設定しました)

## <a name="add-the-advanced-rules-for-confirmation"></a>確認に高度なルールを追加する

会話ルールを追加して確認を行います。

1. 既存の `SetAlarm` コマンドで、中央のペインの `+Add` アイコンを選択し、 **[Interaction rules]\(会話ルール\)** を追加し、 **[Interaction rules]\(会話ルール\)**  ->  **[Confirm command]\(確認コマンド\)** を選択します。

    このルールは、ユーザーにアラームの日付と時刻の確認を要求し、次のターンで確認 (yes/no) を期待します。

   | 設定               | 推奨値                                                                  | 説明                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前             | 日付と時刻の確認                                                                | ルールの目的を説明する名前          |
   | 条件            | 必須パラメーター - DateTime                                                    | ルールを実行できるタイミングを決定する条件    |   
   | Actions               | 音声応答の送信 -> "Are you sure you want to set an alarm for {DateTime}?" (アラームを {DateTime} に設定しますか?)     | ルール条件が真のときに実行するアクション |
   | 期待される回答          | ユーザーからの確認が必要                                                 | 次のターンで期待される回答                      |
   | 実行後の状態  | Wait for user's input (ユーザーの入力を待っています)                                                            | ターン後のユーザーの状態                  |
  
      > [!div class="mx-imgBorder"]
      > ![必須パラメーターの応答を作成する](media/custom-speech-commands/add-validation-set-temperature.png)

1. 成功した確認 (ユーザーが "はい" と言った) を処理する別の会話ルールを追加します

   | 設定               | 推奨値                                                                  | 説明                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前             | 承認の確認                                                            | ルールの目的を説明する名前          |
   | 条件            | 確認の成功と必須のパラメーター -> DateTime                      | ルールを実行できるタイミングを決定する条件    |   
   | 実行後の状態 | 完了ルールの実行                                                          | ターン後のユーザーの状態                   |

1. 拒否の確認の場合 (ユーザーが no (いいえ) と言う) の処理のための高度なルールを追加する

   | 設定               | 推奨値                                                                  | 説明                                        |
   | --------------------- | -------------------------------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前             | 拒否の確認                                                                   | ルールの目的を説明する名前          |
   | 条件            | 拒否の確認と必須のパラメーター -> DateTime                               | ルールを実行できるタイミングを決定する条件    |   
   | Actions               | パラメーターの値をクリアする -> DateTime および音声応答の送信 -> No problem, what time then? (問題ありません。ではいつに設定しますか?)  | ルール条件が真のときに実行するアクション |
   | 実行後の状態 | 入力を待機する                                                                   | ターン後のユーザーの状態                   |
   | 期待される回答          | ユーザーからのパラメーター入力が必要 -> DateTime                           | 次のターンで期待される回答                      |

## <a name="try-out-the-changes"></a>変更を試す

`Train` を選択し、トレーニングが完了するまで待ってから、`Test` を選択します。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:Are you sure you want to set an alarm for 2020-05-02 12:00:00? (アラームを 2020-05-02 12:00:00 に設定しますか?)
- 次の内容を入力します。いいえ
- 出力:No problem, what time then? (問題ありません。ではいつに設定しますか?)
- 次の内容を入力します。5 時
- 出力:"Are you sure you want to set an alarm for 2020-05-01 17:00:00?" (アラームを 2020-05-01 17:00:00 に設定しますか?)
- 次の内容を入力します。はい
- 出力:Ok, alarm set for 2020-05-01 17:00:00 (はい、アラームを 2020-05-01 17:00:00 に設定しました)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [方法: カスタム コマンドにワンステップ修正を追加する (プレビュー)](./how-to-custom-speech-commands-one-step-correction.md)