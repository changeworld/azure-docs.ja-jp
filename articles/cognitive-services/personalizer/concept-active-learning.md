---
title: アクティブなイベントと非アクティブなイベント- Personalizer
titleSuffix: Azure Cognitive Services
description: この記事では、アクティブなイベントと非アクティブなイベントの使用、学習の設定、および Personalizer サービス内での学習ポリシーについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 90658e030c907a9fd99dd8fb9a6e90698d72b1f0
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834474"
---
# <a name="active-and-inactive-events"></a>アクティブなイベントと非アクティブなイベント

アプリケーションから Rank API を呼び出すと、アプリケーションで **rewardActionId** フィールドに表示する必要のあるアクションを受け取ります。  その時点から、Personalizer では、eventId が同じ Reward の呼び出しが必要になります。 報酬スコアは、以降の Rank の呼び出しに対するモデルのトレーニングに使用されます。 eventId に対して Reward の呼び出しを受け取っていない場合は、既定の報酬が適用されます。 既定の報酬は、Azure portal で設定します。

一部のシナリオでは、結果が使用されるか、ユーザーに表示されるかを知るよりも前に、アプリケーションで Rank を呼び出す必要があります。 これは、たとえば、昇格したコンテンツのページ レンダリングがマーケティング キャンペーンで上書きされる場合に発生する可能性があります。 Rank の呼び出しの結果が使用されておらず、ユーザーがそれを見ていない場合は、対応する Reward の呼び出しを送信しないでください。

通常、これらのシナリオは次の場合に発生します。

* ユーザーに表示されるかどうかにかかわらず、UI の事前レンダリングを行っている。
* アプリケーションで、リアルタイム性の低いコンテキストを使用して Rank 呼び出しが行われる予測パーソナル化が行われていて、その出力がアプリケーションで使用される場合もあれば、使用されない場合もある。

このような場合は、Personalizer を使用して Rank を呼び出し、イベントを "_非アクティブ_" にするよう要求します。 Personalizer では、このイベントに対する報酬は必要とされず、既定の報酬も適用されません。
後のビジネス ロジックにおいて、アプリケーションで Rank 呼び出しの情報を使用する場合は、イベントを "_アクティブ化_" するだけです。 イベントがアクティブになると直ちに、Personalizer でイベントの報酬が必要になります。 Reward API の明示的な呼び出しが行われない場合、Personalizer では既定の報酬が適用されます。

## <a name="inactive-events"></a>非アクティブなイベント

イベントのトレーニングを無効にするには、`learningEnabled = False` を使用して Rank を呼び出します。 非アクティブなイベントの学習は、eventId に対して報酬が送信されたり、その eventId に対して `activate` API が呼び出されたりすると、暗黙的にアクティブになります。

## <a name="learning-settings"></a>学習の設定

学習の設定では、モデル トレーニングの "*ハイパーパラメーター*" が決定されます。 同じデータの 2 つのモデルが、異なる学習設定でトレーニングされると、最終的には別のものになります。

### <a name="import-and-export-learning-policies"></a>学習ポリシーのインポートとエクスポート

学習ポリシーのファイルは、Azure portal からインポートおよびエクスポートできます。 この方法を使用して、将来的な参照と監査のために、既存のポリシーをソース コード管理で成果物として保存、テスト、置換、アーカイブします。

学習ポリシーをインポートしたりエクスポートしたりする[方法](how-to-learning-policy.md)をご覧ください。

### <a name="understand-learning-policy-settings"></a>学習ポリシーの設定について

学習ポリシーの設定は、変更されることが想定されていません。 Personalizer に対する影響がわかっている場合にのみ、設定を変更してください。 これがわかっていないと、Personalizer モデルの無効化など、問題が発生する可能性があります。

Personalizer は、[vowpalwabbit](https://github.com/VowpalWabbit) を使用してイベントのトレーニングとスコア付けを行います。 vowpalwabbit を使用して学習設定を編集する方法については、[vowpalwabbit のドキュメント](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)を参照してください。 正しいコマンド ライン引数を指定したら、以下の形式 (arguments プロパティの値は、目的のコマンドに置き換えてください) でコマンドをファイルに保存します。Azure portal から Personalizer リソースの **[モデルと学習設定]** ペインで、そのファイルをアップロードし、学習設定をインポートしてください。

次の `.json` は、学習ポリシーの例です。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

### <a name="compare-learning-policies"></a>学習ポリシーを比較する

[オフライン評価](concepts-offline-evaluation.md)を実施することで、Personalizer ログの過去のデータに対してさまざまな学習ポリシーを適用した場合の差を比較できます。

現在の学習ポリシーと比較するには、[独自の学習ポリシーをアップロード](how-to-learning-policy.md)します。

### <a name="optimize-learning-policies"></a>学習ポリシーを最適化する

Personalizer では、[オフライン評価](how-to-offline-evaluation.md)に最適化された学習ポリシーを作成できます。 オフライン評価での報酬がよくなるように最適化された学習ポリシーほど、Personalizer でオンライン使用したときの結果がよくなります。

学習ポリシーを最適化した後は、Personalizer にそれを直接適用し、現在のポリシーをすぐに置き換えることができます。 または、最適化されたポリシーをさらに評価するために保存しておき、後でそのポリシーを破棄、保存、適用するかどうかを決定することもできます。
