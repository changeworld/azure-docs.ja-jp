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
ms.openlocfilehash: 86a12bd1dccc2b6ac15010546d7e990b768ebc02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "75453350"
---
# <a name="how-to-add-a-one-step-correction-to-a-custom-command-preview"></a>方法:カスタム コマンドにワンステップ修正を追加する (プレビュー)

この記事では、コマンドにワンステップ確認を追加する方法について説明します。

ワンステップ修正は、完了したばかりのコマンドを更新するために使用されます。

すなわち、たった今アラームを設定した場合でも、気が変わってアラームの時刻を更新することができます。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:"Ok, alarm set for 12/06/2019 12:00:00"
- 次の内容を入力します。No, tomorrow at 1pm
- 出力:"Ok

これは、開発者が使用できる、バックエンド アプリケーションでアラームを更新する方法があることを意味します。

## <a name="prerequisites"></a>前提条件

次の記事の手順を完了している必要があります。

- [クイック スタート: カスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-new.md)
- [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
- [方法: カスタム コマンドに確認を追加する (プレビュー)](./how-to-custom-speech-commands-confirmations.md)

## <a name="add-the-advanced-rules-for-one-step-correction"></a>ワンステップ修正の高度なルールを追加する 

ワンステップ修正のデモンストレーションを行うために、[確認の方法](./how-to-custom-speech-commands-confirmations.md)で作成した **SetAlarm** コマンドを拡張しましょう。
 
1. 高度なルールを追加して、以前のアラームを更新します。 

    このルールは、ユーザーにアラームの日付と時刻の確認を要求し、次のターンで確認 (yes/no) を期待します。

   | 設定               | 推奨値                                                  | 説明                                        |
   | --------------------- | ---------------------------------------------------------------- | -------------------------------------------------- |
   | 規則の名前             | Update previous alarm                                            | ルールの目的を説明する名前          |
   | 条件            | UpdateLastCommand & Required Parameter - DateTime                | ルールを実行できるタイミングを決定する条件    |   
   | Actions               | SpeechResponse - "- Updating previous alarm to {DateTime}"       | ルール条件が真のときに実行するアクション |
   | 実行後の状態 | Complete command                                                 | ターン後のユーザーの状態                   |

1. 先ほど作成したルールを高度なルールの先頭に移動します (パネルでルールをスクロールし、上矢印をクリックします)。
   > [!div class="mx-imgBorder"]
   > ![範囲検証を追加する](media/custom-speech-commands/one-step-correction-rules.png)

> [!NOTE]
> 実際のアプリケーションでは、このルールの [アクション] セクションで、クライアントにアクティビティを返信する、またはHTTP エンドポイントを呼び出し、システムのアラームを更新します。

## <a name="try-it-out"></a>試してみる

テスト パネルを選択し、いくつか対話を試します。

- 次の内容を入力します。Set alarm for tomorrow at noon
- 出力:"Are you sure you want to set an alarm for 12/07/2019 12:00:00?"
- 次の内容を入力します。はい
- 出力:"Ok, alarm set for 12/07/2019 12:00:00"
- 次の内容を入力します。No, tomorrow at 1pm
- 出力:"Updating previous alarm to 12/07/2019 13:00:00"
