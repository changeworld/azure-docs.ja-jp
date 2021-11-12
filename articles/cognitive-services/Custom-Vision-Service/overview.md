---
title: Custom Vision とは
titleSuffix: Azure Cognitive Services
description: Azure Custom Vision サービスを使用して、オブジェクトの検出や画像の分類を行うカスタム AI モデルを作成する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 08/25/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 画像認識、画像識別子、画像認識アプリ、Custom Vision
ms.openlocfilehash: 6bf2f9de103416bc9dd35216bf85c609be966a23
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372513"
---
# <a name="what-is-custom-vision"></a>Custom Vision とは

Azure Custom Vision は、独自の画像識別子モデルを構築、デプロイ、改良できるようにする画像認識サービスです。 画像識別子は、検出された視覚特性に従って画像に (分類またはオブジェクトを表す) ラベルを適用します。 [Computer Vision](../computer-vision/overview.md) サービスとは異なり、Custom Vision ではユーザーが独自ラベルを指定し、それらを検出するためのカスタム モデルをトレーニングできます。

このドキュメントには、次のような記事が記載されています。
* [クイックスタート](./getting-started-build-a-classifier.md)は、サービスの呼び出しと結果の取得を短時間で行えるようにする、ステップバイステップの手順です。
* [攻略ガイド](./test-your-model.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [チュートリアル](./iot-visual-alerts-tutorial.md)はより長文のガイドであり、より広範なビジネス ソリューションの 1 コンポーネントとしてこのサービスを使用する方法を示すものです。
<!--* The [conceptual articles](Vision-API-How-to-Topics/call-read-api.md) provide in-depth explanations of the service's functionality and features.-->

## <a name="what-it-does"></a>実行内容

Custom Vision サービスでは、画像の分析に機械学習アルゴリズムを使用します。 開発者は、着目する特性を備えた画像グループと、それを欠いた画像グループを提出します。 提出時に自分で画像にラベルを付けます。 次に、アルゴリズムをこのデータでトレーニングし、同じ画像に対するテストを行って、アルゴリズム自体の精度を計算します。 アルゴリズムをトレーニングした後、テスト、再トレーニングを行い、最終的にアルゴリズムを画像認識アプリ内で使用して、[画像を分類](getting-started-build-a-classifier.md)できます。 オフラインで使用するために、[モデル自体をエクスポートする](export-your-model.md)こともできます。

### <a name="classification-and-object-detection"></a>分類とオブジェクトの検出

Custom Vision の機能は、2 つに分割できます。 **[画像の分類](getting-started-build-a-classifier.md)** では、画像に 1 つまたは複数のラベルを適用します。 **[オブジェクトの検出](get-started-build-detector.md)** では、同様のことを行いますが、適用されたラベルを見つけることができる画像内の座標も返されます。

### <a name="optimization"></a>Optimization

Custom Vision サービスは、画像間の主な違いをすばやく認識するために最適化されているため、少量のデータを含むモデルでプロトタイプの作成を始めることができます。 ラベルあたり 50 個の画像が、通常は適切な出発点です。 しかし、このサービスは、画像の微妙な違いの検出 (たとえば、品質保証のシナリオにおける小さな割れやへこみの検出) には最適ではありません。

また、Custom Vision アルゴリズムのいくつかの種類の中から、特定の対象素材 (たとえば、ランドマークや小売り品目) が含まれている画像に適したものを選択できます。 詳細については、「[ドメインの選択](select-domain.md)」を参照してください。

## <a name="what-it-includes"></a>備えている機能

Custom Vision Service は、[Custom Vision の Web サイト](https://customvision.ai/)の Web ベースのインターフェイスを通じて利用するだけでなく、ネイティブ SDK のセットとしても利用できます。 いずれかのインターフェイス、または両方のインターフェイスを使用して、モデルの作成、テスト、およびトレーニングを行うことができます。

![Chrome ブラウザー ウィンドウでの Custom Vision の Web サイト](media/browser-home.png)

## <a name="data-privacy-and-security"></a>データのプライバシーとセキュリティ

Cognitive Services 全般に言えることですが、Custom Vision サービスを使用する開発者は、顧客データに関する Microsoft のポリシーに留意する必要があります。 詳細については、Microsoft セキュリティ センターの [Cognitive Services のページ](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)を参照してください。

## <a name="next-steps"></a>次のステップ

[分類器の構築](getting-started-build-a-classifier.md)に関するガイドに従って、Web ポータルで Custom Vision の使用を開始するか、[クイックスタート](quickstarts/image-classification.md)を実行して、コードで基本的なシナリオを実装します。