---
title: カスタム分類モデルをトレーニングする方法 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: カスタムテキスト分類のモデルをトレーニングする方法について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 466ddd800c70c27787a0a8ebcd8c14a4392e6c2f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061941"
---
# <a name="how-to-train-a-text-classification-model"></a>テキスト分類モデルをトレーニングする方法


トレーニングは、モデルが[タグ付けされたデータ](tag-data.md)から学習するプロセスです。 トレーニングが済んだら、[モデル評価メトリックを使って](../how-to/view-model-evaluation.md)、[モデルを改善する](../how-to/improve-model.md)必要があるかどうかを判断できます。

## <a name="prerequisites"></a>前提条件

モデルをトレーニングする前に、次のものが必要です。

* 構成済みの Azure Blob Storage アカウントで[正常に作成されたプロジェクト](create-project.md) 
* ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ
* [タグ付けされたデータ](tag-data.md)

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#project-development-lifecycle)に関する記事をご覧ください。

## <a name="data-splits"></a>データの分割

トレーニング プロセスを始める前に、データセット内のファイルは 3 つのグループにランダムに分割されます。

* **トレーニング セット** には、データセット内のファイルの 80% が含まれます。 これは、モデルのトレーニングに使われるメイン セットです。

* **テスト セット** には、データセットで利用できるファイルの 20% が含まれます。 このセットは、モデルの偏りのない[評価](../how-to/view-model-evaluation.md)を提供するために使われます。 このセットは、トレーニング中にはモデルに取り込まれません。 このセットの正しい予測と間違った予測の詳細は表示されません。トレーニングデータを再調整して結果が変更されることがないようにするためです。

## <a name="train-model-in-language-studio"></a>Language Studio でモデルをトレーニングする

1. [Language Studio](https://aka.ms/LanguageStudio) でプロジェクトのページに移動します。

2. 左側のメニューから **[トレーニング]** を選びます。

3. 新しいモデルをトレーニングするには、 **[Train a new model]\(新しいモデルをトレーニングする\)** を選び、下のテキスト ボックスにモデル名を入力します。 このオプションを選び、下のドロップダウンから対象のモデルを選ぶと、**既存のモデルを上書きする** ことができます。

    :::image type="content" source="../media/train-model.png" alt-text="新しいモデルを作成する" lightbox="../media/train-model.png":::

4. ページの下部にある **[トレーニング]** ボタンを選びます。

モデルのトレーニングに要する時間はデータセットによって異なり、最大で数時間かかる場合があります。 一度にトレーニングできるモデルは 1 つのみであり、トレーニングされているモデルが既にあるプロジェクトで、他のモデルを作成またはトレーニングすることはできません。 


トレーニングが正常に完了したら、次の点に注意してください。

* [モデルの評価の詳細を表示する](../how-to/view-model-evaluation.md) モデルのトレーニング後、モデルの評価は[テストセット](../how-to/train-model.md#data-splits)に対して行われます。このテストセットは、トレーニング時にモデルには取り込まれませんでした。 評価を表示することによって、実際のシナリオでモデルがどのように実行されるかを把握できます。

* [データ分布を確認する](../how-to/improve-model.md#examine-data-distribution-from-language-studio) すべてのクラスが適切に表現されていること、およびすべてのクラスが適切に表現されていることを確認して、データ分散のバランスが取れていることを確認してください。 特定のクラスがタグ付けされる頻度が他のクラスよりもはるかに低い場合、このクラスの出現率は低くなり、ほとんどの場合、実行時にモデルによって正しく認識されません。 この場合は、このクラスに属するファイルをデータセットに追加することを検討してください。

* [パフォーマンスを向上する (省略可能)](../how-to/improve-model.md) エラー分析に基づいて[タグ付けされたデータ](tag-data.md)を改訂するほか、パフォーマンスが平均を下回るエンティティ型のタグ数を増やしたり、タグ付けされたデータの多様性を向上させたりすることができます。 これは、障害の原因となる可能性のある言語上の問題について、モデルで正しい予測を提供するのに役立ちます。

<!-- * Define your own test set: If you are using a random split option and the resulting test set was not comprehensive enough, consider defining your own test to include a variety of data layouts and balanced tagged classes.
 -->


## <a name="next-steps"></a>次のステップ

トレーニングが済んだら、[モデル評価メトリックを使い](../how-to/view-model-evaluation.md)、必要に応じて[モデルを改善する](../how-to/improve-model.md)ことができます。 モデルに問題がなければ、それをデプロイし、[テキストを分類する](call-api.md)ために使うことができます。
