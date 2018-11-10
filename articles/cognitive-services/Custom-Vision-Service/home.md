---
title: Azure Custom Vision とは
titlesuffix: Azure Cognitive Services
description: Custom Vision サービスを使用して、Azure クラウド内にカスタム画像分類器を構築する方法について説明します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 10/26/2018
ms.author: anroth
ms.openlocfilehash: 5e8e675b32bfd4c741b82b1ab341a80adbb0529d
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741642"
---
# <a name="what-is-azure-custom-vision"></a>Azure Custom Vision とは

Azure Custom Vision API は、カスタム画像分類器を構築、デプロイ、および改良できるようにする認識サービスです。 画像分類器は、特定の性質に基づいて画像をクラス (タグ) に分類する AI サービスです。 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) サービスとは異なり、Custom Vision では独自の分類を作成できます。

## <a name="what-it-does"></a>実行内容

Custom Vision サービスでは、画像の分類に機械学習アルゴリズムを使用します。 開発者は、分類の判定要素を備えた画像グループと、それを欠いた画像グループを提出する必要があります。 提出時に、画像の適切なタグを指定します。 次に、アルゴリズムをこのデータでトレーニングし、同じデータに対するテストを行って、アルゴリズム自体の精度を計算します。 モデルのトレーニング後、テスト、再トレーニングを行い、最終的にはモデルを使用して、新しい画像をアプリのニーズに合わせて分類することができます。 オフラインで使用するために、モデル自体をエクスポートすることもできます。

### <a name="classification-and-object-detection"></a>分類とオブジェクトの検出

Custom Vision の機能は、2 つに分割できます。 **画像の分類**では、各画像に分類の分布を割り当てます。 **オブジェクトの検出**では、同様のことを行いますが、適用されたタグを見つけることができる画像内の座標も返されます。

### <a name="optimization"></a>最適化

一般に、Custom Vision サービスで使用される方法は差異の影響を受けにくいため、少量のデータでプロトタイプを作り始めることができます。 タグあたり 50 個の画像が、通常は適切な出発点です。 しかし、これは、画像の微妙な違いの検出 (たとえば、品質保証のシナリオにおける小さな割れやへこみの検出) にはこのサービスが最適ではないということを意味します。

また、Custom Vision アルゴリズムのいくつかの種類の中から、特定の対象素材 (たとえば、ランドマークや小売り品目) に適したものを選択することもできます。 それらの詳細については、[分類器の構築](getting-started-build-a-classifier.md)に関するガイドを参照してください。

## <a name="what-it-includes"></a>備えている機能
Custom Vision Service は、[Custom Vision ホーム ページ](https://customvision.ai/)の Web ベースのインターフェイスを通じて利用するだけでなく、ネイティブ SDK のセットとしても利用できます。 いずれかのインターフェイス、または両方のインターフェイスを使用して、モデルの作成、テスト、およびトレーニングを行うことができます。

![Chrome ブラウザー ウィンドウでの Custom Vision ホーム ページ](media/browser-home.png)

## <a name="next-steps"></a>次の手順

[分類器の構築](getting-started-build-a-classifier.md)に関するガイドに従って、Web で Custom Vision の使用を開始するか、[画像分類のチュートリアル](csharp-tutorial.md)を実行して、コードでシナリオを実装します。
