---
title: LUIS アプリで発話の例を追加する | Microsoft Docs
titleSuffix: Azure
description: Language Understanding (LUIS) アプリケーションで発話を追加する方法を学習します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: cf6fc131a1ae2ba0a77bdbeda942c9f85aadd189
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39226289"
---
# <a name="add-example-utterances-and-label-with-entities"></a>発話の例を追加し、エンティティ ラベルを付ける

発話の例とは、ユーザーの質問や命令のサンプル テキストです。 Language Understanding (LUIS) に学習させるには、[意図](luis-concept-intent.md)に[発話の例](luis-concept-utterance.md)を追加する必要があります。

一般的に、まず、発話の例を意図に追加し、それから意図ページでエンティティを作成し、発話にラベルを付けます。 先にエンティティを作成する場合、[エンティティの追加](luis-how-to-add-entities.md)に関するページを参照してください。

## <a name="add-an-utterance"></a>発話を追加する
意図ページで、意図の名前の下にあるテキスト ボックスに、ユーザーから想定される発話の例 (`book 2 adult business tickets to Paris tomorrow on Air France` など) を入力します。 
 
>[!NOTE]
>LUIS によって、すべて発話が小文字に変換されます。

![意図の詳細ページのスクリーンショット、発話が強調表示されています](./media/luis-how-to-add-example-utterances/add-new-utterance-to-intent.png) 

現在の意図の発話一覧に発話が追加されます。 

## <a name="ignoring-words-and-punctuation"></a>単語と句読点を無視する
発話の例にある特定の単語や句読点を無視する場合、[パターン](luis-concept-patterns.md#pattern-syntax)と _ignore_ 構文を使用します。 

## <a name="add-simple-entity-label"></a>シンプル エンティティ ラベルを追加する
次の手順では、意図ページの次の発話内でカスタム エンティティを作成し、ラベルを付けます。

```
book me 2 adult business tickets to Paris tomorrow on Air France
```

1. 発話から "Air France" を選択し、シンプル エンティティのラベルを付けます。

    > [!NOTE]
    > エンティティ ラベルを付ける単語を選択するときは:
    > * 1 つの単語の場合、単純にそれを選択します。 
    > * 2 語以上からなる場合、先頭を選択し、それから末尾を選択します。

2. エンティティ ドロップダウン ボックスが表示されたら、既存のエンティティを選択するか、新しいエンティティを追加できます。 新しいエンティティを追加するには、テキスト ボックスにその名前を入力し、**[新しいエンティティの作成]** を選択します。 
 
    ![意図の詳細ページのスクリーンショット、シンプル エンティティのラベル付けオプションが強調表示されています](./media/luis-how-to-add-example-utterances/create-airline-simple-entity.png)

3. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ポップアップ ダイアログ ボックスで、エンティティ名を確認し、エンティティの種類としてシンプルを選択し、**[完了]** を選択します。

    ![確認ダイアログの画像](./media/luis-how-to-add-example-utterances/create-simple-airline-entity.png)

    エンドポイント JSON クエリ応答からシンプル エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#simple-entity-data)に関するページを参照してください。 シンプル エンティティの使用方法の詳細については、シンプル エンティティの[クイック スタート](luis-quickstart-primary-and-secondary-data.md)をお試しください。


## <a name="add-list-entity-and-label"></a>リスト エンティティを追加し、ラベルを付ける
リスト エンティティとは、システムにおける関連単語の集まりであり、固定かつ限定的です (テキスト厳密一致)。 

飲み物のリスト エンティティの場合、水とソーダ水という正規値を 2 つ与えることができます。 正規名にはそれぞれシノニム (類義語) があります。 水の場合、H2O、炭酸入り、炭酸なしがシノニムとなります。 ソーダ水の場合、果汁、コーラ、ジンジャーエールがシノニムになります。 エンティティを作成するとき、すべての値を知っている必要はありません。 実際にユーザーがシノニムで発話するのを見てから追加できます。

|正規値|シノニム|
|--|--|
|水|H2O、炭酸入り、炭酸なし|
|ソーダ水|果汁、コーラ、ジンジャーエール|

意図ページから新しいリスト エンティティを作成するとき、2 つのことを行いますが、それは自明のことではないかもしれません。 まず、最初のリスト アイテムを追加することで新しいリストを作成します。 次に、発話から選択した単語や語句で最初のリスト アイテムに名前を付けます。 これらは後でエンティティ ページから変更できますが、リスト アイテムの名前には、必要な単語を含む発話を選択すると、時間の節約になることがあります。

たとえば、飲み物の種類のリストを作成するとき、発話から `h2o` という単語を選択し、そのエンティティを作成すると、リストには h2o という名前のアイテムが 1 つ与えられます。 もっと汎用的な名前が必要であれば、そのような名前を使っている発話を選択してください。 

1. 発話では、リストの最初のアイテムになっている単語を選択し、テキストボックスにリストの名前を入力し、**[新しいエンティティの作成]** を選択します。   

    ![意図の詳細ページのスクリーンショット、[新しいエンティティの作成] が強調表示されています](./media/luis-how-to-add-example-utterances/create-drink-list-entity.png)

2. **[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\)** ダイアログ ボックスで、このリスト アイテムのシノニムを追加します。 飲み物のリストのアイテム "water" の場合、`h20`、`perrier`、`waters` を追加し、**[完了]** を選択します。 リスト シノニムがトークン レベルで一致するために "water" が追加されることにご注意ください。 英語では、トークン レベルは単語レベルとなります。つまり、リストにない限り、"waters" は "water" に一致しません。 

    ![[What type of entity do you want to create?]\(どのような種類のエンティティを作成しますか\) ダイアログ ボックスのスクリーンショット](./media/luis-how-to-add-example-utterances/drink-list-ddl.png)

    この飲み物リストには飲み物の種類が 1 つだけあります。"water" です。 他の発話にラベルを付けるか、左のナビゲーションにある **[エンティティ]** からエンティティを編集することで、飲み物の種類を追加できます。 エンティティを[編集](luis-how-to-add-entities.md#add-list-entities)するとき、対応するシノニムと共に追加アイテムを入力したり、リストを[インポート](luis-how-to-add-entities.md#import-list-entity-values)したりできます。 

    エンドポイント JSON クエリ応答からリスト エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#list-entity-data)に関するページを参照してください。 リスト エンティティを使用する方法の詳細については、[クイック スタート](luis-quickstart-intent-and-list-entity.md)をお試しください。

## <a name="add-synonyms-to-the-list-entity"></a>リスト エンティティにシノニムを追加する 
発話の中の単語または語句を選択することでリスト エンティティにシノニムを追加します。 リスト エンティティ "Drink" があるとき、water のシノニムとして `agua` を追加する場合、次の手順を行います。

発話で同義語を選択し (water の場合は `aqua` など)、ドロップダウン リストでリスト エンティティ名を選択し (**Drink** など)、**[Set as synonym]\(シノニムとして設定\)** を選択し、それの同義となるリスト アイテムを選択します (**water** など)。

![意図の詳細ページのスクリーンショット、[Create a new synonym]\(新しいシノニムの作成\) が強調表示されています](./media/luis-how-to-add-example-utterances/set-agua-as-synonym.png)

## <a name="create-new-item-for-list-entity"></a>リスト エンティティのアイテムを新規作成する
発話の中の単語または語句を選択することで既存のリスト エンティティのアイテムを新規作成します。 リスト "Drink" があるとき、新しいアイテムとして `tea` を追加する場合、次の手順を行います。

発話で、新しいリスト アイテムの単語を選択し (`tea` など)、ドロップダウン リストでリスト エンティティ名を選択し (**Drink**)、**[Create a new synonym]\(新しいシノニムの作成\)** を選択します。 

![新しいリスト アイテム追加のスクリーンショット](./media/luis-how-to-add-example-utterances/list-entity-create-new-item.png)

単語が青で強調表示されました。 単語の上にマウス カーソルを合わせると、リスト アイテム名を示すタグが表示されます。下の画像では tea です。

![新しいリスト アイテム タグのスクリーンショット](./media/luis-how-to-add-example-utterances/list-entity-item-name-tag.png)

## <a name="wrap-entities-in-composite-label"></a>複合ラベルでエンティティをラップする
複合エンティティは **[エンティティ]** から作成されます。 意図ページから複合エンティティを作成することはできません。 複合エンティティが作成されたら、意図ページで、発話でエンティティをラップすることができます。 

`book 2 tickets from Seattle to Cairo` という発話の場合、複合発話でチケットの枚数 (2)、出発地 (Seattle)、到着地 (Cairo) というエンティティ情報を 1 つの親エンティティで返すことができます。 

事前構築済みエンティティ **number** を追加するには、こちらの[手順](luis-how-to-add-entities.md#add-prebuilt-entity)を実行します。 エンティティが作成されたら、発話の `2` が青になり、エンティティにラベルが付いていることを示します。 事前構築済みエンティティには LUIS によってラベルが付けられます。 1 つの発話に対して事前構築済みエンティティ ラベルを追加したり、削除したりすることはできません。 事前構築済みラベルはすべて、アプリケーションに対して事前構築済みエンティティを追加または削除する方法でのみ追加または削除できます。

階層構造エンティティ **Location** を作成するには、こちらの[手順](#add-hierarchical-entity-and-label)を実行します。 発話の例で出発地と到着地にラベルを付けます。 

複合エンティティでエンティティをラップする前に、すべての子エンティティが青で強調表示されていること、つまり、発話でラベルが付けられていることを確認します。

1. 個々のエンティティをラップして複合エンティティにするには、複合エンティティの発話で最初のラベル付きエンティティを選択します。 発話の例 `book 2 tickets from Seattle to Cairo` の場合、最初のエンティティは数字の 2 です。 ドロップダウン リストが表示され、この選択の選択肢が示されます。

    ![このスクリーンショットでは、number が選択され、ドロップダウン オプションが強調表示されています](./media/luis-how-to-add-example-utterances/wrap-1.png)

2. ドロップダウン リストから **[Wrap composite entity]\(複合エンティティをラップする\)** を選択します。 

    ![複合エンティティをラップするためのドロップダウン オプションのスクリーンショット、[Wrap composite entity]\(複合エンティティをラップする\) が強調表示されています](./media/luis-how-to-add-example-utterances/wrap-2.png)

3. 複合エンティティの最後の単語を選択します。 この例の発話では、"Location::Destination" (Cairo を表す) を選択します。 複合となっている発話では、エンティティ以外の単語も含め、すべての単語に緑の下線が付きます。

    ![BookFlight 意図ページのスクリーンショット、number が強調表示されています](./media/luis-how-to-add-example-utterances/wrap-composite.png)

4. ドロップダウン リストから複合エンティティ名を選択します。 この例では、**TicketOrder** です。

    ![複合エンティティで単語をラップしているスクリーンショット、ドロップダウン リストで複合エンティティ名が強調表示されています](./media/luis-how-to-add-example-utterances/wrap-4.png)

    エンティティが正しくラップされていると、語句全体に緑の下線が付きます。

    ![発話のスクリーンショット、複合エンティティが強調表示されています](./media/luis-how-to-add-example-utterances/wrap-5.png)

    エンドポイント JSON クエリ応答から複合エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#composite-entity-data)に関するページを参照してください。 複合エンティティの使用方法の詳細については、複合エンティティ [チュートリアル](luis-tutorial-composite-entity.md)をお試しください。

## <a name="add-hierarchical-entity-and-label"></a>階層構造エンティティを追加し、ラベルを付ける
階層構造エンティティは、文脈から学習され、概念的に関連付けられる部類のエンティティです。 次の例では、エンティティに出発地と到着地が含まれます。 

発話 `Book 2 tickets from Seattle to Cairo` では、シアトルが出発地で、カイロが到着地です。 いずれの場所も文脈的に異なり、発話の中の単語の順序と選択から学習されます。

1. 意図ページの発話で、"Seattle" を選択し、エンティティ名 "Location" を入力し、**[新しいエンティティの作成]** を選択します。

    ![階層構造エンティティのラベルを作成するダイアログ ボックスのスクリーンショット](./media/luis-how-to-add-example-utterances/create-hier-ent-1.png)

2. ポップアップ ダイアログ ボックスで、**[エンティティ型]** に階層を選択し、子として `Origin` と `Destination` を追加し、**[完了]** を選択します。

    ![意図の詳細ページのスクリーンショット、到着地エンティティが強調表示されています](./media/luis-how-to-add-example-utterances/create-location-hierarchical-entity.png)

3. 発話の中の単語に親階層構造エンティティのラベルが付けられました。 その単語を子エンティティに割り当てる必要があります。 意図ページで発話に戻ります。 単語を選択し、選択したエンティティ名をドロップダウン リストから選択し、メニューを右にたどり、正しい子エンティティを選択します。

    ![意図の詳細ページのスクリーンショット、到着地エンティティが強調表示されています](./media/luis-how-to-add-example-utterances/label-tolocation.png)

    >[!CAUTION]
    >子エンティティ名は、1 つのアプリのエンティティ全体で一意となる必要があります。 2 つの異なる階層構造エンティティには、同じ名前の子エンティティを含めることができません。 

    エンドポイント JSON クエリ応答から階層構造エンティティを抽出する方法の詳細については、[データの抽出](luis-concept-data-extraction.md#hierarchical-entity-data)に関するページを参照してください。 階層構造エンティティの使用方法の詳細については、階層構造エンティティの[クイック スタート](luis-quickstart-intent-and-hier-entity.md)をお試しください。


## <a name="remove-entity-labels-from-utterances"></a>発話からエンティティ ラベルを削除する
機械学習したエンティティ ラベルは意図ページで発話から削除できます。 エンティティが機械学習ではない場合、発話から削除できません。 機械学習ではないエンティティを発話から削除する必要がある場合、アプリ全体からエンティティを削除する必要があります。 

機械学習したエンティティ ラベルを発話から削除するには、発話でそのエンティティを選択します。 次に、エンティティ ドロップダウン ボックスが表示されるのでそこから **[ラベルの削除]** を選択します。

![意図の詳細ページのスクリーンショット、[ラベルの削除] が強調表示されています](./media/luis-how-to-add-example-utterances/remove-label.png) 

## <a name="add-prebuilt-entity-label"></a>事前構築済みエンティティ ラベルを追加する
事前構築済みエンティティを LUIS アプリに追加する場合、発話にそのエンティティのラベルを付ける必要はありません。 事前構築済みエンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-prebuilt-entity)に関するページを参照してください。

## <a name="add-regular-expression-entity-label"></a>正規表現エンティティ ラベルを追加する
正規表現エンティティを LUIS アプリに追加する場合、発話にそのエンティティのラベルを付ける必要はありません。 正規表現エンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-regular-expression-entities)に関するページを参照してください。

## <a name="create-a-pattern-from-an-utterance"></a>発話からパターンを作成する
[意図またはエンティティ ページで既存の発話からパターンを追加する](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page)方法に関するページを参照してください。

## <a name="add-patternany-entity-label"></a>pattern.any エンティティ ラベルを追加する
pattern.any エンティティを LUIS アプリに追加する場合、発話にそのエンティティのラベルを付けることはできません。 pattern.any エンティティはパターンでのみ有効です。 pattern.any エンティティとその追加方法に関する詳細については、[エンティティの追加](luis-how-to-add-entities.md#add-patternany-entities)に関するページを参照してください。

<!--
Fix this - moved to luis-how-to-add-intents.md - how ?

## Search in utterances
## Prediction discrepancy errors
## Filter by intent prediction discrepancy errors
## Filter by entity type
## Switch to token view
## Delete utterances
## Edit an utterance
## Reassign utterances

-->
## <a name="train-your-app-after-changing-model-with-utterances"></a>発話を含むモデルの変更後にアプリをトレーニングする
発話を追加、編集、または削除したら、その変更について、エンドポイントのクエリに影響を与えるようにアプリを[トレーニング](luis-how-to-train.md)して[発行](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

意図で発話にラベルを付けたら、[複合エンティティ](luis-how-to-add-entities.md)を作成できます。
