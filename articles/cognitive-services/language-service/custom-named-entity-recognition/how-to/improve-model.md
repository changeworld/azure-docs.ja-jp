---
title: カスタム固有表現認識 (NER) モデルのパフォーマンスを向上させる方法
titleSuffix: Azure Cognitive Services
description: カスタム固有表現認識 (NER) のモデルの改善について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 5ae65097fbd4e7bdc9f4e602a64bc392e45ef75e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091258"
---
# <a name="improve-the-performance-of-custom-named-entity-recognition-ner-models"></a>カスタム固有表現認識 (NER) モデルのパフォーマンスを向上させる 

モデルをトレーニングして、評価の詳細を確認したら、モデルのパフォーマンスの向上を始めることができます。 この記事では、予測されたクラスとモデルによってタグ付けされたクラスとの間の不整合を確認し、データの分布を調べます。

## <a name="prerequisites"></a>前提条件

* 構成済みの Azure Blob Storage アカウントで正常に[作成されたプロジェクト](create-project.md)
    * ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* [タグ付けされたデータ](tag-data.md)
* [正常にトレーニングされたモデル](train-model.md)
* [モデルの評価の詳細](view-model-evaluation.md)を調べて、モデルのパフォーマンスを確認した。
    * 評価に使われる[評価メトリック](../concepts/evaluation-metrics.md)を十分理解した。

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#application-development-lifecycle)に関する記事をご覧ください。


## <a name="improve-model"></a>モデルを改善する

[モデルの評価](view-model-evaluation.md)を確認したら、モデルの予測にどのような問題があるかがわかりますｊ。 

> [!NOTE]
> このガイドでは、トレーニングの間に作成された[検証セット](train-model.md#data-split) のデータに焦点を当てます。

### <a name="review-validation-set"></a>検証セットを確認する

Language Studio を使うと、予想されるパフォーマンスと比較してモデルのパフォーマンスを検討できます。 トレーニングしたモデルごとに、予測されたクラスとタグ付けされたクラスを確認できます。

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio の **[Classify text]\(テキストを分類する\)** というラベルが付いたセクションを探します。
    2. **[Custom text classification]\(カスタム テキスト分類\)** を選びます。 

2. 左側のメニューから **[Improve model]\(モデルの改善\)** を選びます。

3. **[Review validation set]\(検証セットの確認\)** を選びます。

4. **[モデル]** ドロップダウン メニューから、トレーニング済みのモデルを選びます。

5. 分析を容易にするには、 **[Show incorrect predictions only]\(正しくない予測のみを表示する\)** をオンにして、誤りのみを表示できます。

次の情報はモデルの改善に役立ちます。 

* エンティティ `X` が常にエンティティ `Y` として識別される場合は、これらのエンティティの種類の間にあいまいさがあることを意味し、スキーマを再検討する必要があります。

* 複雑なエンティティが繰り返し抽出されない場合は、簡単に抽出できるように、さらに単純なエンティティに分割することを検討します。

* データにタグが付けられていないのに、エンティティが予測される場合は、タグを確認する必要があることを意味します。 すべてのファイルで、エンティティのすべてのインスタンスに正しくタグが付けられていることを確認します。

### <a name="examine-data-distribution"></a>データの分布を調べる

ファイル内のデータの分布を調べることで、いずれかのエンティティの出現が少なすぎるかどうかを判断できます。 エンティティ間にタグが均等に分散されていないと、データの不均衡が発生し、モデルのパフォーマンスが低下する可能性があります。 たとえば、"*エンティティ 1*" には 50 個のタグがあるのに、"*エンティティ 2*" には 10 個のタグしかない場合、これはデータの不均衡の例であり、"*エンティティ 1*" の出現は多すぎ、"*エンティティ 2*" は少なすぎます。 モデルは、"*エンティティ 1*" の抽出に偏っており、"*エンティティ 2*" を見落とす可能性があります。 スキーマがあいまいな場合、データの不均衡によってさらに複雑な問題が発生する可能性があります。 2 つのエンティティが似ていて、"*エンティティ 2*" の出現が少なすぎる場合、モデルによってそれが "*エンティティ 1*" として抽出される可能性が高くなります。

[モデルの評価](view-model-evaluation.md)では、出現が多すぎるエンティティは他のエンティティよりリコールが高くなり、少なすぎるエンティティはリコールが低くなる傾向があります。

データセット内のデータの分布を調べるには:

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio で **[情報の抽出]** というラベルが付いたセクションを探します。
    2. **[Custom named entity extraction]\(カスタム名前付きエンティティ抽出\)** を選びます。 
2. 左側のメニューから **[Improve model]\(モデルの改善\)** を選びます。

3. **[Examine data distribution]\(データの分布を調べる\)** を選びます。

## <a name="next-steps"></a>次のステップ

モデルのパフォーマンスに問題がなくなったら、ランタイム API を使って[エンティティ抽出要求の送信](call-api.md)を始めることができます。
