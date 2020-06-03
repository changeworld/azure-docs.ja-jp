---
title: 方法:カスタム コマンドにワンステップ修正を追加する (プレビュー) - 音声サービス
titleSuffix: Azure Cognitive Services
description: この記事では、コマンドのワンステップ修正をカスタム コマンドで実装する方法について説明します。
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: encorona
ms.openlocfilehash: f43c28d314cb8a0211496664cd20d2c380e4d5b0
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858273"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>方法:カスタム コマンドにワンステップ修正を追加する (プレビュー)

この記事では、コマンドにワンステップ確認を追加する方法について説明します。

ワンステップ修正は、完了したばかりのコマンドを更新するために使用されます。 すなわち、たった今アラームを設定した場合でも、気が変わってアラームの時刻を更新することができます。 この例を以下に示します。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
- 次の内容を入力します。No, tomorrow at 1pm
- 出力:[OK]

実際のシナリオ。通常、クライアントはコマンド完了の結果として、あるアクションを実行します。この記事では、バックエンド アプリケーションでアラームを更新するメカニズムが開発者に与えられていると想定しています。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。
> [!div class="checklist"]

> * [クイック スタート: カスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-new.md)
> * [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
> * [方法: カスタム コマンドに確認を追加する (プレビュー)](./how-to-custom-speech-commands-confirmations.md)


## <a name="add-interaction-rules-for-one-step-correction"></a>ワンステップ修正の相互作用ルールを追加する 

ワンステップ修正のデモンストレーションを行うために、**SetAlarm** コマンドを拡張しましょう。これは「[方法: カスタム コマンドに確認を追加する (プレビュー)](./how-to-custom-speech-commands-confirmations.md)」で作成しました。
1. 以前設定したアラームを更新する相互作用ルールを追加します。 

    このルールは、ユーザーにアラームの日付と時刻の確認を要求し、次のターンで確認 (yes/no) を期待します。

   | 設定               | 推奨値                                                  | 説明                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前             | Update previous alarm                                            | ルールの目的を説明する名前          |
   | 条件            | 前のコマンドを更新する必要がある、かつ、必須パラメーター -> DateTime                | ルールを実行できるタイミングを決定する条件    |   
   | Actions               | [送信] 音声応答 -> シンプルなエディター -> 前のアラームを {DateTime} に更新      | ルール条件が真のときに実行するアクション |
   | 実行後の状態 | コマンドは完了しました        | ターン後のユーザーの状態                   |

1. 作成したルールを相互作用ルールの一番上に移動します (パネルでルールを選択し、中央ペインの上部にある `...` アイコンの下にある上向き矢印をクリックします)。
   > [!div class="mx-imgBorder"]
   > ![範囲検証を追加する](media/custom-speech-commands/one-step-correction-rules.png)
    > [!NOTE]
    > 実際のアプリケーションでは、このルールの [アクション] セクションで、クライアントにアクティビティを返信する、またはHTTP エンドポイントを呼び出し、システムのアラームを更新します。

## <a name="try-it-out"></a>試してみる

`Train` を選択し、トレーニングが完了するまで待ってから、`Test` を選択します。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:Are you sure you want to set an alarm for 2020-05-02 12:00:00 (2020-05-02 12:00:00 のアラームを設定しますか)
- 次の内容を入力します。はい
- 出力:Ok, alarm set for 2020-05-02 12:00:00 (はい、アラームを 2020-05-02 12:00:00 に設定しました)
- 次の内容を入力します。No, tomorrow at 2pm (いいえ、明日の午後 2 時)
- 出力:Updating previous alarm to 2020-05-02 14:00:00 (前のアラームを 2020-05-02 14:00:00 に更新)
