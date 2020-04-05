---
title: 記述子 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "74123125"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>記述子を使用して単語リストのシグナルをブーストする

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語やフレーズがアプリ領域のボキャブラリの一部であるというヒントを提供することで、LUIS を助けます。 

[記述子](luis-concept-feature.md)には、同じように処理する必要がある、同じクラスに属している値 (単語またはフレーズ) のグループが含まれています (都市名、製品名など)。 LUIS がそのうちの 1 つについて学習した内容が、他のすべてに自動的に適用されます。 このリストは、一致した単語の[リスト エンティティ](reference-entity-list.md) (テキストの完全一致) と同じものではありません。

記述子は、それらの単語に関する LUIS に対する 2 番目のシグナルとして、アプリ ドメインのボキャブラリを増強します。

[機能の概念](luis-concept-feature.md)を確認して、どのような場合に、どのような理由で記述子を使用するかを理解します。 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>記述子の追加

1. **[マイ アプリ]** ページでアプリの名前をクリックしてアプリを開き、 **[ビルド]** をクリックし、アプリの左パネルで **[記述子]** をクリックします。 

1. **[記述子]** ページで、 **[+ Add Descriptor]\(+ 記述子の追加\)** をクリックします。 
 
1. **[Create new phrase list descriptor]\(新しいフレーズ リスト記述子の作成\)** ダイアログ ボックスで、記述子の名前を入力します (`Cities` など)。 **[値]** ボックスに記述子の値を入力します (`Seattle` など)。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    > [!div class="mx-imgBorder"]
    > ![記述子 Cities の追加](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS に十分な値を入力すると、提案が表示されます。 提案されたすべての値を **[+ すべて追加]** で追加するか、個々の用語を選択することができます。

1. 追加される記述子の値が、入れ替えて使用できる代替値である場合、 **[These values are interchangeable]\(これらの値は交換可能です\)** をオンのままにします。

1. **[完了]** を選択します。 新しい記述子が **[記述子]** ページに追加されます。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 記述子の削除や非アクティブ化は、 **[記述子]** ページのコンテキスト ツール バーから行うことができます。

## <a name="next-steps"></a>次のステップ

記述子を追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうかを確認します。
