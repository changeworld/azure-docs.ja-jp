---
title: QnA Maker ナレッジ ベースへのおしゃべりの追加
titleSuffix: Azure Cognitive Services
description: ボットに個人的なおしゃべりを追加すると、KB を作成するときにより会話的で魅力的なものになります。 QnA Maker を使用すると、定義済みの最上位のおしゃべりのセットを、KB に簡単に追加することができます。 これを使ってボットのおしゃべりの作成を始めれば、最初から作成するよりもコストと時間を節約することができます。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/24/2018
ms.author: tulasim
ms.openlocfilehash: 6f542d095211242fcf6dcb13d4426534e32e57ab
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041729"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>ナレッジ ベースにおしゃべりを追加する

ボットにおしゃべりを追加すると、ボットがより会話的で魅力的なものになります。 QnA Maker のおしゃべり機能を使用すると、定義済みのおしゃべりのセットを、ナレッジ ベース (KB) に簡単に追加することができます。 これを使ってボットのパーソナリティの作成を始めることができ、それにより最初から作成するよりもコストと時間が節約されます。  

このデータセットには、3 つのペルソナ (プロフェッショナル、フレンド、コミック) の音声の約 100 のシナリオのおしゃべりが含まれています。 ボットの音声に最も近いペルソナを選択します。 ユーザー クエリを指定すると、QnA Maker がそれを、最も近い既知のおしゃべり QnA と一致させようとします。 

さまざまなパーソナリティのいくつかの例を、次に示します。<!-- added quotes so acrolinx doesn't score these sentences -->
|ユーザー クエリ|プロフェッショナル|フレンド|コミック|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Who made you?`|`There wouldn't be time to list everyone.`|`So many people!`|`Nerds.`|


> [!NOTE]
> おしゃべりのサポートは、現時点では英語でのみ提供されています。 

## <a name="add-chit-chat-during-kb-creation"></a>KB 作成時におしゃべりを追加する
ナレッジ ベースの作成中に、ソース URL とファイルを追加した後で、おしゃべりの追加を選択することができます。 おしゃべりのベースとして使用するパーソナリティを選択します。 おしゃべりを追加しない場合、またはデータ ソース内におしゃべりのサポートが既にある場合は、**[なし]** を選択します。 
   
![作成時におしゃべりを追加する](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>既存の KB におしゃべりを追加する
KB を選択し、**[設定]** ページに移動します。 適切な **.tsv** 形式のすべてのおしゃべりデータセットへのリンクがあります。 目的のパーソナリティをダウンロードして、ファイル ソースとしてアップロードします。 ファイルをダウンロードおよびアップロードするときは、形式またはメタデータを編集しないでください。 
  
![既存の KB におしゃべりを追加する](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>おしゃべりの質問や返答を編集する
KB を編集するときは、選択したパーソナリティに基づいて、おしゃべりの新しいソースが表示されます。 ここで、他のソースと同様に、変更した質問を追加したり返答を編集したりできます。 

![おしゃべり QnA を編集する](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>追加のおしゃべりの質問と返答を追加する
定義済みのセットに含まれていない、新しいおしゃべり QnA を追加することができます。 既におしゃべりセットで対象となっている QnA ペアは、複製しないようにしてください。 追加した新しいおしゃべり QnA は、**[編集]** ソースに追加されます。 これがおしゃべりであることをランカーが確実に理解できるようにするには、次の図に示すように、メタデータ キー/値ペアである "編集: おしゃべり" を追加します。
   
![おしゃべり QnAs を追加する](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>既存の KB からおしゃべりを削除する
KB を選択し、**[設定]** ページに移動します。 選択したパーソナリティ名を持つ特定のおしゃべりソースが、ファイルとして表示されます。 ソース ファイルとしてこれを削除することができます。

![KB からおしゃべりを削除する](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [ナレッジ ベースをインポートする](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>関連項目 

[QnA Maker の概要](../Overview/overview.md)
