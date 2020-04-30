---
title: Custom Vision プロジェクトのドメインを選択する - Computer Vision
titleSuffix: Azure Cognitive Services
description: この記事では、Custom Vision Service でプロジェクトのドメインを選択する方法について説明します。
services: cognitive-services
author: shonohs
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: shono
ms.openlocfilehash: 1fb30cc0634224213dc9a188a16902e07d379904
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/29/2020
ms.locfileid: "82127767"
---
# <a name="select-a-domain-for-a-custom-vision-project"></a>Custom Vision プロジェクトのドメインを選択する

Custom Vision プロジェクトの [設定] ブレードで、プロジェクトのドメインを選択できます。 お使いのシナリオに最も近いドメインを選択します。

## <a name="image-classification"></a>画像の分類

|Domain|目的|
|---|---|
|__全般__| さまざまな画像分類タスクに最適化されています。 他のドメインのいずれも適切でないか、どのドメインを選択すればよいか不確かな場合は、汎用ドメインを選択してください。|
|__食料__|レストランのメニューで見るような料理の写真に最適化されています。 個々のフルーツや野菜の写真を分類する場合、食料ドメインを使用します。|
|__ランドマーク__|自然物と人工物の両方の認識可能なランドマークに最適化されています。 このドメインは、ランドマークが写真にはっきりと映っているときに最も効果があります。 このドメインは、ランドマークがその前にいる人々によって少し邪魔されている場合でも機能します。|
|__小売__|ショッピング カタログやショッピング Web サイトで見られる画像に最適化されています。 ドレス、ズボン、シャツを分類するときに高い精度が必要な場合に、このドメインを使用します。|
|__コンパクト ドメイン__| エッジ デバイスでのリアルタイムの分類の制約に最適化されています。|

## <a name="object-detection"></a>オブジェクトの検出

|Domain|目的|
|---|---|
|__全般__| さまざまなオブジェクト検出タスク用に最適化されています。 他のドメインのいずれも適切でないか、どのドメインを選択すればよいか不確かな場合は、汎用ドメインを選択してください。|
|__ロゴ__|画像内のブランド ロゴを探すために最適化されています。|
|__シェルブの製品__|シェルブで製品を検出して分類するために最適化されています。|
|__コンパクト ドメイン__| エッジ デバイス上でのリアルタイムのオブジェクト検出の制約に最適化されています。|

## <a name="compact-domains"></a>コンパクト ドメイン

コンパクト ドメインで生成されたモデルは、ローカルで実行するためにエクスポートできます。 モデルのパフォーマンスは、選択したドメインによって異なります。 次の表では、Intel Desktop CPU および NVidia GPU \[1\] でモデルのサイズと推論時間をレポートします。 

> [!NOTE]
> これらの数値には、前処理時間と後処理時間は含まれません。

|タスク|Domain|モデルのサイズ|CPU 推論時間|GPU 推論時間|
|---|---|---|---|---|
|分類|General (compact)|5 MB|13 ミリ秒|5 ミリ秒|
|オブジェクトの検出|General (compact)|45 MB|35 ミリ秒|5 ミリ秒|
|オブジェクトの検出|汎用（コンパクト） [S1]|14 MB|27 ミリ秒|7 ミリ秒|

## <a name="vaidk-vision-ai-dev-kit"></a>VAIDK （Vision AI Dev Kit）

コンパクト ドメインを選択すると、"Basic Platforms" と "Vision AI Dev Kit" を区別できるように、追加のオプション "エクスポート機能" が提供されます。

[_エクスポート機能_] で、次の2つのオプションがあります。

- 基本プラットフォーム （Tensorflow、CoreML、ONNX など）
- Vision AI Dev Kit。

_Vision AI Dev Kit_ が選択されている場合、_汎用_、_ランドマーク_、および _Retail_ であり、_Food_ ではないコンパクト ドメインは、Image Classification に使用できますが、_汎用 (コンパクト)_ および _汎用（コンパクト） [S1]_ の両方はオブジェクト検出に使用できます。

>[!NOTE]
>オブジェクト検出のための__汎用（コンパクト）__ ドメインには、特別な後処理ロジックが必要です。 詳細については、エクスポートされた zip パッケージのサンプル スクリプトを参照してください。 後処理ロジックのないモデルが必要な場合は、__汎用（コンパクト） [S1]__ を使用します。

>[!IMPORTANT]
>エクスポートされたモデルでは、クラウド上の予測 API とまったく同じ結果が得られるという保証はありません。 実行中のプラットフォームまたは前処理実装のわずかな違いにより、モデル出力の差が大きくなることがあります。 前処理ロジックの詳細については、[このドキュメント](quickstarts/image-classification.md) を参照してください。

\[1\] Intel Xeon E5-2690 CPU および NVIDIA Tesla M60
