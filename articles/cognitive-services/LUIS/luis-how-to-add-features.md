---
title: フレーズ リスト - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467620"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>フレーズ リストを使用して単語リストのシグナルをブーストする

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語やフレーズがアプリ領域のボキャブラリの一部であるというヒントを提供することで、LUIS を助けます。 

[フレーズ リスト](luis-concept-feature.md)には、同じように処理する必要がある、同じクラスに属している値 (単語またはフレーズ) のグループが含まれています (都市名、製品名など)。 LUIS がそのうちの 1 つについて学習した内容が、他のすべてに自動的に適用されます。 このリストは、一致した単語の[リスト エンティティ](reference-entity-list.md) (テキストの完全一致) と同じものではありません。

フレーズ リストは、それらの単語に関する LUIS に対する 2 番目のシグナルとして、アプリ ドメインのボキャブラリを増強します。

[機能の概念](luis-concept-feature.md)を確認して、フレーズ リストを使用するタイミングと理由を理解します。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>フレーズ リストの追加

LUIS では、アプリごとに最大 10 のフリーズ リストが許可されています。 

1. **[My Apps]\(マイ アプリ\)** ページでアプリの名前をクリックしてアプリを開き、 **[ビルド]** をクリックし、アプリの左パネルで **[Phrase lists]\(フレーズ リスト\)** をクリックします。 

1. **[Phrase lists]\(フレーズ リスト\)** ページで、 **[Create new phrase list]\(新しいフレーズ リストの作成\)** をクリックします。 
 
1. **[Add Phrase List]\(フレーズ リストの追加\)** ダイアログ ボックスで、フレーズ リストの名前として `Cities` と入力します。 **[値]** ボックスにフレーズ リストの値を入力します。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    ![フレーズ リスト Cities の追加](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS は、フレーズ リストに追加する関連した値を提案することができます。 **[Recommend]\(推奨\)** をクリックすると、追加する値とセマンティック的に関連した提案値のグループが得られます。 提案値のどれかをクリックしてその値を追加するか、または **[すべて追加]** をクリックしてすべての提案値を追加することができます。

    ![フレーズ リストの提案値 - すべて追加](./media/luis-add-features/related-values.png)

1. 追加されるフレーズ リストの値が、交換して使用できる代替値である場合、 **[These values are interchangeable]\(これらの値は交換可能です\)** をクリックします。

    ![フレーズ リストの提案値 - 代替値の選択ボックス](./media/luis-add-features/interchangeable.png)

1. **[Done]** をクリックします。 "Cities" フレーズ リストが **[Phrase lists]\(フレーズ リスト\)** ページに追加されます。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 語句一覧の削除や非アクティブ化は､**Phrase リスト**ページのコンテキスト ツールバーから行うことができます。

## <a name="next-steps"></a>次の手順

フレーズ リストを追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうか確認します。
