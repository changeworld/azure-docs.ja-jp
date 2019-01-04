---
title: アプリの発行
titleSuffix: Azure Cognitive Services
description: アクティブな LUIS アプリの構築とテストが終了したら、それをエンドポイントに発行して、クライアント アプリケーションが使用できるようにします。
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 73788b05cad3abac19b1ef509f1250542d10dba2
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53136649"
---
# <a name="publish-your-trained-app"></a>トレーニング済みのアプリを発行する

アクティブな LUIS アプリの構築とテストが終了したら、それをエンドポイントに発行して、クライアント アプリケーションが使用できるようにします。 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>発行

エンドポイントを発行するには、右パネル上部の **[Publish]\(発行)** を選択します。 

![右上のナビゲーション バー](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

ポップアップ ウィンドウが表示されたら、適切なスロットを選択します。ステージングまたは運用です。 2 つの発行スロットを使うことにより、2 つの異なるエンドポイントで異なるバージョンまたは同じバージョンを発行できます。 

アプリは、LUIS ポータルで追加された LUIS リソースと関連付けられているすべてのリージョンに発行されます。 たとえば、[www.luis.ai](https://www.luis.ai) で作成されたアプリの場合、**westus** で LUIS リソースを作成してそれをリソースとしてアプリに追加すると、アプリはそのリージョンで発行されます。 LUIS のリージョンの詳細については、[リージョン](luis-reference-regions.md)に関するページを参照してください。
 
![発行のポップアップ ウィンドウ](./media/luis-how-to-publish-app/publish-pop-up.png)

アプリが正常に発行されると、ブラウザーの上部に緑色の成功通知が表示されます。 緑色の通知バーには、エンドポイントへのリンクも含まれています。 

![発行のポップアップ ウィンドウとエンドポイントへのリンク](./media/luis-how-to-publish-app/publish-success.png)

エンドポイント URL が必要な場合は、リンクを選択します。 エンドポイント URL には、上部のメニューの **[管理]** を選択し、左側のメニューの **[Keys and endpoints]\(キーとエンドポイント)** を選択してもアクセスできます。 

## <a name="configuring-publish-settings"></a>発行の設定を構成する

右上のナビゲーションで **[管理]** を選択してから **[Publish Settings]\(発行の設定)** を選択し、発行の設定を構成します。 

![Publish settings](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>感情分析の有効化後に発行する

<a name="enable-sentiment-analysis"></a>

感情分析を使用すると、LUIS を [Text Analytics](https://azure.microsoft.com/services/cognitive-services/text-analytics/) と統合して、感情分析とキーフレーズ分析を提供できます。 

Text Analytics キーを指定する必要はなく、Azure アカウントに対するこのサービスの課金はありません。 この設定を確認した後、設定は保持されます。 

センチメント データは 1 と 0 の間のスコアで、1 に近いほどポジティブな感情を示し、0 に近いほどネガティブな感情を示します。

感情分析での JSON エンドポイントの応答の詳細については、「[Sentiment analysis](luis-concept-data-extraction.md#sentiment-analysis)」(感情分析) をご覧ください。



## <a name="next-steps"></a>次の手順

* LUIS に対する Azure サブスクリプションにキーを追加する方法と、Bing Spell Check キーを設定し、結果にすべての意図を含めるする方法については、[キーの管理](./luis-how-to-manage-keys.md)に関するページを参照してください。
* テスト コンソールでの発行済みアプリのテスト方法については、[アプリのトレーニングとテスト](luis-interactive-test.md)に関するページをご覧ください。

