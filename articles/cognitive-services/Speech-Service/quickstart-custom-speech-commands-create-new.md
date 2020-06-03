---
title: クイック スタート:カスタム コマンドを作成する (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、ホストされたカスタム コマンド アプリケーションを作成してテストします。
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872524"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>クイック スタート:カスタム コマンド アプリを作成する (プレビュー)

このクイックスタートでは、カスタム コマンド アプリケーションを作成してテストする方法について説明します。
作成されたアプリケーションは、"turn on the tv" (テレビをつけて) のような発話を処理し、簡単なメッセージ "Ok, turning on the tv".(了解です。テレビをつけます) で応答します。

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech リソースを作成する <span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > 現時点で、カスタム コマンドでサポートされているのは、westus、westus2、neur の各リージョンの音声サブスクリプションのみです。

## <a name="go-to-the-speech-studio-for-custom-commands"></a>カスタム コマンドを使用するため Speech Studio にアクセスする

1. Web ブラウザーを開き、[Speech Studio](https://speech.microsoft.com/) に移動します。
1. 資格情報を入力してポータルにサインインします。

   - 既定のビューは、音声サブスクリプションの一覧です。
     > [!NOTE]
     > サブスクリプションの選択ページが表示されない場合は、上部のバーの [設定] メニューで [Speech resources]\(音声リソース\) を選択すると表示されます。

1. 音声サブスクリプションを選択し、 **[Go to Studio]\(Studio にアクセス\)** を選択します。
1. **[カスタム コマンド]** を選択します。

     - 既定のビューは、選択したサブスクリプションにあるカスタム コマンド アプリケーションの一覧です。

## <a name="create-a-custom-commands-project"></a>カスタム コマンド プロジェクトを作成する

1. **[新しいプロジェクト]** を選択して新しいプロジェクトを作成します。

   > [!div class="mx-imgBorder"]
   > ![プロジェクトの作成](media/custom-speech-commands/create-new-project.png)

1. プロジェクト名を入力します。
1. ドロップダウンから言語を選択します。
1. ドロップダウンから作成リソースを選択します。 有効な作成リソースがない場合は、 **[Create new LUIS authoring resource]\(新しい LUIS 作成リソースの作成\)** をクリックして作成します。

   > [!div class="mx-imgBorder"]
   > ![リソースの作成](media/custom-speech-commands/create-new-resource.png)

   - [リソース名] と [リソース グループ] を入力します。
   - ドロップダウンから [場所] と [価格レベル] を選択します。

      > [!NOTE]
      > リソース グループを作成するには、[リソース グループ] フィールドに目的のリソース グループ名を入力します。 **[作成]** を選択すると、リソース グループが作成されます。

1. 次に、 **[作成]** を選択してプロジェクトを作成します。
1. 作成したら、プロジェクトを選択します。

    - これで、新しく作成したカスタム コマンド アプリケーションの概要が表示されます。

## <a name="update-luis-resources-optional"></a>LUIS リソースを更新する (省略可能)

[新しいプロジェクト] ウィンドウで設定した作成リソースを更新し、予測リソースを設定できます。 予測リソースは、カスタム コマンド アプリケーションが発行された後に認識のために使用されます。 開発およびテスト フェーズでは、予測リソースは必要ありません。

1. 左側のペインで **[設定]** を選択してから、中央のペインの **[LUIS リソース]** セクションに移動します。
1. 予測リソースを選択するか、 **[新しいリソースの作成]** を選択して作成します。
1. **[保存]** を選択します。
    
    > [!div class="mx-imgBorder"]
    > ![LUIS のリソースをセットする](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> 作成リソースは 1 か月あたり 1,000 の予測エンドポイント要求しかサポートしていないため、カスタム コマンド アプリケーションを発行する前に LUIS 予測リソースを設定する必要があります。


## <a name="create-a-new-command"></a>新しいコマンドを作成する

1 つの発話 `turn on the tv` を受け取って、メッセージ `Ok, turning on the tv` で応答する簡単なコマンドを作成してみましょう。

1. 左端のペインの上部にある `+ New command` アイコンを選択して、新しいコマンドを作成します。 **[新しいコマンド]** というタイトルの新しいポップアップが表示されます。
1. **[名前]** フィールドの値に `TurnOn` を指定します。
1. **［作成］** を選択します

中央のペインに、コマンドのさまざまなプロパティが一覧表示されます。


| 構成            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例文 | 発話の例では、ユーザーはこのコマンドを言ってトリガーすることができます。                                                                 |
| パラメーター       | コマンドを完了するために必要な情報                                                                                |
| 完了ルール | コマンドを完了するために実行されるアクション。 たとえば、ユーザーへの応答や、別の Web サービスとの通信などです。 |
| 相互作用ルール   | より具体的または複雑な状況に対処するための追加ルールです。                                                              |


> [!div class="mx-imgBorder"]
> ![コマンドの作成](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>例文を追加する

まず [例文] セクションから始めて、ユーザーが言うことができる文の例を示します。

1. 中央のペインから **[例文]** セクションを選択し、右端のペインで例を追加します。

    ```
    turn on the tv
    ```

1. このペインの上部に表示されている `Save` アイコンを選択します。

現時点では、パラメーターはないので、 **[完了ルール]** に移ります。

### <a name="add-a-completion-rule"></a>完了ルールの追加

次に、次の構成で完了ルールを追加します。 このルールは、フルフィルメント アクションが実行されていることをユーザーに示します。


| 設定    | 推奨値                          | 説明                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| 規則の名前  | ConfirmationResponse                  | ルールの目的を説明する名前          |
| 条件 | なし                                     | ルールを実行できるタイミングを決定する条件    |
| Actions    | SpeechResponse "- Ok, turning on the tv" (了解です。テレビをつけます) | ルール条件が真のときに実行するアクション |

1. 中央のペインの上部にある `+Add` アイコンを選択して、新しい完了ルールを作成します。
1. **[名前]** セクションに値を指定します。
1. アクションを追加する
   1. **[アクション]** セクションで **[+ アクションの追加]** を選択して、新しいアクションを作成します。
   1. **[新しいアクション]** のポップアップで、 **[種類]** のドロップダウン オプションから [`Send speech response`] を選択します。
   1. **[応答]** フィールドに `Simple editor` を選択します。
       - **[最初のバリエーション]** フィールドで、応答の値に `Ok, turning on the tv` を指定します。

   > [!div class="mx-imgBorder"]
   > ![音声応答の作成](media/custom-speech-commands/create-speech-response-action.png)

1. **[保存]** をクリックしてルールを保存します。
1. **[完了ルール]** セクションに戻り、 **[保存]** を選択してすべての変更を保存します。 

> [!div class="mx-imgBorder"]
> ![完了ルールの作成](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストする
1. 右側のペインの上部に表示されている `Train` アイコンを選択します。
1. トレーニングが完了したら、[`Test`] を選択します。 新しい **[アプリケーションのテスト]** ウィンドウが表示されます。
    - 「turn on the tv」と入力します。
    - 予期される応答:Ok, turning on the tv (了解です。テレビをつけます)


> [!div class="mx-imgBorder"]
> ![Web チャットを使用したテスト](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クイック スタート: パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
