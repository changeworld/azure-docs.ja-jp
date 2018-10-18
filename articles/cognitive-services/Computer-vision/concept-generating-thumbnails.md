---
title: サムネイルの生成 - Computer Vision
titleSuffix: Azure Cognitive Services
description: Computer Vision API を使用するイメージ サムネイルの生成に関する概念。
services: cognitive-services
author: deken
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: ff16fa4d30fb60e6b1376027a40242af23be04d1
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45985349"
---
# <a name="generating-thumbnails"></a>サムネイルの生成

サムネイルは、フルサイズのイメージを縮小したものです。 携帯電話、タブレット、PC などのさまざまなデバイスで、さまざまなユーザー エクスペリエンス (UX) のレイアウトとサムネイル サイズが必要になります。 この Computer Vision 機能は、スマート トリミングを使用して問題を解決するのに役立ちます。

イメージのアップロード後、Computer Vision では高品質のサムネイルを生成してから、イメージ内のオブジェクトを分析して*関心領域* (ROI) を識別します。 必要に応じて、ROI の要件に合わせてイメージをトリミングができます。 ニーズに合わせて、元のイメージの縦横比とは異なる縦横比を使用して、生成されたサムネイルを表示することができます。

サムネイルのアルゴリズムは次のように動作します。

1. イメージから不要な要素を削除し、メイン オブジェクトである、関心領域を識別します。
2. 識別された関心領域に基づいて、イメージをトリミングします。
3. ターゲットのサムネイルの寸法に合わせて、縦横比を変更します。

生成されるサムネイルは、以下の図に示すように、高さ、幅、スマート トリミングについて指定する内容によって大きく異なる場合があります。

![サムネイル](./Images/thumbnail-demo.png)

## <a name="thumbnail-generation-examples"></a>サムネイルの生成例

次の表は、Computer Vision で生成される、イメージ例に対する一般的なサムネイルを示しています。 スマート トリミングが有効になっており、ターゲットの高さと幅が 50 ピクセルに指定されたサムネイルが生成されています。

| イメージ | サムネイル |
|-------|-----------|
|![屋外の山](./Images/mountain_vista.png) | ![屋外の山のサムネイル](./Images/mountain_vista_thumbnail.png) |
|![Vision Analyze 花](./Images/flower.png) | ![Vision Analyze 花のサムネイル](./Images/flower_thumbnail.png) |
|![屋上にいる女性](./Images/woman_roof.png) | ![屋上にいる女性のサムネイル](./Images/woman_roof_thumbnail.png) |

## <a name="next-steps"></a>次の手順

[イメージへのタグ付け](concept-tagging-images.md)および[イメージの分類](concept-categorizing-images.md)に関する概念を確認します。