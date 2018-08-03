---
title: LUIS アプリでのパターン テンプレートの追加 | Microsoft Docs
titleSuffix: Azure
description: Language Understanding (LUIS) アプリケーションでパターン テンプレートを追加して予測精度を改善する方法を説明します。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: diberry;
ms.openlocfilehash: bf1931355fd873eaeac6c313b70717dfa99814c6
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222598"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>パターンを追加して予測精度を改善する方法
LUIS アプリでエンドポイント発話を受信したら、パターンの[概念](luis-concept-patterns.md)を使用して、単語の順序や選択にパターンが現れる発話の予測精度を改善します。 パターンでは[エンティティ](luis-concept-entity-types.md)とそのロールを使用して、特定のパターン構文によってデータが抽出されます。 

## <a name="add-template-utterance-to-create-pattern"></a>パターンを作成するためのテンプレート発話の追加
1. **[マイ アプリ]** ページでアプリの名前を選択してアプリを開き、左側のパネルの **[アプリのパフォーマンス向上]** の下で **[パターン]** を選択します。

    ![パターン一覧のスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. パターンの適切な意図を選択します。 

    ![意図の選択](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. テンプレート テキスト ボックスに、テンプレートの発話を入力し、Enter キーを押します。 エンティティ名を入力する場合は、正しいパターン エンティティ構文を使用します。 エンティティ構文は `{` で開始します。 エンティティの一覧が表示されます。 正しいエンティティを選択し、Enter キーを押します。 

    ![パターンのエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    ご自身のエンティティにロールが含まれている場合、エンティティ名の後ろに 1 つのコロン `:`を付けてロールを指定します (`{Location:Origin}` など)。 エンティティのロールの一覧が表示されます。 ロールを選択し、Enter キーを押します。 

    ![ロールを含むエンティティのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    正しいエンティティを選択したら、パターンを入力して、Enter キーを押します。 パターンの入力が完了したら、ご自身のアプリを[トレーニング](luis-how-to-train.md)します。

    ![両方の型のエンティティを含む入力済みパターンのスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>検索パターン
検索によって、指定したテキストを含むパターンを見つけることができます。  

1. 虫眼鏡アイコンを選択します。

    ![検索ツール アイコンが強調表示されているパターン ページのスクリーンショット](./media/luis-how-to-model-intent-pattern/search-icon.png)

    パターン一覧の右上隅にある検索ボックスに検索テキストを入力し、Enter キーを押します。 検索テキストを含むパターンのみが表示されるようにパターン一覧が更新されます。

    ![検索ボックスの検索テキストが強調表示されているパターン ページのスクリーンショット](./media/luis-how-to-model-intent-pattern/search-text.png)

    検索を取り消して、パターンの完全な一覧を復元するには、入力した検索テキストを削除します。

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>パターンの編集
1. パターンを編集するには、そのパターンの行の右端にある省略記号 (***...***) ボタンを選択してから、**[編集]** を選択します。 

    ![パターンの行にある [編集] メニュー項目のスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. テキスト ボックスに、すべての変更を入力します。 完了したら、Enter キーを押します。 パターンの編集が完了したら、ご自身のアプリを[トレーニング](luis-how-to-train.md)します。

    ![パターンの編集のスクリーンショット](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>別の意図への 1 つのパターンの再割り当て

1 つのパターンを別の意図に再割り当てするには、パターン テキストの右側にある意図リスト ボックスを選択し、別の意図を選択します。

![別の意図への 1 つのパターンの再割り当てのスクリーンショット](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>別の意図への複数のパターンの再割り当て

複数のパターンを別の意図に再割り当てするには、各パターンの左側にあるチェック ボックスをオンにするか、一番上のチェック ボックスをオンにします。 **[Reassign intent]\(意図の再割り当て\)** オプションがツール バーに表示されます。 パターンの適切な意図を選択します。 

![別の意図への複数のパターンの再割り当てのスクリーンショット](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>1 つのパターンの削除

1. パターンを削除するには、そのパターンの行の右端にある省略記号 (***...***) ボタンを選択してから、**[削除]** を選択します。 

    ![発話の削除のスクリーンショット](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. **[OK]** を選択して、削除を確定します。

    ![削除の確認のスクリーンショット](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>複数のパターンの削除

1. 複数のパターンを削除するには、各パターンの左側にあるチェック ボックスをオンにするか、一番上のチェック ボックスをオンにします。 **[Delete patterns(s)]\(パターンの削除\)** オプションがツール バーに表示されます。 **[Delete patterns(s)]\(パターンの削除\)** を選択します。  

    ![複数のパターンの削除のスクリーン ショット](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. **[Delete patterns]\(パターンの削除\)** の確認のダイアログが表示されます。 **[OK]** を選択して削除を完了します。

    ![複数のパターンの削除のスクリーン ショット](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>エンティティでのパターンの一覧のフィルター処理

特定のエンティティによってパターンの一覧をフィルター処理するには、パターンの上にあるツール バーで **[Entity filters]\(エンティティ フィルター\)** を選択します。 

![エンティティでのパターンのフィルター処理のスクリーンショット](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

フィルターが適用されると、ツール バーの下にエンティティ名が表示されます。 

## <a name="filter-pattern-list-by-intent"></a>意図でのパターンの一覧のフィルター処理

特定の意図によってパターンの一覧をフィルター処理するには、パターンの上にあるツー ルバーで **[Intent filters]\(意図フィルター\)** を選択します。 

![意図でのパターンのフィルター処理のスクリーンショット](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

フィルターが適用されると、ツール バーの下に意図名が表示されます。 

## <a name="remove-entity-or-intent-filter"></a>エンティティまたは意図フィルターの削除
パターンの一覧をフィルター処理すると、ツールバーの下にエンティティまたは意図の名前が表示されます。 フィルターを削除するには、名前を選択します。

![エンティティでフィルター処理されたパターンのスクリーンショット](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

フィルターが削除され、すべてのパターンが表示されます。 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>意図またはエンティティ ページでの既存の発話からのパターンの追加
**[Intent]\(意図\)** または **[エンティティ]** ページで既存の発話からパターンを作成できます。 意図またはエンティティ ページでは、すべての発話が一覧表示され、右側の列で **[編集]**、**[削除]**、**[Add as pattern]\(パターンとして追加\)** など、発話レベルのオプションにアクセスできます。

1. 選択された発話の行で、その発話の右にある省略記号 (***...***) ボタンを選択し、**[Add as pattern] (パターンとして追加)** を選択します。

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "[オプション] メニューでパターンの追加が強調表示されている発話テーブルのスクリーンショット")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. [構文規則](luis-concept-patterns.md#pattern-syntax)に従って、パターンを変更します。 選択した発話にエンティティのラベルが付いている場合、それらのエンティティは既に正しい構文でパターンに存在します。

    ![エンティティでフィルター処理されたパターンのスクリーンショット](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>パターンを含むモデルの変更後にアプリをトレーニング
パターンを追加、編集、削除、または再割り当てしたら、その変更について、エンドポイントのクエリに影響を与えるようにアプリを[トレーニング](luis-how-to-train.md)して[公開](luis-how-to-publish-app.md)します。 

## <a name="next-steps"></a>次の手順

* pattern.any およびロールで[パターンをビルド](luis-tutorial-pattern.md)する方法を確認します。
* お使いのアプリを[トレーニング](luis-how-to-train.md)する方法を確認します。