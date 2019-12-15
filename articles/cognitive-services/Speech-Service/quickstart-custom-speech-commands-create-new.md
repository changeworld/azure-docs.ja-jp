---
title: クイック スタート:カスタム コマンドを作成する (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: この記事では、ホストされたカスタム コマンド アプリケーションを作成してテストします。
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 5c90c91791af8a9a16039e9650765bd24433ff38
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2019
ms.locfileid: "74815824"
---
# <a name="quickstart-create-a-custom-command-preview"></a>クイック スタート:カスタム コマンドを作成する (プレビュー)

この記事では、ホストされたカスタム コマンド アプリケーションを作成してテストする方法について説明します。
このアプリケーションでは、"turn on the tv" (テレビをつけて) のような発話を認識し、簡単なメッセージ "Ok, turning on the tv".(了解です。テレビをつけます) で応答します。

## <a name="prerequisites"></a>前提条件

- Speech サブスクリプション [Speech Service を無料で試してください](~/articles/cognitive-services/speech-service/get-started.md)。

  > [!NOTE]
  > プレビュー期間中は、サブスクリプション キーに対して westus2 リージョンのみがサポートされています。

- [Language Understanding](https://www.luis.ai/home) (LUIS) オーサリング キー:
  1. Web ブラウザーを開いて [Azure Portal](https://portal.azure.com) に移動します。
  1. [リソースの作成] を選択します。
  1. [Language Understanding](https://aka.ms/sc-luis-all) を検索して選択します。
  1. 作成オプションで [作成] を選択します。
  1. リソースがデプロイされたら、リソースにアクセスして、[クイックスタート] または [キー] セクションからキーをコピーします。

      > [!div class="mx-imgBorder"]
      > ![オーサリング リソースを作成する](media/custom-speech-commands/resources-lu-authoring.png)

## <a name="go-to-the-speech-studio-for-custom-commands"></a>カスタム コマンドを使用するため Speech Studio にアクセスする

1. Web ブラウザーを開き、[Speech Studio](https://speech.microsoft.com/) に移動します。
1. 資格情報を入力してポータルにサインインします。

   - 既定のビューは、音声サブスクリプションの一覧です。
     > [!NOTE]
     > サブスクリプションの選択ページが表示されない場合は、上部のバーの [設定] メニューで [Speech resources]\(音声リソース\) を選択すると表示されます。

1. 音声サブスクリプションを選択し、 **[Go to Studio]\(Studio にアクセス\)** を選択します。
1. **[カスタム コマンド (プレビュー)]** を選択します。

既定のビューは、作成したカスタム コマンド アプリケーションの一覧です。

## <a name="create-a-custom-commands-project"></a>カスタム コマンド プロジェクトを作成する

1. **[新しいプロジェクト]** を選択して新しいプロジェクトを作成します。

   > [!div class="mx-imgBorder"]
   > ![新しいプロジェクトの作成](media/custom-speech-commands/create-new-project.png)

1. プロジェクトの名前と言語を入力し、 **[次へ]** を選択して続行します
1. LUIS のオーサリング キーを入力します。
1. 作成したら、プロジェクトを選択します

これで、カスタム コマンド アプリケーションの概要が表示されます。

## <a name="create-a-new-command"></a>新しいコマンドを作成する

これで、コマンドを作成できるようになりました。 1つの発話 `turn on the tv` を取得して、メッセージ `Ok, turning on the TV`で応答する例を使用してみましょう。

1. コマンドの横にある `+` アイコンを選択して `TurnOn` という名前を指定し、新しいコマンドを作成します。
1. **[保存]** を選びます。

> [!div class="mx-imgBorder"]
> ![コマンドの作成](media/custom-speech-commands/create-add-command.png)

コマンドは、次のセットです。

| Group            | 説明                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| 例文 | 発話の例では、ユーザーはこのコマンドを言ってトリガーすることができます。                                                                 |
| parameters       | コマンドを完了するために必要な情報                                                                                |
| 完了ルール | コマンドを完了するために実行されるアクション。 たとえば、ユーザーへの応答や、別の web サービスとの通信などです。 |
| 高度なルール   | より具体的または複雑な状況に対処するための追加ルールです。                                                              |

### <a name="add-a-sample-sentence"></a>例文の追加

ここでは、最初に例文を示し、ユーザーが言うことができる文の例を示します。

```
turn on the tv
```

現時点では、パラメーターはないので、完了ルールに移行できます。

### <a name="add-a-completion-rule"></a>完了ルールの追加

次に、完了ルールを追加し、アクションが実行されていることを示す応答をユーザーに返します。

> [!div class="mx-imgBorder"]
> ![完了ルールの作成](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Setting    | 推奨値                        | 説明                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| 規則の名前  | "ConfirmationResponse"                 | ルールの目的を説明する名前          |
| 条件 | なし                                   | ルールを実行できるタイミングを決定する条件    |
| Actions    | SpeechResponse "Ok, turning on the TV" (了解です。テレビをつけます) | ルール条件が真のときに実行するアクション |

## <a name="try-it-out"></a>試してみる

テスト チャット パネルを使用して動作をテストします。

> [!div class="mx-imgBorder"]
> ![Webchat を使用したテスト](media/custom-speech-commands/create-basic-test-chat.png)

- 「turn on the tv」と入力します。
- 予期される応答:Ok, turning on the tv (了解です。テレビをつけます)

## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [クイック スタート:パラメーターを使用してカスタム コマンドを作成する (プレビュー)](./quickstart-custom-speech-commands-create-parameters.md)
