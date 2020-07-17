---
title: 信頼できる機械学習 (ML)
titleSuffix: Azure Machine Learning
description: 信頼できる ML とはどのようなものか、および Azure Machine Learning でそれを使用する方法について説明します
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: luquinta
author: luisquintanilla
ms.date: 05/08/2020
ms.openlocfilehash: 3cef3c2179019f6d84de5596e61abaf8d7d3182c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595011"
---
# <a name="responsible-machine-learning-ml"></a>信頼できる機械学習 (ML)

この記事では、信頼できる ML とはどのようなもので、Azure Machine Learning でそれを実践するにはどうすればよいかについて説明します。

AI システムの開発と使用を通して、信頼をその中心にする必要があります。 プラットフォーム、プロセス、モデルでの信頼。 Microsoft では、信頼できる ML には次のような価値と原則が含まれます。

- 機械学習モデルを理解する
  - モデルの動作を解釈して説明する
  - モデルの不公平性を評価して軽減する
- ユーザーとそのデータを保護する
  - 差分プライバシーによりデータの漏えいを防ぐ  
- エンドツーエンドの機械学習プロセスを制御する
  - データシートで機械学習のライフサイクルを文書化する

:::image type="content" source="media/concept-responsible-ml/responsible-ml-pillars.png" alt-text="信頼できる ML の柱":::

社会構造により多くの人工知能や自律システムが統合されるようになったら、これらのテクノロジの望ましくない結果を事前に予測し、軽減することが重要です。

## <a name="interpret-and-explain-model-behavior"></a>モデルの動作を解釈して説明する

説明するのが難しいシステムやブラックボックス システムは、システムで特定の決定が行われた理由を、システム開発者、規制者、ユーザー、ビジネス意思決定者などの利害関係者が理解するのが困難であるため、問題になる可能性があります。 説明のしやすさは AI システムによって差があり、場合によっては、正確性の高いシステムと説明しやすいシステムの間でトレードオフが発生します。

解釈可能な AI システムを構築するには、Microsoft によって構築されたオープンソース パッケージである [InterpretML](https://github.com/interpretml/interpret) を使用します。 [InterpretML を Azure Machine Learning の内部で使用](how-to-machine-learning-interpretability.md)して、[自動化された機械学習モデル](how-to-machine-learning-interpretability-automl.md)など、[機械学習モデルを解釈して説明する](how-to-machine-learning-interpretability-aml.md)ことができます。

## <a name="assess-and-mitigate-model-unfairness"></a>モデルの不公平性を評価して軽減する

社会の日々の意思決定への AI システムの関与がますます大きくなると、これらのシステムがすべてのユーザーに公正な結果を提供するよう適切に機能することが非常に重要です。

不公平な AI システムでは、次のような意図しない結果になる可能性があります。

- 個人から機会、リソース、情報を取り上げる。
- バイアスやステレオタイプを強める。

公平性の多くの側面は、メトリックでは把握または表現できません。 AI システムの設計と開発における公平性を向上させることができるツールとプラクティスがあります。

AI システムにおける不公平性を減らすための 2 つの重要なステップは、評価と軽減です。 AI システムの潜在的な不公平性を評価および軽減できるオープンソース パッケージである [FairLearn](https://github.com/fairlearn/fairlearn) をすることをお勧めします。 公平性と FairLearn パッケージの詳細については、[ML での公平性に関する記事](./concept-fairness-ml.md)を参照してください。

## <a name="prevent-data-exposure-with-differential-privacy"></a>差分プライバシーによりデータの漏えいを防ぐ

データを分析に使用する場合は、使用期間全体を通して、データのプライベート性と機密性が維持されることが重要です。 差分プライバシーとは、個人のデータを安全かつプライベートに保つうえで役立つ、一連のシステムとプラクティスのことです。

従来のシナリオでは、生データはファイルとデータベースに保存されます。 データを分析する場合、ユーザーは通常、生データを使用します。 しかし、この方法では個人のプライバシーを侵害する可能性があるため、問題があります。 差分プライバシーは、データに "ノイズ" やランダム性を追加し、ユーザーが個々のデータ ポイントを識別できないようにすることで、この問題に対処しようとするものです。

差分プライバシー システムを実装することは簡単ではありません。 [WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-core) は、グローバルな差分プライバシー システムを構築するためのさまざまなコンポーネントを含んだ、オープンソース プロジェクトです。 差分プライバシーと WhiteNoise プロジェクトの詳細については、[差分プライバシーと WhiteNoise を使用し たデータ プライバシーの保持](./concept-differential-privacy.md)に関する記事を参照してください。

## <a name="document-the-machine-learning-lifecycle-with-datasheets"></a>データシートで機械学習のライフサイクルを文書化する

機械学習プロセスで適切な情報を文書化することは、各段階で責任ある決定を行ううえで重要なことです。 データシートは、機械学習のライフサイクルの一部として使用および作成される機械学習資産を文書化する手段です。

モデルは、"ブラックボックス" と見なされる傾向があり、多くの場合、それらに関する情報はほとんどありません。 機械学習システムはますます普及し、意思決定に使用されるようになるため、データシートを使用することは、より多くの信頼できる機械学習システムを開発するためのステップです。

データシートの一部としてドキュメント化するモデル情報には次のようなものがあります。

- 使用目的
- モデルのアーキテクチャ
- 使用されるトレーニング データ
- 使用される評価データ
- トレーニング モデルのパフォーマンス メトリック
- 公平性の情報。

Azure Machine Learning SDK を使用して[モデル用のデータシート](https://github.com/microsoft/MLOps/blob/master/pytorch_with_datasheet/model_with_datasheet.ipynb)を実装する方法についてのサンプルを参照してください。

## <a name="additional-resources"></a>その他のリソース

- 詳細については、機械学習システムのドキュメントに関するガイドラインの [ABOUT ML](https://www.partnershiponai.org/about-ml/) のセットを参照してください。