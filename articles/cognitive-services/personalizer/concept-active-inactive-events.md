---
title: アクティブなイベントと非アクティブなイベント- Personalizer
description: この記事では、Personalizer サービス内でのアクティブおよび非アクティブなイベントの使用について説明します。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 948d375c0f580a71dbd27fa10660c0c7e0046a10
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219156"
---
# <a name="defer-event-activation"></a>イベントのアクティブ化を遅延させる

イベントの遅延アクティブ化を使用すると、ユーザーが実際にページを表示したり電子メールを開いたりしない可能性を考慮して、パーソナライズされた Web サイトやメーリング キャンペーンを作成できます。 これらのシナリオでは、結果が使用されたりユーザーに表示されたりするかどうかをアプリケーション側で知る前に、アプリケーションで Rank を呼び出す必要があります。 コンテンツがユーザーに表示されない場合は、学習のためにコンテンツに対して既定の報酬 (通常は 0) を想定する必要はありません。
遅延アクティブ化を使用すると、Rank 呼び出しの結果をある時点で使用し、イベントを後から、またはコード内の他の場所で、学習する必要があるかどうかを決定できます。

## <a name="typical-scenarios-for-deferred-activation"></a>遅延アクティブ化の一般的なシナリオ

イベントのアクティブ化の遅延は、次の例のようなシナリオで役に立ちます。

* ユーザー向けにパーソナライズされた Web ページを事前にレンダリングしていますが、Personalizer のアクションの選択がいくつかのビジネス ロジックによってオーバーライドされるため、それをユーザーが見ない可能性があります。
* Web ページの "下半分" にあるコンテンツをパーソナライズしていて、そのコンテンツがユーザーに見られない可能性が高いと考えられます。
* マーケティング電子メールをパーソナライズしていて、ユーザーに開かれたことがない電子メールによるトレーニングは避ける必要があります。
* 動的メディア チャネルをパーソナライズしましたが、Personalizer によって選択された曲や動画に到達する前に、ユーザーがチャネルの再生を停止する可能性があります。 

一般的に、これらのシナリオは以下の場合に発生します。

* UI または時間の制約により、ユーザーが見るか見ないかわからない UI を事前にレンダリングしている。
* 出力を使用するかどうかわかる前に Rank 呼び出しを行う予測パーソナライズを、アプリケーションが実行している。

## <a name="how-to-defer-activation-and-later-activate-events"></a>イベントのアクティブ化を遅延させ、後でアクティブ化する方法

イベントのアクティブ化を遅延させるには、要求本文で、`deferActivation = True` にして [Rank](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) を呼び出します。

パーソナライズされたコンテンツまたはメディアがユーザーに表示され、報酬が妥当であることがわかったらすぐに、そのイベントをアクティブ化する必要があります。 そうするには、eventId を指定して [Activate API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) を呼び出します。


その EventID 呼び出しの [Activate API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Activate) 呼び出しは、報酬の待機時間枠が終了する前に受信される必要があります。

### <a name="behavior-with-deferred-activation"></a>遅延アクティブ化での動作 

Personalizer は、次のようにイベントと報酬から学習します。
* `deferActivation = True` にして Rank を呼び出し、その eventId の `Activate` API を呼び出さ "*ない*" で、Reward を呼び出す場合、Personalizer はイベントから学習しません。
* `deferActivation = True` にして Rank を呼び出し、その eventId の `Activate` API を "*呼び出して*"、Reward を呼び出す場合、Personalizer は指定された報酬スコアを使用してイベントから学習します。
* `deferActivation = True` にして Rank を呼び出し、その eventId の `Activate` API を "*呼び出す*" ものの、Reward の呼び出しは省略する場合、Personalizer は構成内の既定の報酬スコア セットを使用してイベントから学習します。

## <a name="next-steps"></a>次のステップ
* [既定の報酬](how-to-settings.md#configure-rewards-for-the-feedback-loop)を構成する方法。
* [報酬スコアを決定する方法と考慮すべきデータ](concept-rewards.md)を確認します。
