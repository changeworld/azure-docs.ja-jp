---
title: Custom Vision とは
titleSuffix: Azure Cognitive Services
description: Azure Custom Vision サービスを使用して、Azure クラウド内にカスタム AI 画像分類器およびオブジェクト検出器を構築する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/14/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 画像認識、画像識別子、画像認識アプリ、Custom Vision
ms.openlocfilehash: aa338bf708abaa49d65b6a817009224973f3fb29
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616911"
---
# <a name="what-is-custom-vision"></a>Custom Vision とは

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Custom Vision は、独自の画像識別子を構築、デプロイ、改良できるようにする画像認識サービスです。 画像識別子は、視覚特性に従って画像に (クラスまたはオブジェクトを表す) ラベルを適用します。 [Computer Vision](../computer-vision/overview.md) サービスとは異なり、Custom Vision ではユーザーがラベルを指定し、それらを検出するためのカスタム モデルをトレーニングできます。

## <a name="what-it-does"></a>実行内容

Custom Vision サービスでは、画像の分析に機械学習アルゴリズムを使用します。 開発者は、着目する特性を備えた画像グループと、それを欠いた画像グループを提出します。 提出時に自分で画像にラベルを付けます。 次に、アルゴリズムをこのデータでトレーニングし、同じ画像に対するテストを行って、アルゴリズム自体の精度を計算します。 アルゴリズムのトレーニング後、テスト、再トレーニングを行い、最終的にはアルゴリズムを画像認識アプリ内で使用して、新しい画像を分類することができます。 オフラインで使用するために、モデル自体をエクスポートすることもできます。

### <a name="classification-and-object-detection"></a>分類とオブジェクトの検出

Custom Vision の機能は、2 つに分割できます。 **画像の分類** では、画像に 1 つまたは複数のラベルを適用します。 **オブジェクトの検出** では、同様のことを行いますが、適用されたラベルを見つけることができる画像内の座標も返されます。

### <a name="optimization"></a>Optimization

Custom Vision サービスは、画像間の主な違いをすばやく認識するために最適化されているため、少量のデータを含むモデルでプロトタイプの作成を始めることができます。 ラベルあたり 50 個の画像が、通常は適切な出発点です。 しかし、このサービスは、画像の微妙な違いの検出 (たとえば、品質保証のシナリオにおける小さな割れやへこみの検出) には最適ではありません。

また、Custom Vision アルゴリズムのいくつかの種類の中から、特定の対象素材 (たとえば、ランドマークや小売り品目) が含まれている画像に適したものを選択することもできます。 詳細については、[分類器の構築](getting-started-build-a-classifier.md)および[オブジェクト検出器の構築](get-started-build-detector.md)に関するガイドを参照してください。

## <a name="what-it-includes"></a>備えている機能

Custom Vision Service は、[Custom Vision の Web サイト](https://customvision.ai/)の Web ベースのインターフェイスを通じて利用するだけでなく、ネイティブ SDK のセットとしても利用できます。 いずれかのインターフェイス、または両方のインターフェイスを使用して、モデルの作成、テスト、およびトレーニングを行うことができます。

![Chrome ブラウザー ウィンドウでの Custom Vision の Web サイト](media/browser-home.png)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Custom Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

[分類器の構築](getting-started-build-a-classifier.md)に関するガイドに従って、Web ポータルで Custom Vision の使用を開始するか、[クライアント ライブラリのクイックスタート](quickstarts/image-classification.md)を実行して、コードで基本的なシナリオを実装します。