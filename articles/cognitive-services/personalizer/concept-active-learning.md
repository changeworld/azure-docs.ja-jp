---
title: アクティブなイベントと非アクティブなイベント- Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429040"
---
# <a name="active-and-inactive-events"></a>アクティブなイベントと非アクティブなイベント

アプリケーションから Rank API が呼び出されると、アプリケーションで rewardActionId フィールドに表示される必要のあるアクションを受け取ります。  その時点から、Personalizer では、eventId が同じ報酬呼び出しが必要になります。 報酬スコアは、今後の Rank 呼び出しに使用されるモデルのトレーニングに使用されます。 eventId に対して報酬呼び出しを受け取っていない場合は、既定の報酬が適用されます。 既定の報酬は、Azure portal で設定されます。

場合によっては、結果が使用されるか、ユーザーに表示されるかを知るよりも前に、アプリケーションで Rank を呼び出す必要があります。 これは、たとえば、昇格したコンテンツのページ レンダリングがマーケティング キャンペーンで上書きされる場合に発生する可能性があります。 Rank 呼び出しの結果が使用されておらず、ユーザーがそれを見ることができなかった場合は、ゼロまたはそれ以外のどのような報酬でもトレーニングすることは正しくありません。
通常、これは次の場合に発生します。

* ユーザーに対して表示されるかどうかに関係なく、一部の UI を事前レンダリングしている可能性がある。 
* アプリケーションが、リアルタイム性の低いコンテキストを使用して Rank 呼び出しが行われる予測パーソナル化を実行していて、その出力がアプリケーションで使用される場合もあれば、使用されない場合もある。 

こうした場合、Personalizer を使用する正しい方法は、イベントが "_非アクティブ_" になるように要求する Rank を呼び出すことです。 Personalizer では、このイベントに対する報酬は必要とされず、既定の報酬も適用されません。 ビジネス ロジックで、アプリケーションで順位呼び出しの情報を使用する場合、やらなければいけないのは、イベントを "_アクティブ化_" することだけです。 イベントがアクティブになった瞬間から、Personalizer では、そのイベントに対する報酬が期待されます。または、報酬 API に対して明示的な呼び出しが行われない場合は、既定の報酬が適用されます。

## <a name="get-inactive-events"></a>非アクティブなイベントを取得する

イベントのトレーニングを無効にするには、`learningEnabled = False` で Rank を呼び出します。

非アクティブなイベントの学習は、eventId の報酬を送信、または、その eventId の `activate` API を呼び出した場合、暗黙的にアクティブになります。

## <a name="learning-settings"></a>学習の設定

学習設定では、モデル トレーニングの特定の "*ハイパーパラメーター*" を決定します。 データが同じ 2 つのモデルが異なる学習設定でトレーニングされると、最終的には別のものになります。

### <a name="import-and-export-learning-policies"></a>学習ポリシーのインポートとエクスポート

学習ポリシーのファイルは、Azure portal からインポートおよびエクスポートできます。 これにより、将来的な参照と監査のために、既存のポリシーをソース コード管理で成果物として保存、テスト、置換、アーカイブできるようになります。

### <a name="learning-policy-settings"></a>学習ポリシーの設定

**学習ポリシー**の設定は、変更が想定されていません。 Personalizer にどのような影響があるかを把握している場合にのみ、設定を変更してください。 この知識のない状態で設定を変更すると、Personalizer モデルの無効化などの副作用が発生します。

### <a name="comparing-effectiveness-of-learning-policies"></a>学習ポリシーの効果を比較する

[オフライン評価](concepts-offline-evaluation.md)を実施することで、Personalizer ログの過去のデータに対してさまざまな学習ポリシーを適用した場合の差を比較できます。

現在の学習ポリシーと比較するには、[独自の学習ポリシーをアップロード](how-to-offline-evaluation.md)します。

### <a name="discovery-of-optimized-learning-policies"></a>最適化された学習ポリシーの特定

Personalizer は、[オフライン評価](how-to-offline-evaluation.md)の実施時に、より最適化された学習ポリシーを作成できます。 より最適化された学習ポリシーとは、オフライン評価においてより良い報酬を獲得したもののことです。そのポリシーを Personalizer でオンラインで使用すると、より良い結果が得られます。

最適化された学習ポリシーが作成されたら、Personalizer に直接適用して現在のポリシーの代わりに使用できるほか、今後の評価のために現在のポリシーを保存しておいて、破棄、保存、適用の判断は将来行うこともできます。
