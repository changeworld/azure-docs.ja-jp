---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 8fb9e276c7f8d0e195c7d7618392e7c5e61c7c9e
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132590590"
---
## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services)。

## <a name="sign-in-to-language-studio"></a>Language Studio にサインインする

1. [Language Studio](https://aka.ms/languageStudio) にアクセスし、Azure アカウントでサインインします。 

2. 表示される **[Choose a language resource]\(言語リソースの選択\)** ウィンドウで、自分の Azure サブスクリプションを見つけ、言語リソースを選びます。 リソースがない場合は、新しいものを作成できます。

    > [!NOTE]
    > 現在、会話言語理解サービスで使用できるのは、Standard (**S**) 価格レベルのリソースのみです。
    
    :::image type="content" source="../../media/quickstart-language-resource.png" alt-text="Language Studio のリソースの選択画面を示すスクリーンショット。" lightbox="../../media/quickstart-language-resource.png":::

## <a name="create-a-conversation-project"></a>会話プロジェクトを作成する

アカウントに言語リソースを関連付けたら、会話言語理解プロジェクトを作成します。 このクイックスタートでは、特定のからのメールを読む、メールを削除する、メールにドキュメントを添付するなど、メールに関するコマンドを識別できるプロジェクトを作成します。

1. [Language Studio](https://aka.ms/languageStudio) で、 **[Understand conversational language]\(会話言語を理解する\)** というセクションを見つけ、 **[会話言語理解]** をクリックします。  
    :::image type="content" source="../../media/select-custom-clu.png" alt-text="Language Studio のランディングページのカスタム Language Understanding の場所を示すスクリーンショット。" lightbox="../../media/select-custom-clu.png"::: 
    

2. これにより、 **[Conversations project]\(会話プロジェクト\)** ページが表示されます。 **[新しいプロジェクトの作成]** をクリックします。 **[Conversations project]\(会話プロジェクト\)** を選び、 **[次へ]** をクリックします。

    :::image type="content" source="../../media/projects-page.png" alt-text="Language Studio の [Conversations project]\(会話プロジェクト\) ページのスクリーンショット。" lightbox="../../media/projects-page.png":::


次に、以下の詳細を指定する必要があります。

|値  | 説明  |
|---------|---------|
|名前     | プロジェクトの名前。        |
|説明    | オプションのプロジェクトの説明。        |
|Text primary language (テキストの第一言語)     | プロジェクトの第一言語。 トレーニング データは、主にこの言語にする必要があります。 このクイックスタートでは、 **[英語]** を選びます。        |
|Enable multiple languages (複数の言語を有効にする)     |  複数の言語の同時サポートをプロジェクトで有効にするかどうか。 このクイックスタートでは、このオプションを有効にします。       |

終わったら、 **[次へ]** をクリックし、詳細を確認し、 **[プロジェクトの作成]** をクリックしてプロセスを完了します。 プロジェクトの **[Build Schema]\(スキーマのビルド\)** 画面が表示されます。

## <a name="build-schema"></a>スキーマの作成

1. **[Build Schema]\(スキーマのビルド\)** ページの [Intents]\(意図\) または [エンティティ] タブを選び、 **[追加]** をクリックします。 意図またはエンティティの作成を完了する前に、名前の入力を求められます。

2. 次の名前で 3 つの意図を作成します。
    - **読み取り**
    - **削除**
    - **[アタッチ]**

3. 次の名前で 3 つのエンティティを作成します。
    - **送信者**
    - **FileName**
    - **FileType**


意図をクリックすると、[[tag utterances]\(発話にタグ付けする\)](../../how-to/tag-utterances.md) ページが表示され、意図の例を追加したり、エンティティを使用してラベルを付けたりすることができます。


:::image type="content" source="../../media/quickstart-intents.png" alt-text="Language Studio のスキーマ ページを示すスクリーンショット。" lightbox="../../media/quickstart-intents.png":::

## <a name="tag-utterances"></a>発話にタグ付けする

[tag utterances]\(発話にタグ付けする\) ページで、意図にいくつかの例を追加しましょう。 **[Select Intent]\(意図の選択\)** というドロップダウン ボックスから意図の **[Read]\(読む\)** を選びます。

**[Write your example utterance and press enter]\(発話例を入力し、Enter キーを押してください\)** というテキスト ボックスに「*read the email from Carol*」(Carol からのメールを読んで) という文を入力し、Enter キーを押して発話例として追加します。

"*Carol*" という単語にカーソルをドラッグして **[送信者]** エンティティを選び、"*Carol*" にエンティティとしてラベルを付けます。

次の意図とエンティティを使用して、これらの発話の残りを追加します。

|発話|Intent|エンティティ|
|--|--|--|
|*John のメールを読んで*|**読み取り**|"John": **送信者**|
|*Matt からのメールはどのような内容でしたか*|**読み取り**|"Matt": **送信者**|
|*Martha からの最後のメールを削除して*|**削除**|"Martha": **送信者**|
|*これを削除して*|**削除**|_エンティティがありません_|
|*こちらを削除して*|**削除**|_エンティティがありません_|
|*これを削除済みフォルダーに移動して*|**削除**|_エンティティがありません_|
|*reports q1 という Excel ファイルを添付して*|**[アタッチ]**|"excel": **FileType**、 <br> "reports q1" -> **FileName**|
|*PowerPoint ファイルを添付して*|**[アタッチ]**|"PowerPoint": **FileType**|
|*署名付きコントラクトという名前の PDF ファイルを追加して* |**[アタッチ]**|"PDF": **FileType**、 <br> "署名済みコントラクト": **FileName**|


完了したら、 **[変更内容の保存]** をクリックして、発話とラベルをプロジェクトに保存します。 変更が保存されると、ボタンの横のアイコンが緑色に変わります。 次に、 **[モデルのトレーニング]** ページに移動します。

:::image type="content" source="../../media/quickstart-utterances.png" alt-text="Language Studio の意図のタグ付け画面を示すスクリーンショット。" lightbox="../../media/quickstart-utterances.png":::

## <a name="train-your-model-and-view-its-details"></a>モデルをトレーニングし、その詳細を確認する

画面の左にある **[モデルのトレーニング]** をクリックします。 モデルをトレーニングするには、モデルの名前を指定する必要があります。 「*v0.1*」などの名前を入力し、Enter キーを押します。 

**[トレーニング]** をクリックする前に **[Run evaluation with training]\(評価とトレーニングを実行する\)** をオフにします。 

**[View model details]\(モデルの詳細を表示する\)** ページが表示されます。 トレーニングが完了するまで待ちます。所要時間は約 5 分です。 トレーニングが成功したら、画面の左側にある **[Deploy Model]\(モデルのデプロイ\)** を選びます。

## <a name="deploy-your-model"></a>モデルをデプロイする

画面の左側にある **[Deploy Model]\(モデルのデプロイ\)** ページからトレーニング済みモデルを選び、 **[Deploy model]\(モデルのデプロイ\)** ボタンをクリックします。 表示された画面で **[デプロイ]** をクリックします。

## <a name="test-your-model"></a>モデルのテスト

画面の左側にある **[Test model]\(モデルのテスト\)** をクリックし、モデルのリンクを選びます。 「*trash this one*」(これをごみ箱に入れて) という発話を入力し、 **[Run the test]\(テストの実行\)** をクリックします。 

これで、一番上の意図がエンティティのない "**削除**" と表示されます。

次のような他の発話をテストすることができます。
* 「*attach my docx file*」(docx ファイルを添付して)、 
* 「*read the email by Jason*」(Jason からのメールを読んで)、 
* 「*attach the ppt file named CLU demo*」(CLU デモという ppt ファイルを添付して)。

また、次のフレーズなど、他の言語の発話をテストすることもできます。

* 「*Joindre le fichier PDF*」(フランス語: 「*PDF ファイルを添付して*」)、 
* "*lesen sie die e-mail von Macy*" (ドイツ語: 「*Macy のメールを読んで*」)
