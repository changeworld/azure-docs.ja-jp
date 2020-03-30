---
title: 新しいアプリの作成 - LUIS
titleSuffix: Azure Cognitive Services
description: Language Understanding (LUIS) Web ページで、アプリケーションを作成し、管理します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219109"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS ポータルでの新しい LUIS アプリの作成
LUIS アプリを作成するにはいくつかの方法があります。 LUIS ポータル内または LUIS オーサリング [API](developer-reference-resource.md) を使用して LUIS アプリを作成できます。

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>LUIS ポータルを使用する

プレビュー ポータルでは、新しいアプリをいくつかの方法で作成できます。

* 空のアプリから始めて、意図、発話、およびエンティティを作成する。
* 空のアプリから始めて、[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)を追加する。
* 意図、発話、およびエンティティが既に格納されている `.lu` または `.json` ファイルから LUIS アプリをインポートする。

## <a name="using-the-authoring-apis"></a>オーサリング API を使用する
オーサリング API を使用していくつかの方法で新しいアプリを作成できます。

* [アプリケーションの追加](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) - 空のアプリから始めて、意図、発話、エンティティを作成する。
* [構築済みアプリケーションの追加](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) - 意図、発話、エンティティなど、構築済みのドメインから始める。  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>LUIS で新しいアプリを作成する

1. **[マイ アプリ]** ページで、自分のサブスクリプション、作成リソース、 **[+ 作成]** の順に選択します。 無料試用版キーを使用している場合、[作成リソースの作成方法](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)について学習してください。

    ![LUIS アプリの一覧](./media/create-app-in-portal.png)


1. ダイアログ ボックスに、`Pizza Tutorial` など、アプリケーションの名前を入力します。

    ![[新しいアプリの作成] ダイアログ ボックス](./media/create-pizza-tutorial-app-in-portal.png)

1. アプリケーション カルチャを選択し、 **[完了]** を選択します。 説明と予測リソースは現時点では任意です。 ポータルの **[管理]** セクションでいつでも設定できます。

    > [!NOTE]
    > カルチャは、アプリケーションを作成した後に変更できません。 

    アプリが作成されると、LUIS ポータルに **[意図]** 一覧が表示されます。この一任には、自動的に作成された `None` 意図が含まれています。 これで空のアプリが作成されました。 
    
    > [!div class="mx-imgBorder"]
    > ![None 意図が作成されており、サンプル発話のない意図リスト](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>利用できるアクションはありません

コンテキスト ツール バーに他のアクションがあります。

* アプリの名前の変更
* `.lu` または `.json` を利用してファイルからインポートする
* `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown) の場合)、`.json`、`.zip` ([LUIS コンテナー](luis-container-howto.md)の場合) としてアプリをエクスポートする
* コンテナー エンドポイント ログをインポートし、エンドポイント発話をレビューする
* オフライン分析のためにエンドポイントログを `.csv` としてエクスポートする
* アプリの削除

## <a name="next-steps"></a>次のステップ

アプリ デザインに意図検出が含まれている場合、[新しい意図を作成](luis-how-to-add-intents.md)し、サンプル発話を追加します。 アプリ デザインがデータ抽出のみの場合、None 意図にサンプル発話を追加し、[エンティティを作成](luis-how-to-add-example-utterances.md)し、そのエンティティでサンプル発話にラベルを付けます。 
