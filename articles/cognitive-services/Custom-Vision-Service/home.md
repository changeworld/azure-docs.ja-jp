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
ms.date: 01/10/2019
ms.author: anroth
ms.openlocfilehash: d9b5473e87d83912dd2f33c00e983e346226e4aa
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2019
ms.locfileid: "54242949"
---
# <a name="what-is-azure-custom-vision"></a>Azure Custom Vision とは

Azure Custom Vision API は、カスタム画像分類器を構築、デプロイ、および改良できるようにする認識サービスです。 画像分類器は、特定の性質に基づいて画像をクラス (タグ) に分類する AI サービスです。 [Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) サービスとは異なり、Custom Vision では独自の分類を作成できます。

## <a name="what-it-does"></a>実行内容

Custom Vision サービスでは、画像の分類に機械学習アルゴリズムを使用します。 開発者は、分類の判定要素を備えた画像グループと、それを欠いた画像グループを提出する必要があります。 提出時に、画像の適切なタグを指定します。 次に、アルゴリズムをこのデータでトレーニングし、同じデータに対するテストを行って、アルゴリズム自体の精度を計算します。 モデルのトレーニング後、テスト、再トレーニングを行い、最終的にはモデルを使用して、新しい画像をアプリのニーズに合わせて分類することができます。 オフラインで使用するために、モデル自体をエクスポートすることもできます。

### <a name="classification-and-object-detection"></a>分類とオブジェクトの検出

Custom Vision の機能は、2 つに分割できます。 **画像の分類**では、各画像に分類の分布を割り当てます。 多クラス (画像ごとのタグ数が 1 つ) とマルチラベル (画像ごとのタグ数が任意) の両方の分類モデルがサポートされます。 **オブジェクトの検出**では、マルチラベル分類と同様のことを行いますが、適用されたラベルを見つけることができる画像内の座標も返されます。

### <a name="optimization"></a>最適化

一般に、Custom Vision サービスで使用される方法は差異の影響を受けにくいため、少量のデータでプロトタイプを作り始めることができます。 タグあたり 50 個の画像が、通常は適切な出発点です。 しかし、これは、画像の微妙な違いの検出 (たとえば、品質保証のシナリオにおける小さな割れやへこみの検出) にはこのサービスが最適ではないということを意味します。

また、Custom Vision アルゴリズムのいくつかの種類の中から、特定の対象素材 (たとえば、ランドマークや小売り品目) に適したものを選択することもできます。 それらの詳細については、[分類器の構築](getting-started-build-a-classifier.md)に関するガイドを参照してください。

## <a name="what-it-includes"></a>備えている機能
Custom Vision Service は、[Custom Vision ホーム ページ](https://customvision.ai/)の Web ベースのインターフェイスを通じて利用するだけでなく、ネイティブ SDK のセットとしても利用できます。 いずれかのインターフェイス、または両方のインターフェイスを使用して、モデルの作成、テスト、およびトレーニングを行うことができます。

![Chrome ブラウザー ウィンドウでの Custom Vision ホーム ページ](media/browser-home.png)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Custom Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次の手順

[分類器の構築](getting-started-build-a-classifier.md)に関するガイドに従って、Web で Custom Vision の使用を開始するか、[画像分類のチュートリアル](csharp-tutorial.md)を実行して、コードでシナリオを実装します。
