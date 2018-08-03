---
title: LUIS アプリケーションでの意図の追加 | Microsoft Docs
description: Language Understanding (LUIS) を使用して意図を追加し、アプリがユーザー要求を解釈して、適切に対応できるようにします。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 0ebf15ea49467674ab3c56aa7983131593cf5c9a
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39225888"
---
# <a name="manage-intents"></a>意図の管理 
[意図](luis-concept-intent.md)を LUIS アプリに追加して、同じ意図を持つ質問またはコマンドのグループを特定します。 

**[Intents]\(意図\)** ページで、ご自身の意図を追加して管理します。このページには、LUIS の左側のパネルの **[Intents]\(意図\)** からアクセスできます。 

次の手順では、TravelAgent アプリに "Bookflight" 意図を追加する方法を示します。

## <a name="add-intent"></a>意図を追加する

1. **[マイ アプリ]** ページでアプリ (TravelAgent など) の名前をクリックして開き、左側のパネルで **[Intents]\(意図\)** をクリックします。 
2. **[Intents]\(意図\)** ページで、**[Create new intent]\(意図の新規作成\)** をクリックします。

    ![意図の一覧](./media/luis-how-to-add-intents/IntentsList.png)
3. **[Create new intent]\(意図の新規作成\)** ダイアログ ボックスで、意図の名前として「BookFlight」と入力し、**[完了]** をクリックします。

    ![意図の追加](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    新しく追加した意図の詳細ページで、[発話を追加](#add-an-utterance-on-intent-page)します。

## <a name="rename-intent"></a>意図の名前を変更する

1. **[Intent]\(意図\)** ページで、意図の名前の横にある名前の変更アイコン ![意図の名前の変更](./media/luis-how-to-add-intents/Rename-Intent-btn.png) をクリックします。 

2. **[Intent]\(意図\)** ページで、現在の意図の名前がダイアログ ボックスに表示されます。 意図の名前を編集し、Enter キーを押します。 新しい名前が保存され、意図のページに表示されます。

    ![意図の編集](./media/luis-how-to-add-intents/EditIntent-dialogbox.png)

## <a name="delete-intent"></a>意図を削除する
意図 [None]\(なし\) 以外の意図を削除するときは、すべての発話を意図 [None]\(なし\) に追加します。 これは、発話を削除するのではなく、移動する必要があるときに便利です。   

1. **[Intent]\(意図\)** ページで、意図の名前の右側にある **[Delete Intent]\(意図の削除\)** をクリックします。 

    ![[Delete Intent]\(意図の削除\) ボタン](./media/luis-how-to-add-intents/DeleteIntent.png)

2. 確認のダイアログ ボックスで [OK] をクリックします。

<!--
    TBD: waiting for confirmation about which delete dialog is going to be in //BUILD

    ![Delete Intent Dialog](./media/luis-how-to-add-intents/DeleteIntent-Confirmation.png)
-->


## <a name="add-an-utterance-on-intent-page"></a>意図ページで発話を追加する

意図ページで、意図の名前の下にあるテキスト ボックスに、該当するユーザーから想定される関連する発話 (`book 2 adult business tickets to Paris tomorrow on Air France` など) を入力します。 
 
>[!NOTE]
>LUIS によって、すべて発話が小文字に変換されます。

![意図の詳細ページのスクリーンショット、発話が強調表示されています](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

現在の意図の発話一覧に発話が追加されます。 発話が追加されたら、発話内で[任意のエンティティにラベルを付け](luis-how-to-add-example-utterances.md)て、ご自身のアプリを[トレーニング](luis-how-to-train.md)します。 

## <a name="create-a-pattern-from-an-utterance"></a>発話からパターンを作成する
[予測の正確さを向上するためにパターンを追加する方法](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)に関するページをご覧ください。

## <a name="edit-an-utterance-on-intent-page"></a>意図ページで発話を編集する

発話を編集するには、その発話の行の右端にある省略記号 (***...***) ボタンを選択してから、**[編集]** を選択します。 テキストを編集し、キーボードで Enter キーを押します。

![省略記号 [...] ボタンが強調表示されている意図の詳細ページのスクリーンショット](./media/luis-how-to-add-intents/edit-utterance.png) 

## <a name="reassign-utterances-on-intent-page"></a>意図ページで発話を再割り当てする
1 つまたは複数の発話の意図を変更するには、その発話を別の意図に再割り当てします。 

1 つの発話を別の意図に再割り当てするには、発話の行の右端にある **[Labeled intent]\(ラベル付き意図\)** 列で、適切な意図の名前を選択します。 発話が、現在の意図の発話一覧から削除されます。 

![[Labeled intent]\(ラベル付き意図\) 列で発話の意図が選択されている BookFlight 意図ページのスクリーンショット](./media/luis-how-to-add-intents/reassign-1-utterance.png)

複数の発話の意図を変更するには、発話の左側にあるチェック ボックスをオンにして、**[Reassign intent]\(意図の再割り当て\)** を選択します。 一覧から適切な意図を選択します。

![発話がオンで、[Reassign intent]\(意図の再割り当て\) ボタンが強調表示されている BookFlight 意図ページのスクリーンショット](./media/luis-how-to-add-intents/delete-several-utterances.png) 

## <a name="delete-utterances-on-intent-page"></a>意図ページで発話を削除する

発話を削除するには、その発話の行の右端にある省略記号 (***...***) ボタンを選択してから、**[削除]** を選択します。 発話が、一覧および LUIS アプリから削除されます。

![[削除] オプションが強調表示されている意図の詳細ページのスクリーンショット](./media/luis-how-to-add-intents/delete-utterance-ddl.png)

複数の発話を削除するには、次の手順を実行します。

1. 発話の左側にあるチェック ボックスをオンにして、**[Delete utterance(s)]\(発話の削除\)** を選択します。 

    ![発話がオンで、[Delete utterance(s)]\(発話の削除\) ボタンが強調表示されている意図の詳細ページのスクリーンショット](./media/luis-how-to-add-intents/delete-several-utterances.png)

2. **[Delete utterances?]\(発話を削除しますか\)** ポップアップ ダイアログで **[完了]** を選択します。

## <a name="search-in-utterances-on-intent-page"></a>意図ページで発話を検索する
意図で、テキスト (単語またはフレーズ) を含む発話を検索できます。 たとえば、特定の単語を含むエラーに気が付き、その単語を含むすべての例を見つける必要があるとします。 

1. ツール バーの虫眼鏡アイコンを選択します。

    ![虫眼鏡の検索アイコンが強調表示されている意図ページのスクリーンショット](./media/luis-how-to-add-intents/magnifying-glass.png)

2. 検索テキスト ボックスが表示されます。 発話一覧の右上隅の検索ボックスに単語またはフレーズを入力します。 発話一覧が更新され、入力した検索テキストを含む発話のみが表示されます。 

    ![検索テキスト ボックスが強調表示されている意図ページのスクリーンショット](./media/luis-how-to-add-intents/search-textbox.png)

    検索を取り消して、発話の完全な一覧を復元するには、入力した検索テキストを削除します。 検索テキスト ボックスを閉じるには、ツール バーの虫眼鏡アイコンを再度選択します。

## <a name="prediction-discrepancy-errors-on-intent-page"></a>意図ページの予測の不一致エラー
意図の発話で、選択した意図と予測スコアの間に不一致が存在する場合があります。 LUIS では、この不一致がスコアと共に赤い枠内に表示されます。 

![予測不一致スコアが強調表示されている BookFlight 意図ページのスクリーンショット](./media/luis-how-to-add-intents/score-discrepancy.png) 

## <a name="filter-by-intent-prediction-discrepancy-errors-on-intent-page"></a>意図ページで意図の予測の不一致エラーによってフィルター処理する
意図の予測の不一致を含む発話のみが表示されるように発話一覧をフィルター処理するには、ツール バーで **[すべて表示]** から **[Errors only]\(エラーのみ\)** に切り替えます。 

## <a name="filter-by-entity-type-on-intent-page"></a>意図ページでエンティティ型によってフィルター処理する
ツール バーの **[Entity filters]\(エンティティ フィルター\)** ドロップダウンを使用して、発話をエンティティでフィルター処理します。 

![エンティティ型フィルターが強調表示されている意図ページのスクリーンショット](./media/luis-how-to-add-intents/filter-by-entities.png) 

フィルターを削除するには、ツール バーの下で、その単語またはフレーズが示されている青いフィルター ボックスを選択します。  
<!-- TBD: waiting for ux fix - bug in ux of prebuit entity number -- when filtering by it, it doesn't show the list -->

## <a name="switch-to-token-view-on-intent-page"></a>意図ページでトークン ビューに切り替える
エンティティ型の名前ではなくトークンを表示するには、**[Tokens View]\(トークン ビュー\)** に切り替えます。 キーボードで **Control + E** キーを押して切り替えることもできます。 

![[Token View]\(トークン ビュー\) が強調表示されている BookFlight 意図のスクリーンショット](./media/luis-how-to-add-intents/toggle-tokens-view.png)

## <a name="train-your-app-after-changing-model-with-intents"></a>意図を含むモデルの変更後にアプリをトレーニングする
意図を追加、編集、または削除したら、その変更がエンドポイントのクエリに反映されるようにアプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

意図をアプリに追加したら、次に、追加した意図に対して[発話の例](luis-how-to-add-example-utterances.md)を追加し始めます。 
