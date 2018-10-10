---
title: エンティティの検出を強化するためのフレーズ リスト
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6831f67a33d114ca5c42ddacf8ef4de704e21711
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036958"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>フレーズ リストを使用して単語リストのシグナルをブーストする

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語やフレーズがアプリ領域のボキャブラリの一部であるというヒントを提供することで、LUIS を助けます。 

## <a name="add-phrase-list"></a>フレーズ リストの追加

1. **[My Apps]\(マイ アプリ\)** ページでアプリの名前をクリックしてアプリを開き、**[ビルド]** をクリックし、アプリの左パネルで **[Phrase lists]\(フレーズ リスト\)** をクリックします。 

2. **[Phrase lists]\(フレーズ リスト\)** ページで、**[Create new phrase list]\(新しいフレーズ リストの作成\)** をクリックします。 
 
3. **[Add Phrase List]\(フレーズ リストの追加\)** ダイアログ ボックスで、フレーズ リストの名前として「Cities」と入力します。 **[値]** ボックスにフレーズ リストの値を入力します。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    ![フレーズ リスト Cities の追加](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS は、フレーズ リストに追加する関連した値を提案することができます。 **[Recommend]\(推奨\)** をクリックすると、追加する値とセマンティック的に関連した提案値のグループが得られます。 提案値のどれかをクリックしてその値を追加するか、または **[すべて追加]** をクリックしてすべての提案値を追加することができます。

    ![フレーズ リストの提案値](./media/luis-add-features/related-values.png)

5. 追加されるフレーズ リストの値が、交換して使用できる代替値である場合、**[These values are interchangeable]\(これらの値は交換可能です\)** をクリックします。

    ![フレーズ リストの提案値](./media/luis-add-features/interchangeable.png)

6. **[Save]** をクリックします。 "Cities" フレーズ リストが **[Phrase lists]\(フレーズ リスト\)** ページに追加されます。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 各フレーズ リストの行の末尾にある省略記号 (***...***) ボタンから表示されるフレーズ リストは、編集、削除、または非アクティブ化することができます。

## <a name="pattern-regular-expression-feature"></a>パターン (正規表現) フィーチャー 
**このフィーチャーは非推奨です**。 新しいパターン フィーチャーを LUIS に追加することはできません。 既存のパターン フィーチャーは 2018 年 5 月までサポートされます。 PR に一致する標準 LUIS 正規表現を [Recognizers-Text Github リポジトリ](https://github.com/Microsoft/Recognizers-Text)にお寄せください。 

## <a name="next-steps"></a>次の手順

フレーズ リストを追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうか確認します。
