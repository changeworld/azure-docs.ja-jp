---
title: 初めての Language Understanding (LUIS) アプリを 10 分で作成する - Cognitive Services LUIS | Microsoft Docs
description: このクイック スタートでは、照明やアプライアンスの電源をオンにしたりオフにしたりする用途を想定し、事前構築済みのドメイン `HomeAutomation` を使用した LUIS アプリを作成します。 この事前構築済みのドメインによって、意図、エンティティ、発話例が得られます。 完成すると、クラウド内で LUIS エンドポイントが実行されるようになります。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2018
ms.locfileid: "43771647"
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

左側のナビゲーション ウィンドウで **[Intents]\(意図\)** を選択し、HomeAutomation ドメインの意図を確認します。 

[![](media/luis-quickstart-new-app/home-automation-intents.png "テーブル内で意図の名前が強調表示されている [Intents]\(意図\) リストのスクリーンショット")](media/luis-quickstart-new-app/home-automation-intents.png)

それぞれの意図には、サンプル発話が存在します。

> [!NOTE]
> **[None]\(なし\)** は、すべての LUIS アプリに用意されている意図です。 これは自分のアプリの機能に対応しない発話を処理する目的で使用されます。 

**[HomeAutomation.TurnOff]** 意図を選択します。 意図には、エンティティでラベル付けされている発話の一覧が含まれていることがわかります。

[![](media/luis-quickstart-new-app/home-automation-turnon.png "HomeAutomation.TurnOff 意図のスクリーンショット")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>アプリをトレーニングする

上部のナビゲーションの **[Train]\(トレーニング\)** を選択します。

[![](media/luis-quickstart-new-app/trained.png "HomeAutomation.TurnOff 意図と成功を示す緑色の通知のスクリーンショット")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>アプリをテストする
アプリのトレーニング後、そのテストを行うことができます。 上部のナビゲーションの **[Test]\(テスト\)** を選択します。 対話型のテスト ウィンドウにテスト発話 (「Turn off the lights」など) を入力し、Enter キーを押します。 

```
Turn off the lights
```

それぞれのテスト発話について、最もスコアの高い意図が、想定された意図と対応していることを確認します。

この例では、"HomeAutomation.TurnOff" に対する最もスコアの高い意図として "Turn off the lights" が正しく識別されています。

[![](media/luis-quickstart-new-app/test.png "発話が強調表示されたテスト パネルのスクリーンショット")](media/luis-quickstart-new-app/test.png)


もう一度 **[テスト]** を選択して、テスト ウィンドウを折りたたみます。 

## <a name="publish-your-app"></a>アプリケーションの発行
上部のナビゲーションの **[Publish]\(公開\)** を選択します。 

[![](media/luis-quickstart-new-app/publish.png "公開ボタンが強調表示されたアプリのスクリーンショット")](media/luis-quickstart-new-app/publish.png)

[Production]\(運用\) スロットを選択し、**[Publish]\(公開\)** ボタンを選択します。

上部に表示される緑色の通知バーは、アプリが正常に公開されたことを示します。

[![](media/luis-quickstart-new-app/published.png "公開に成功したときのアプリのスクリーンショット")](media/luis-quickstart-new-app/published.png)

正常に公開されたら、**[Publish app]\(アプリの公開\)** ページに表示されるエンドポイントの URL を使用することができます。

[![](media/luis-quickstart-new-app/endpoint.png "エンドポイント URL が強調表示されている公開ページのスクリーンショット")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>アプリを使用する
公開されたエンドポイントは、生成された URL を使って、ブラウザーでテストすることができます。 この URL をブラウザーで開き、URL パラメーター "&q" に、目的のテスト クエリを設定します。 たとえば、`turn off the living room light` を URL の最後に追加し、Enter キーを押します。 ブラウザーに HTTP エンドポイントの JSON 応答が表示されます。


[![](media/luis-quickstart-new-app/turn-off-living-room.png "意図 TurnOff が検出された JSON の結果を表示するブラウザーのスクリーンショット")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ
不要になったら、LUIS アプリを削除します。 それを行うには、アプリの一覧内のアプリ名の右にある省略記号 (***...***) ボタンを選択し、**[削除]** を選択します。 **[Delete app?]\(アプリを削除しますか?\)** ポップアップ ダイアログで、**[OK]** をクリックします。

## <a name="next-steps"></a>次の手順

エンドポイントはコードから呼び出すことができます。

> [!div class="nextstepaction"]
> [コードを使って LUIS エンドポイントを呼び出す](luis-get-started-cs-get-intent.md)
