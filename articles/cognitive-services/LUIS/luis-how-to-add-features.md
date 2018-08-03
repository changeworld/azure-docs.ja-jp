---
title: LUIS アプリケーションにフィーチャーを追加する | Microsoft Docs
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222955"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>LUIS アプリのパフォーマンスを向上させるフィーチャーを使用する  

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語またはフレーズがカテゴリの一部であるというヒントを提供することで LUIS を支援します。 カテゴリの 1 つのメンバーを認識する方法を LUIS が学習すれば、LUIS は他のメンバーを同様に扱うことができます。

## <a name="add-phrase-list"></a>フレーズ リストの追加

1. **[My Apps]\(マイ アプリ\)** ページでアプリの名前をクリックしてアプリを開き、**[ビルド]** をクリックし、アプリの左パネルで **[Phrase lists]\(フレーズ リスト\)** をクリックします。 

    ![フレーズ リストのナビゲーション](./media/luis-add-features/phrase-list-nav.png)

2. **[Phrase lists]\(フレーズ リスト\)** ページで、**[Create new phrase list]\(新しいフレーズ リストの作成\)** をクリックします。 
 
    ![新しいフレーズ リストの作成](./media/luis-add-features/create-new-phrase-list.png)
    
3. **[Add Phrase List]\(フレーズ リストの追加\)** ダイアログ ボックスで、フレーズ リストの名前として「Cities」と入力します。 **[値]** ボックスにフレーズ リストの値を入力します。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    ![フレーズ リスト Cities の追加](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS は、フレーズ リストに追加する関連した値を提案することができます。 **[Recommend]\(推奨\)** をクリックすると、追加する値とセマンティック的に関連した提案値のグループが得られます。 提案値のどれかをクリックしてその値を追加するか、または **[すべて追加]** をクリックしてすべての提案値を追加することができます。

    ![フレーズ リストの提案値](./media/luis-add-features/related-values.png)

5. 追加されるフレーズ リストの値が、交換して使用できる代替値である場合、**[These values are interchangeable]\(これらの値は交換可能です\)** をクリックします。

    ![フレーズ リストの提案値](./media/luis-add-features/interchangeable.png)

6. **[Save]** をクリックします。 "Cities" フレーズ リストが **[Phrase lists]\(フレーズ リスト\)** ページに追加されます。

    ![追加されたフレーズ リスト](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>フレーズ リストの編集

**[Phrase lists]\(フレーズ リスト\)** ページで、フレーズ リストの名前をクリックします。 表示された **[Edit Phrase List]\(フレーズ リストの編集\)** ダイアログ ボックスで、必要な編集と変更を行って **[保存]** をクリックします。

 ![追加されたフレーズ リスト](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>フレーズ リストの削除 

行の末尾にある省略記号 (***...***) ボタンをクリックして、**[削除]** を選択します。

 ![追加されたリストの削除](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>フレーズ リストの非アクティブ化 

行の末尾にある省略記号 (***...***) ボタンをクリックして、**[Deactivate]\(非アクティブ化\)** を選択します。

 ![追加されたリストの非アクティブ化](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>パターン (正規表現) フィーチャー 
**このフィーチャーは非推奨です**。 新しいパターン フィーチャーを LUIS に追加することはできません。 既存のパターン フィーチャーは 2018 年 5 月までサポートされます。 PR に一致する標準 LUIS 正規表現を [Recognizers-Text Github リポジトリ](https://github.com/Microsoft/Recognizers-Text)にお寄せください。 

## <a name="next-steps"></a>次の手順

フレーズ リストを追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうか確認します。
