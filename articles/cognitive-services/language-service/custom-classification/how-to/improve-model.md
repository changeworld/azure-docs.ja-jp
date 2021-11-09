---
title: カスタム テキスト分類モデルのパフォーマンスを向上させる方法
titleSuffix: Azure Cognitive Services
description: カスタム テキスト分類のモデルの改善について説明します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 73513e90f37a3d003351604d119612306ba66277
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062740"
---
# <a name="improve-model-performance"></a>モデルのパフォーマンスを向上させる

モデルをトレーニングして評価を詳しく確認した後は、モデルのパフォーマンスを向上させる必要があるかどうかを判断できます。 この記事では、予測されたクラスとモデルによってタグ付けされたクラスとの間の不整合を確認し、データの分布を調べます。

## <a name="prerequisites"></a>前提条件

必要に応じてモデルを改善するには、次のものが必要です。

* Azure Blob Storage アカウントが構成されている[カスタム分類プロジェクト](create-project.md)。 
* ストレージ アカウントに[アップロードされた](create-project.md#prepare-training-data)テキスト データ。
* 適切に[モデルをトレーニングする](train-model.md)ための[タグ付けされたデータ](tag-data.md)
* [モデルの評価の詳細](view-model-evaluation.md)を調べて、モデルのパフォーマンスを確認した。
* 評価に使われる[評価メトリック](../concepts/evaluation.md)を十分理解した

詳しくは、[アプリケーション開発ライフサイクル](../overview.md#project-development-lifecycle)に関する記事をご覧ください。

## <a name="review-test-set-predictions"></a>テスト セットの予測を確認する

Language Studio を使うと、予想されるパフォーマンスと比較してモデルのパフォーマンスを検討できます。 トレーニングしたモデルごとに、予測されたクラスとタグ付けされたクラスを並べて確認できます。

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio の **[Classify text]\(テキストを分類する\)** というラベルが付いたセクションを探します。
    2. **[Custom text classification]\(カスタム テキスト分類\)** を選びます。 

2. 左側のメニューから **[Improve model]\(モデルの改善\)** を選びます。

3. **[Review test set]\(テスト セットのレビュー\)** を選びます。

4. **[モデル]** ドロップダウン メニューから、トレーニング済みのモデルを選びます。

5. 分析を容易にするには、 **[Show incorrect predictions only]\(正しくない予測のみを表示する\)** をオンにして、誤りのみを表示できます。

    :::image type="content" source="../media/review-validation-set.png" alt-text="検証セットを確認する" lightbox="../media/review-validation-set.png":::

6. クラス `X` に属する必要があるファイルが常にクラス `Y` として分類される場合は、これらのクラスの間にあいまいさがあり、スキーマの再検討が必要であることを意味します。

## <a name="examine-data-distribution-from-language-studio"></a>Language Studio でデータの分布を調べる

ファイル内のデータの分布を調べることで、いずれかのクラスの出現が少なすぎるかどうかを判断できます。 データの不均衡は、トレーニングに使用されたファイルがクラス間に均等に分布していないときに発生し、モデルのパフォーマンスに対するリスクの原因になります。 たとえば、"*クラス 1*" には 50 個のタグ付きファイルがあるのに、"*クラス 2*" にタグ付きファイルが 10 個しかない場合、データに不均衡があり、"*クラス 1*" の出現は多すぎ、"*クラス 2*" は少なすぎます。 

この場合のモデルは、ファイルを "*クラス 1*" として分類することが多く、"*クラス 2*" を見落とす可能性があります。 スキーマがあいまいな場合、データの不均衡によってさらに複雑な問題が発生する可能性があります。 2 つのクラスの間に明確な区別がなく、"*クラス 2*" の出現が少なすぎる場合、モデルによってほとんどのテキストが "*クラス 1*" として分類されます。

[評価メトリック](../concepts/evaluation.md)では、出現が多すぎるクラスは他のクラスよりリコールが高くなり、出現が少なすぎるクラスはリコールが低くなる傾向があります。

データセット内のデータの分布を調べるには:

1. [Language Studio](https://aka.ms/languageStudio) でプロジェクトのページに移動します。
    1. Language Studio の **[Classify text]\(テキストを分類する\)** というラベルが付いたセクションを探します。
    2. **[Custom text classification]\(カスタム テキスト分類\)** を選びます。 

2. 左側のメニューから **[Improve model]\(モデルの改善\)** を選びます。

3. **[Examine data distribution]\(データの分布を調べる\)** を選びます

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="データの分布を調べる" lightbox="../media/examine-data-distribution.png":::

4. **[Tag data]\(タグ データ\)** ページに戻り、別の方法でデータにタグを付ける方法を考えて、調整を行います。

## <a name="next-steps"></a>次のステップ

* モデルのパフォーマンスに問題がなくなったら、ランタイム API を使って[テキスト分類要求の送信](call-api.md)を始めることができます。
