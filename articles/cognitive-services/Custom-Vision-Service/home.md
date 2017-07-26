---
title: Custom Vision Service machine learning の概要 | Microsoft Docs
description: これを使えばあなたのアプリケーションに機械学習の力を持ち込むことができます。
services: cognitive-services
author: v-royhar
manager: juliakuz
ms.service: cognitive-services
ms.technology: custom vision service
ms.topic: article
ms.date: 07/26/2017
ms.author: v-royhar
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 51a4db987a6125daab7bb74affc04a5427ea3437
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017

---

# <a name="overview"></a>概要

## <a name="custom-vision-service-brings-the-power-of-machine-learning-to-your-apps"></a>Custom Vision Service はあなたのアプリケーションに機械学習の力を取り込みます

Custom Vision Service は Custom image の分類機を構築するためのツールです。それは、簡単そして素早く、構築・デプロイすることができます。さらに、画像の分類を向上させることができます。私たちは REST API や Web インターフェイスを通し 画像をアップロード、そして train します。

## <a name="what-can-custom-vision-service-do-well-what-cant-it-do"></a>Custom Vision Service は何ができる？何ができない？

Custom Vision Service は 画像の分類機を作成するともに、時間が経つごとによりよいものを作成します。もしあなたが、「Daisies」「Daffodils」「Dahlias」の画像を分類をするツールが必要な時は、 あなたは、 分類するためのものを学習させることができます。 あなたはCustom Vision Service を通じて認識させたい画像ごとにタグ付けして学習させることができます。

Custom Vision Service は 分類したい画像が目立つときに一番良く働きます。 Custom Vision Service は 画像の分類機です。しかし、 「オブジェクト検出」ではありません。 これが意味するのは、 Custom Vision Service は画像内にオブジェクトがあるかどうかは判定しますが、オブジェクトがどこにあるかは識別しません。

分類機を作るためには、とても多くの画像を要求します。 --　一つのクラスのプロトタイプを作成するのに必要な画像は 30枚必要です。この方法を Custom Vision Serviceが使っているのは 強固な違いを作るためです。 もしあなたが、少ないデータでプロトタイプを作成してしまったら、 Custom Vision Service は 微妙な違いを検出するのは難しくなります。 (例えば、 品質保証する場面での 微妙な 壊れやへこみなど)

Custom Vision Service は 簡単に分類機を作成できるようにデザインされています。そして、 時間が経つごとにあなたの分類機の性能の向上を助けます。


## <a name="next-steps"></a>次のステップ

[Build a Classifier](getting-started-build-a-classifier.md)

