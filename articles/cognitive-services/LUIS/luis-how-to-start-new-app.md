---
title: 新しいアプリの作成
titleSuffix: Language Understanding - Azure Cognitive Services
description: Language Understanding (LUIS) Web ページで、アプリケーションを作成し、管理します。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: ddfee80c67c22c7c6016ed87dc17925c91637d21
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/21/2018
ms.locfileid: "53714000"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>LUIS ポータルでの新しい LUIS アプリの作成
LUIS アプリを作成するにはいくつかの方法があります。 [LUIS](https://www.luis.ai) ポータル内または LUIS オーサリング[API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) を使用して LUIS アプリを作成できます。

## <a name="using-the-luis-portal"></a>LUIS ポータルを使用する
LUIS ポータルでは、新しいアプリをいくつかの方法で作成できます。

* 空のアプリから始めて、意図、発話、およびエンティティを作成する。
* 空のアプリから始めて、[事前構築済みのドメイン](luis-how-to-use-prebuilt-domains.md)を追加する。
* 意図、発話、およびエンティティが既に格納されている JSON ファイルから LUIS アプリをインポートする。

## <a name="using-the-authoring-apis"></a>オーサリング API を使用する
オーサリング API を使用していくつかの方法で新しいアプリを作成できます。

* 空のアプリから[始め](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)て、意図、発話、およびエンティティを作成する
* 事前構築済みのドメインから[始め](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5)る  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>LUIS で新しいアプリを作成する

1. **[マイ アプリ]** ページで、**[新しいアプリの作成]** を選択します。

    ![LUIS アプリの一覧](./media/luis-create-new-app/apps-list.png)


2. ダイアログ ボックスで、アプリケーションに "TravelAgent" という名前を付けます。

    ![[新しいアプリの作成] ダイアログ ボックス](./media/luis-create-new-app/create-app.png)

3. アプリケーションのカルチャを選択 (TravelAgent アプリの場合は、英語を選択) し、**[完了]** を選択します。 

    > [!NOTE]
    > カルチャは、アプリケーションを作成した後に変更できません。 


## <a name="next-steps"></a>次の手順

アプリでの最初のタスクは、[意図の追加](luis-how-to-add-intents.md)です。