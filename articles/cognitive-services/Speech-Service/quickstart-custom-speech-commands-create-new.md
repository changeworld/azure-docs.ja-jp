---
title: クイック スタート:カスタム コマンド プレビュー アプリを作成する - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、ホスト型カスタム コマンド プレビュー アプリケーションを作成してテストします。 このアプリケーションでは発話を処理します。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142352"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>クイック スタート:カスタム コマンド プレビュー アプリを作成する

このクイックスタートでは、カスタム コマンド アプリケーションを作成してテストする方法について説明します。
このアプリケーションは、"turn on the tv" (テレビをつけて) のような発話を処理し、"Ok, turning on the tv" (了解です。テレビをつけます) のような簡単なメッセージで応答します。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 現時点で、カスタム コマンドでサポートされているのは、westus、westus2、neur の各リージョンの音声サブスクリプションのみです。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>カスタム コマンドを使用するため Speech Studio にアクセスする

1. Web ブラウザーで [Speech Studio](https://speech.microsoft.com/) にアクセスします。
1. 資格情報を入力してポータルにサインインします。

   既定のビューは、音声サブスクリプションの一覧です。
    > [!NOTE]
    > サブスクリプションの選択ページが表示されない場合は、上部のバーの設定メニューで **[Speech resources]\(音声リソース\)** を選択すると表示できます。

1. 音声サブスクリプションを選択し、 **[Go to Studio]\(Studio にアクセス\)** を選択します。
1. **[カスタム コマンド]** を選択します。

     既定のビューは、選択したサブスクリプションに含まれるカスタム コマンド アプリケーションの一覧です。

## <a name="create-a-custom-commands-project"></a>カスタム コマンド プロジェクトを作成する

1. **[新しいプロジェクト]** を選択してプロジェクトを作成します。

   > [!div class="mx-imgBorder"]
   > ![プロジェクトの作成](media/custom-speech-commands/create-new-project.png)

1. **[名前]** ボックスにプロジェクト名を入力します。
1. **[言語]** の一覧で言語を選択します。
1. **[LUIS authoring resource]\(LUIS オーサリング リソース\)** の一覧でオーサリング リソースを選択します。 有効なオーサリング リソースがない場合は、 **[Create new LUIS authoring resource]\(新しい LUIS オーサリング リソースの作成\)** を選択して作成します。

   > [!div class="mx-imgBorder"]
   > ![リソースの作成](media/custom-speech-commands/create-new-resource.png)

   1. **[リソース名]** ボックスに、リソースの名前を入力します。
   1. **[リソース グループ]** の一覧でリソース グループを選択します。
   1. **[Location]\(場所\)** の一覧で場所を選択します。
   1. **[価格レベル]** の一覧でレベルを選択します。

      > [!NOTE]
      > リソース グループを作成するには、 **[リソース グループ]** ボックスにリソース グループ名を入力します。 このリソース グループは、 **[作成]** を選択すると作成されます。

1. **［作成］** を選択します
1. プロジェクトが作成されたら選択します。

    これで、新しいカスタム コマンド アプリケーションの概要が表示されます。

## <a name="update-luis-resources-optional"></a>LUIS リソースを更新する (省略可能)

**[新しいプロジェクト]** ウィンドウで選択したオーサリング リソースを更新し、予測リソースを設定できます。 予測リソースは、カスタム コマンド アプリケーションが発行されると、認識のために使用されます。 開発およびテスト フェーズでは、予測リソースは必要ありません。

1. 左ペインで **[設定]** を選択してから、中央のペインで **[LUIS resources]\(LUIS リソース\)** を選択します。
1. 予測リソースを選択するか、 **[新しいリソースの作成]** を選択して作成します。
1. **[保存]** を選択します。
    
    > [!div class="mx-imgBorder"]
    > ![LUIS リソースの設定](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> オーサリング リソースは 1 か月あたり 1,000 の予測エンドポイント要求しかサポートしないため、カスタム コマンド アプリケーションを発行する前に LUIS 予測リソースを設定する必要があります。


## <a name="create-a-command"></a>コマンドを作成する

1 つの発話 `turn on the tv` を受け取って、メッセージ `Ok, turning on the tv` で応答する簡単なコマンドを作成してみましょう。

1. 左ペインの上部にある **[新しいコマンド]** を選択して、コマンドを作成します。 **[新しいコマンド]** ウィンドウが開きます。
1. **[名前]** ボックスに、「**TurnOn**」と入力します。
1. **［作成］** を選択します

中央のペインには、このコマンドのプロパティが表示されます。


| 構成            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **[Example sentences]\(例文\)** | ユーザーがコマンドをトリガーするために利用できる発話の例。                                                                 |
| **パラメーター**       | コマンドを完了するために必要な情報。                                                                                |
| **[Completion rules]\(完了ルール\)** | コマンドを完了するために実行されるアクション。 たとえば、ユーザーへの応答や別の Web サービスとの通信があります。 |
| **[Interaction rules]\(相互作用ルール\)**   | より具体的または複雑な状況に対処するための追加ルール。                                                              |


> [!div class="mx-imgBorder"]
> ![コマンドの作成](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>例文を追加する

**[Example sentences]\(例文\)** セクションから始めます。 ユーザーが発話できる内容の例を指定します。

1. 中央のペインで **[Example sentences]\(例文\)** を選択します。 
1. 右側のペインで、例を追加します。

    ```
    turn on the tv
    ```

1. ペインの上部にある **[保存]** を選択します。

現在、パラメーターがないため、 **[Completion rules]\(完了ルール\)** セクションに移動できます。

### <a name="add-a-completion-rule"></a>完了ルールを追加する

ここで、次の構成を含む完了ルールを追加します。 このルールは、ユーザに対して、フルフィルメント アクションが実行されていることを示します。


| 設定    | 推奨値                          | 説明                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **名前**  | **ConfirmationResponse**                  | ルールの目的を説明する名前。          |
| **条件** | なし                                     | ルールを実行できるタイミングを決定する条件。    |
| **アクション**    | **Send speech response (音声応答の送信) -> Ok, turning on the tv** | ルール条件に該当するときに実行するアクション。 |

1. 中央のペインの上部にある **[追加]** を選択して、新しい完了ルールを作成します。
1. **[名前]** ボックスに、名前を入力します。
1. アクションを追加します。
   1. **[アクション]** セクションで **[アクションの追加]** を選択して、アクションを作成します。
   1. **[アクションの編集]** ウィンドウの **[Type]\(種類\)** の一覧で、 **[Send speech response]\(音声応答の送信\)** を選択します。
   1. **[応答]** で、 **[Simple editor]\(シンプルなエディター\)** を選択します。 **[First variation]\(最初のバリエーション\)** ボックスに「**Ok, turning on the tv**」と入力します。

   > [!div class="mx-imgBorder"]
   > ![応答の作成](media/custom-speech-commands/create-speech-response-action.png)

1. **[保存]** を選択してルールを保存します。
1. **[完了ルール]** セクションに戻り、 **[保存]** を選択してすべての変更を保存します。 

> [!div class="mx-imgBorder"]
> ![完了ルールの作成](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストします。
1. 右ペインの上部にある **[トレーニング]** を選択します。
1. トレーニングが完了したら、 **[テスト]** を選択します。 新しい **[Test your application]\(アプリケーションのテスト\)** ウィンドウが表示されます。
    - 「**turn on the tv**」と入力します。
    - 予期される応答:**Ok, turning on the tv**


> [!div class="mx-imgBorder"]
> ![動作のテスト](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: パラメーターを使用してカスタム コマンド プレビュー アプリケーションを作成する](./quickstart-custom-speech-commands-create-parameters.md)
