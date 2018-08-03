---
title: Node.js を使用して Conversation Learner モデルを作成する方法 - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Node.js を使用して Conversation Learner モデルを作成する方法について説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bc0cf0900ec9f87c75091b3bf219d92e0859aa1f
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282141"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Node.js を使用して Conversation Learner モデルを作成する

Conversation Learner を使うと、ボットを作成する複雑さが軽減されます。 ハイブリッド開発ワークフローを利用して、手書きコードと機械学習によりボットの作成に必要なコードの量を削減できます。 ユーザーがログインしているかどうかの確認や、ストア インベントリを確認するための API 要求の実行など、モデルの特定の固定された部分は引き続きコード化できます。 一方、状態とアクションの選択での他の変更は、ドメインの専門家や開発者によって提供される例のダイアログから学習できます。

## <a name="invitation-required"></a>招待が必要

*Project Conversation Learner にアクセスするには招待が必要です*。

Project Conversation Learner は、ボットに追加する SDK と、機械学習のために SDK がアクセスするクラウド サービスで構成されます。  現在、Project Conversation Leaner クラウド サービスにアクセスするには、招待が必要です。  まだ招待されていない場合は、[招待を要求](https://aka.ms/conversation-learner-request-invite)してください。  招待を受け取っていない場合は、クラウド API にアクセスできません。

## <a name="prerequisites"></a>前提条件

- Node 8.5.0 以降と npm 5.3.0 以降。 [https://nodejs.org](https://nodejs.org) からインストールします。
  
- LUIS のオーサリング キー:

  1. [http://www.luis.ai](http://www.luis.ai) にログインします。

  2. 右上の自分の名前クリックし、[設定] をクリックします

  3. 結果のページにオーサリング キーが表示されます

  (LUIS オーサリング キーは 2 つのロールを提供します。  第 1 に、Conversation Learner のオーサリング キーとして機能します。  第 2 に、Conversation Learner はエンティティの抽出に LUIS を使用します。LUIS オーサリング キーは、ユーザーに代わって LUIS モデルを作成するために使用されます)

- Google Chrome Web ブラウザー。 [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html) からインストールします。

- git。 [https://git-scm.com/downloads](https://git-scm.com/downloads) からインストールします。

- VSCode。 [https://code.visualstudio.com/](https://code.visualstudio.com/) からインストールします。 推奨されますが、必須ではありません。

## <a name="quick-start"></a>クイック スタート 

1. インストールしてビルドします。

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > `npm install` の間に次のエラーが発生した場合は無視してかまいません。`gyp ERR! stack Error: Can't find Python executable`

2. 構成します。

   `.env` という名前のファイルを `cl-bot-01` ディレクトリに作成します。  ファイルの内容は次のようになります。

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. ボットを開始します。

    ```
    npm start
    ```

    汎用の空のボットが `cl-bot-01/src/app.ts` で実行します。

3. Conversation Learner UI を実行します。

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. ブラウザーで http://localhost:5050 を開きます。 

Conversation Learner を使用するようになり、Conversation Learner のモデルを作成および教育できます。  

> [!NOTE]
> 起動時には、Project Conversation Learner は招待によって使用できます。  http://localhost:5050 で HTTP エラー `403` が表示される場合は、アカウントが招待されていないことを意味します。  [招待を要求](https://aka.ms/conversation-learner-request-invite)してください。

## <a name="tutorials-demos-and-switching-between-bots"></a>チュートリアル、デモ、およびボット間の切り替え

上記の手順で、汎用的な空のボットが開始しました。  代わりにチュートリアルまたはデモ ボットを実行するには、次のようにします。

1. Conversation Learner Web UI が開いている場合は、 http://localhost:5050/home のモデルの一覧に戻ます。
    
2. 別のボットが実行している場合は (`npm start` や `npm run demo-pizza` など)、停止します。  UI プロセスを停止したり、Web ブラウザーを閉じたりする必要はありません。

3. コマンドライン (上記の手順 2) からデモ ボットを実行します。  デモには次のものが含まれます。

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run tutorial-hybrid
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. まだ行っていない場合は、 http://localhost:5050/home を読み込むことによって Chrome で Conversation Learner Web UI に切り替えます。 

5. [Import tutorials] をクリックします (行う必要があるのは一度だけです)。  これには約 1 分かかり、すべてのチュートリアルの Conversation Learner モデルが Conversation Learner アカウントにコピーされます。

6. Conversation Learner UI で、開始したデモに対応するデモ モデルをクリックします。

デモのソース ファイルは `cl-bot-01/src/demos` にあります。

## <a name="create-a-bot-which-includes-back-end-code"></a>バックエンド コードを含むボットを作成する

1. Conversation Learner Web UI が開いている場合は、 http://localhost:5050/home のモデルの一覧に戻ます。
    
2. ボットが実行している場合は (`npm run demo-pizza` など)、停止します。  UI プロセスを停止したり、Web ブラウザーを閉じたりする必要はありません。

3. 必要な場合は、`cl-bot-01/src/app.ts` のコードを編集します。

4. ボットを再ビルドして再起動します。

    ```bash    
    npm run build
    npm start
    ```

5. まだ行っていない場合は、 http://localhost:5050/home を読み込むことによって Chrome で Conversation Learner Web UI に切り替えます。 

6. UI で新しい Conversation Learner モデルを作成し、学習を開始します。

7. `cl-bot-01/src/app.ts` のコードを変更するには、手順 2 から開始します。

## <a name="vscode"></a>VS Code

VSCode では、各デモおよび "空のボット" に対する実行構成が `cl-bot-01/src/app.ts` にあります。  `cl-bot-01` フォルダーを VSCode で開きます。

## <a name="advanced-configuration"></a>詳細な構成

テンプレート `.env.example` ファイルでは、サンプルを構成するために設定できる環境変数が示されています。

`.env` ファイルをプロジェクトのルートに追加することにより、コンピューターで実行している他のサービスと競合しないように、これらのポートを調整できます。

```bash
cp .env.example .env
```

これでは標準構成が使用されており、ボットをローカルに実行し、Conversation Learner を使って開始できます。  (後でボットを Bot Framework に展開するには、このファイルを編集する必要があります。)

## <a name="support"></a>サポート

- [Stack Overflow](https://stackoverflow.com) の質問に "microsoft cognitive" というタグを付けます
- [User Voice ページ](https://aka.ms/conversation-learner-uservoice)で機能を要求します
- [GitHub のリポジトリ](https://github.com/Microsoft/ConversationLearner-Samples)で問題を開きます

## <a name="contributing"></a>寄与

このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。 詳細については、[倫理規定についてよくある質問](https://opensource.microsoft.com/codeofconduct/faq/)を参照するか、[opencode@microsoft.com](mailto:opencode@microsoft.com) 宛てに質問またはコメントをお送りください。

## <a name="source-repositories"></a>ソース リポジトリ

- [conversationlearner-samples](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Hello world](./tutorials/1-hello-world.md)
