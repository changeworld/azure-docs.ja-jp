---
title: 10 分で作る最初の LUIS アプリ
titleSuffix: Azure Cognitive Services
description: 照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 12a660b49d1a81865c34ceda38f041de9be31eb1
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037475"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>クイック スタート - 事前構築済みのホーム オートメーション アプリを使用する

このクイック スタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。

## <a name="prerequisites"></a>前提条件

この記事には、LUIS ポータル ([http://www.luis.ai](http://www.luis.ai)) で作成された無料の LUIS アカウントが必要です。 

## <a name="create-a-new-app"></a>新しいアプリの作成
アプリケーションは、**[マイ アプリ]** で作成および管理できます。 

1. LUIS ポータルにサインインします。

2. **[Create new app]\(新しいアプリの作成\)** を選択します。

    [![](media/luis-quickstart-new-app/app-list.png "アプリ リストのスクリーンショット")](media/luis-quickstart-new-app/app-list.png)

3. ダイアログ ボックスで、アプリケーションに "Home Automation" という名前を付けます。

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "[新しいアプリの作成] ポップアップ ダイアログのスクリーンショット")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. アプリケーションのカルチャを選択します。 この Home Automation アプリでは、英語を選択します。 **[完了]** を選択します。 LUIS により Home Automation アプリが作成されます。 

    >[!NOTE]
    >カルチャは、アプリケーションを作成した後に変更できません。 

## <a name="add-prebuilt-domain"></a>事前構築済みのドメインの追加

左側のナビゲーション ウィンドウで **[Prebuilt domains]\(事前構築済みドメイン\)** を選択します。 次に、"Home" を検索します。 **[Add domain]\(ドメインの追加\)** を選択します。

[![](media/luis-quickstart-new-app/home-automation.png "事前構築済みドメイン メニューに表示された Home Automation ドメインのスクリーンショット")](media/luis-quickstart-new-app/home-automation.png)

ドメインが正常に追加されると、事前構築済みドメインのボックスに、**[Remove domain]\(ドメインの削除\)** ボタンが表示されます。

[![](media/luis-quickstart-new-app/remove-domain.png "削除ボタンを含む Home Automation ドメインのスクリーンショット")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>意図とエンティティ

左側のナビゲーション ウィンドウで **[Intents]\(意図\)** を選択し、HomeAutomation ドメインの意図を確認します。 それぞれの意図には、サンプル発話が存在します。

> [!NOTE]
> **[None]\(なし\)** は、すべての LUIS アプリに用意されている意図です。 これは自分のアプリの機能に対応しない発話を処理する目的で使用されます。 

**[HomeAutomation.TurnOff]** 意図を選択します。 意図には、エンティティでラベル付けされている発話の一覧が含まれていることがわかります。

[![](media/luis-quickstart-new-app/home-automation-turnon.png "HomeAutomation.TurnOff 意図のスクリーンショット")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>LUIS アプリをトレーニングする

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>アプリをテストする
アプリのトレーニング後、そのテストを行うことができます。 上部のナビゲーションの **[Test]\(テスト\)** を選択します。 対話型のテスト ウィンドウにテスト発話 (「Turn off the lights」など) を入力し、Enter キーを押します。 

```
Turn off the lights
```

それぞれのテスト発話について、最もスコアの高い意図が、想定された意図と対応していることを確認します。

この例では、"HomeAutomation.TurnOff" に対する最もスコアの高い意図として "Turn off the lights" が正しく識別されています。

[![](media/luis-quickstart-new-app/test.png "発話が強調表示されたテスト パネルのスクリーンショット")](media/luis-quickstart-new-app/test.png)


もう一度 **[テスト]** を選択して、テスト ウィンドウを折りたたみます。 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>アプリを公開してエンドポイント URL を取得する

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>異なる発話でエンドポイントにクエリを実行する

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. アドレスの URL の末尾に移動し、「`turn off the living room light`」と入力して Enter キーを押します。 ブラウザーに HTTP エンドポイントの JSON 応答が表示されます。

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "意図 TurnOff が検出された JSON の結果を表示するブラウザーのスクリーンショット")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>次の手順

エンドポイントはコードから呼び出すことができます。

> [!div class="nextstepaction"]
> [コードを使って LUIS エンドポイントを呼び出す](luis-get-started-cs-get-intent.md)
