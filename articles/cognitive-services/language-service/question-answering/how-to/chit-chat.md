---
title: カスタムの質問と回答ナレッジ ベースにおしゃべりを追加する
titleSuffix: Azure Cognitive Services
description: ボットに個人的なおしゃべりを追加すると、プロジェクトを作成するときに、より会話的で魅力的なものになります。 カスタムの質問と回答を使うと、定義済みのおしゃべりのセットを、ナレッジ ベースに簡単に追加できます。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: c925fdf1b5b53f703d7c996110fe1b6b5f52cde8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476587"
---
# <a name="use-chitchat-with-a-project"></a>プロジェクトでおしゃべりを使用する

ボットにおしゃべりを追加すると、ボットがより会話的で魅力的なものになります。 カスタムの質問と回答のおしゃべり機能を使うと、定義済みのおしゃべりのセットを、ナレッジ ベース (KB) に簡単に追加することができます。 これを使ってボットのパーソナリティの作成を始めることができ、それにより最初から作成するよりもコストと時間が節約されます。

このデータセットには、複数のペルソナ (Professional、Friendly、Witty など) の音声の約 100 のシナリオのおしゃべりが含まれています。 ボットの音声に最も近いペルソナを選択します。 ユーザー クエリを指定すると、質問と回答により、最も近い既知のおしゃべりの質問と回答への一致が試みられます。

さまざまなパーソナリティのいくつかの例を、次に示します。 すべてのパーソナリティの[データセット](https://github.com/microsoft/botframework-cli/blob/main/packages/qnamaker/docs/chit-chat-dataset.md)とパーソナリティの詳細を確認できます。

`When is your birthday?` のユーザー クエリでは、パーソナリティごとに定型の応答が用意されています。

<!-- added quotes so acrolinx doesn't score these sentences -->
|パーソナリティ|例|
|--|--|
|Professional|"Age doesn't really apply to me." (年齢は、私にはまったく当てはまりません。)|
|Friendly|"I don't really have an age." (私には、年齢なんて本当にありません。)|
|Witty|"I'm age-free." (私は、年齢にとらわれない存在です。)|
|Caring (温かい)|"I don't have an age." (私には、年齢はありません。)|
|Enthusiastic (熱心)|"I'm a bot, so I don't have an age." (私はボットですから、年齢はありません。)|
||

## <a name="language-support"></a>言語のサポート

おしゃべりデータ セットは、次の言語でサポートされています。

|Language|
|--|
|Chinese|
|英語|
|フランス語|
|ドイツ|
|イタリア語|
|日本語|
|韓国語|
|Portuguese|
|スペイン語|

## <a name="add-chitchat-source"></a>おしゃべりソースを追加する
プロジェクトを作成した後は、 **[Manage sources]\(ソースの管理\)** ペインで URL、ファイル、おしゃべりからソースを追加できます。

> [!div class="mx-imgBorder"]
> ![ソースのおしゃべりを追加する](../media/chit-chat/add-source.png)

おしゃべりのベースとして使うパーソナリティを選びます。

> [!div class="mx-imgBorder"]
> ![さまざまなおしゃべりのパーソナリティのメニュー](../media/chit-chat/personality.png)

## <a name="edit-your-chitchat-questions-and-answers"></a>おしゃべりの質問と回答を編集する

ナレッジ ベースを編集するときは、選んだパーソナリティに基づいて、おしゃべりの新しいソースが表示されます。 ここで、他のソースと同様に、変更した質問を追加したり返答を編集したりできます。

> [!div class="mx-imgBorder"]
> ![おしゃべりの質問ペアを編集する](../media/chit-chat/edit-chit-chat.png)

コンテキストとメタデータのビューをオンまたはオフにするには、ツール バーの **[列を表示]** を選びます。

## <a name="add-more-chitchat-questions-and-answers"></a>おしゃべりの質問と回答を追加する

定義済みのデータ セットに含まれていない、新しいおしゃべりの質問ペアを追加することができます。 既におしゃべりセットで対象となっている質問ペアは、複製しないようにしてください。 追加した新しいおしゃべり質問ペアは、 **[編集]** ソースに追加されます。 これがおしゃべりであることをランカーが確実に理解できるようにするには、次の図に示すように、メタデータのキーと値のペアである "Editorial: chitchat" を追加します。

:::image type="content" source="../media/chit-chat/add-new-chit-chat.png" alt-text="おしゃべりの質問と回答のペアを追加する" lightbox="../media/chit-chat/add-new-chit-chat.png":::

## <a name="delete-chitchat-from-your-project"></a>プロジェクトからおしゃべりを削除する

**[Manage sources]\(ソースの管理\)** ペインを選び、お使いのおしゃべりソースを選びます。 選んだパーソナリティ名を持つ特定のおしゃべりソースが、tsv ファイルとして表示されます。 ツールバーから **[削除]** を選択します。

> [!div class="mx-imgBorder"]
> ![おしゃべりソースを削除する](../media/chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースをインポートする](./migrate-knowledge-base.md)
