---
title: フィーチャー - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) を使用して、カテゴリやパターンがある意図およびエンティティの検出または予測を改善できるアプリ フィーチャーを追加します
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 12445ec5b14f4c274e471bf1b061a3b221664d20
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592306"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>特徴を使用して単語リストのシグナルをブーストする

LUIS アプリにフィーチャーを追加して、その精度を向上させることができます。 フィーチャーは、特定の単語やフレーズがアプリ領域のボキャブラリの一部であるというヒントを提供することで、LUIS を助けます。

特徴を使用するタイミングと理由を理解するために、[概念](luis-concept-feature.md)を確認してください。

## <a name="add-phrase-list-as-a-feature"></a>フレーズ リストを特徴として追加する

1. [LUIS ポータル](https://www.luis.ai)にサインインし、自分の**サブスクリプション**と**作成リソース**を選択して、その作成リソースに割り当てられているアプリを表示します。
1. **[マイ アプリ]** ページで自分のアプリの名前を選択して、そのアプリを開きます。
1. **[ビルド]** を選択し、アプリの左側のパネルで **[特徴]** を選択します。

1. **[特徴]** ページで **[+ 作成]** を選択します。

1. **[Create new phrase list feature]\(新しいフレーズ リストの特徴の作成\)** ダイアログ ボックスで、`Cities` などの名前を入力します。 **[値]** ボックスに、`Seattle` などの市区町村の例を入力します。 値は一度に 1 つずつ入力することも、コンマで区切った値のセットを入力することもできます。入力したら **Enter** キーを押します。

    > [!div class="mx-imgBorder"]
    > ![特徴 (フレーズ リスト) の市区町村を追加するスクリーンショット](./media/luis-add-features/add-phrase-list-cities.png)

    LUIS に十分な値を入力すると、提案が表示されます。 提案されたすべての値を **[+ すべて追加]** で追加するか、個々の用語を選択することができます。

1. フレーズを交換して使用できる場合は、 **[これらの値はどれを使っても問題ありません]** をオンのままにします。

1. 語句リストは **グローバル**設定を使用してアプリ全体に適用したり、特定のモデル (意図またはエンティティ) に適用したりできます。 フレーズ リストを作成する場合、意図またはエンティティの "_特徴_" として、トグルはグローバルに設定されません。 この場合、トグルの意味は、特徴がそのモデルに対してのみローカルであり、そのため、アプリケーションに対しては "_グローバルではない_" ということです。

1. **[Done]** を選択します。 新しい特徴は **[ML の特徴]** ページに追加されます。

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> 語句一覧の削除や非アクティブ化は､ **[ML の特徴]** ページのコンテキスト ツールバーから行うことができます。

## <a name="next-steps"></a>次のステップ

特徴を追加、編集、削除、または非アクティブ化した後、もう一度[アプリのトレーニングとテスト](luis-interactive-test.md)を行って、パフォーマンスが改善するかどうかを確認します。
