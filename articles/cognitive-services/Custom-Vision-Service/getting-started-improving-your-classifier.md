---
title: Custom Vision Service を使用して分類器を改善する - Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service 分類器の品質を改善する方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 05/03/2018
ms.author: anroth
ms.openlocfilehash: 65b1424f259066c7d5bd6b2b508d2a4052ff0527
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374725"
---
# <a name="how-to-improve-your-classifier"></a>分類器を改善する方法

Custom Vision Service 分類器の品質を改善する方法について説明します。 分類器の品質は、それに提供するラベル付きデータの品質に左右されます。 

## <a name="train-more-varied-images"></a>変化に富むイメージをより多くトレーニングする

分類器は、さまざまな角度、背景、オブジェクトのサイズ、写真のグループ、およびその他の変種を持つタグ付したイメージを提供することで改善されます。 背景がある写真は、中間色の背景の前で撮った写真より適しています。 通常使用時に分類器に送信されるものの代表的なイメージを含めます。

イメージの追加の詳細については、[分類器のビルド](getting-started-build-a-classifier.md)に関するドキュメントを参照してください。

> [!IMPORTANT]
> イメージを追加したら、必ず分類器をトレーニングしてください。

## <a name="use-images-submitted-for-prediction"></a>予測のために送信されたイメージを使用する

Custom Vision Service は、予測エンドポイントに送信されたイメージを保存しています。 分類器を改善するためにこれらのイメージを使用するには、次の手順を使用します。

1. 分類器に送信したイメージを表示するには、[Custom Vision Web ページ](https://customvision.ai)を開き、__[Predictions]__(予測) タブを選択します。

    ![予測タブのイメージ](./media/getting-started-improving-your-classifier/predictions-tab.png)

    > [!TIP]
    > 既定のビューには、現在のイテレーションのイメージが表示されます。 __[Iteration]__(イテレーション) ドロップ ダウン フィールドを使用すると、以前のイテレーションで送信されたイメージを表示できます。

2. イメージの上にポインターを置き、分類器が予測したタグを表示します。

    > [!TIP]
    > イメージは、最も分類器に貢献したイメージが一番上になるように順位付けされます。 異なる並べ替えを選択するには、__[Sort]__(並べ替え) セクションを使用します。

    トレーニング データにイメージを追加するには、イメージとタグを選択してから __[保存して閉じる]__ を選択します。 イメージは __[Predictions]__(予測) から削除され、トレーニング イメージに追加されます。 __[Training Images]__(トレーニング イメージ) タブを選択すると、これを表示できます。

    ![タグ付けページのイメージ](./media/getting-started-improving-your-classifier/tag-image.png)

3. __[Train]__(トレーニング) ボタンを使用して、分類器を再トレーニングします。

## <a name="visually-inspect-predictions"></a>予測を視覚的に検査する

イメージの予測を検査するには、選択、__[Training Images]__(トレーニング イメージ) タブを選択してから、__[Iteration History]__(イテレーション履歴) を選択します。 赤いボックスで囲まれているイメージは、正しく予測されました。

![イテレーション履歴のイメージ](./media/getting-started-improving-your-classifier/iteration-history.png)

目視の検査をすると、他のトレーニング データを追加することで後から修正できるパターンの識別が可能な場合があります。 たとえば、バラとヒナギクを区別するための分類器が、白いバラを誤ってすべてヒナギクとしてラベルを付ける可能性があります。 この問題は、タグ付けした白いバラのイメージを含むトレーニング データを追加して提供することで、修正できる可能性があります。

## <a name="unexpected-classification"></a>予期しない分類

分類器は、提供したイメージが共通して持っている特性を学習場合があります。 たとえば、バラとチューリップを区別するための分類器を作成するとします。 野原に咲いたチューリップのイメージと、青い壁の前の赤い花瓶に挿された赤いバラを提供します。 このデータが与えられると、分類器はバラとチューリップの区別ではなく、野原と、壁プラス花瓶の区別についてトレーニングする可能性があります。

この問題を修正するには、より多くの変化に富むイメージを使用したトレーニングに関するガイダンスを利用します。さまざまな角度、背景、オブジェクトのサイズ、グループ、およびその他の変種を含むイメージを提供するのです。

## <a name="negative-image-handling"></a>イメージの否定処理

Custom Vision Service サービスは、イメージの自動否定処理をいくつかサポートしています。 ネコとイヌを区別する分類器とビルドしていて、予測のために片方の靴のイメージを送信した場合、分類器はネコとイヌの両方について、そのイメージに 0% に近いスコアを付ける必要があります。 

> [!WARNING]
> この自動アプローチは、明らかに該当しないイメージに対して機能します。 否定用のイメージが、トレーニングで使用されているイメージのバリエーションにすぎない場合は、うまく機能しないことがあります。 
>
> たとえば、ハスキーとコーギーを区別する分類器がある場合にポメラニアンのイメージを提供すると、ポメラニアンがハスキーとスコア付けされる可能性があります。 手元にある否定用のイメージがこのような性質のものである場合は、新しいタグ ("その他" など) を作成し、それを否定用のトレーニング イメージに適用します。

## <a name="next-steps"></a>次の手順

[予測 API の使用](use-prediction-api.md)