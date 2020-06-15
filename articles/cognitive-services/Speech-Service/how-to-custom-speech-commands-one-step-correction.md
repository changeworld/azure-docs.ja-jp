---
title: カスタム コマンド プレビューにワンステップ修正を追加する - 音声サービス
titleSuffix: Azure Cognitive Services
description: コマンドのワンステップ修正をカスタム コマンド プレビュー アプリに追加する方法について説明します。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: 05f63ba4e70f649df33905f1e92fb1fab866a86c
ms.sourcegitcommit: 69156ae3c1e22cc570dda7f7234145c8226cc162
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/03/2020
ms.locfileid: "84310429"
---
# <a name="add-a-one-step-correction-to-a-custom-command-in-a-custom-commands-preview-application"></a>カスタム コマンド プレビュー アプリケーションのカスタム コマンドにワンステップ修正を追加する

この記事では、カスタム コマンド プレビュー アプリのコマンドにワンステップ確認を追加する方法について説明します。

ワンステップ修正は、完了したばかりのコマンドを更新するために使用されます。 アラーム コマンドにワンステップ修正を追加した場合、気が変わったらアラームの時刻を更新することができます。 次に例を示します。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
- 次の内容を入力します。No, tomorrow at 1pm
- 出力:[OK]

> [!NOTE]
> 実際のシナリオでは、クライアントはコマンドの完了の結果としてアクションを実行します。 この記事では、バックエンド アプリケーションでアラームを更新するためのメカニズムがあることを前提としています。

## <a name="prerequisites"></a>前提条件

以下の記事の手順を完了してください。
> [!div class="checklist"]

> * [クイック スタート: カスタム コマンド プレビュー アプリを作成する](./quickstart-custom-speech-commands-create-new.md)
> * [クイック スタート: パラメーターを使用してカスタム コマンド プレビュー アプリを作成する](./quickstart-custom-speech-commands-create-parameters.md)
> * [方法: カスタム コマンド プレビュー アプリで確認をコマンドに追加する](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-interaction-rules-for-one-step-correction"></a>ワンステップ修正の相互作用ルールを追加する

ワンステップ修正のデモンストレーションを行うために、**SetAlarm** コマンドを拡張します。これは、[カスタム コマンド プレビューで確認をコマンドに追加する方法](./how-to-custom-speech-commands-confirmations.md)に関するページで作成したものです。

1. **SetAlarm** コマンドを更新する相互作用ルールを追加します。

    このルールは、ユーザーにアラームの日付と時刻の確認を要求し、次のターンでの確認 (yes または no) を想定しています。

   | 設定               | 推奨値                                                  | 説明                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | **ルールの名前**             | **前のアラームの更新**                                            | ルールの目的を説明する名前          |
   | **条件**            | **前のコマンドを更新する必要がある、かつ、必須パラメーター -> DateTime**                | ルールを実行できるタイミングを決定する条件    |   
   | **アクション**               | **音声応答の送信 -> シンプルなエディター -> 前のアラームを {DateTime} に更新**      | ルール条件が真のときに実行するアクション |
   | **実行後の状態** | **コマンドは完了しました**        | ターン後のユーザーの状態                   |

1. ペインで、先ほど作成した相互作用ルールを選択します。 ペインの左上隅にある省略記号 ( **...** ) ボタンを選択します。 次に、 **[上へ移動]** 矢印を使用して、ルールを **[Interaction rules]\(相互作用ルール\)** の一覧の一番上に移動します。
   > [!div class="mx-imgBorder"]
   > ![範囲検証を追加する](media/custom-speech-commands/one-step-correction-rules.png)

    > [!NOTE]
    > 実際のアプリケーションでは、 **[アクション]** セクションを使用してクライアントにアクティビティを返送するか、HTTP エンドポイントを呼び出してシステムのアラームを更新します。

## <a name="try-it-out"></a>試してみる

1. **[Train]\(トレーニング\)** を選択します。

1. トレーニングが完了したら、 **[テスト]** を選択し、以下の操作を試します。

   - 次の内容を入力します。Set alarm for tomorrow at noon
   - 出力:Are you sure you want to set an alarm for 2020-05-02 12:00:00 (2020-05-02 12:00:00 のアラームを設定しますか)
   - 次の内容を入力します。はい
   - 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
   - 次の内容を入力します。No, tomorrow at 2pm (いいえ、明日の午後 2 時)
   - 出力:Updating previous alarm to 2020-05-02 14:00:00 (前のアラームを 2020-05-02 14:00:00 に更新)
