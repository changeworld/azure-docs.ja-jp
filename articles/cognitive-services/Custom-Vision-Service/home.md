---
title: Custom Vision Service 機械学習の概要 - Azure Cognitive Services | Microsoft Docs
description: Custom Vision Service は、Azure プラットフォームにおけるカスタム画像分類子の構築を支援する Microsoft Cognitive Services です。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: de45fc399470a806fb7456cbbe936cecf659ee7c
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37087683"
---
# <a name="what-is-the-custom-vision-service"></a>Custom Vision Service とは

Custom Vision Service は、カスタム画像分類子の構築を支援する Microsoft Cognitive Services です。 画像の分類子を簡単に素早く構築、デプロイし、その性能を向上させることができます。 Custom Vision Service には、画像をアップロードして分類子をトレーニングするための REST API や Web インターフェイスが用意されています。

## <a name="what-does-custom-vision-service-do-well"></a>Custom Vision Service でできること

Custom Vision Service は、分類したい項目が画像の中で目立つ場合に最も良く動作します。 

分類子を作るうえで画像はほとんど必要ありません。 クラスごとに 50 枚もあれば、プロトタイプを作り始めることができます。 Custom Vision Service が使用する方法は差異の影響を受けづらいため、データが少なくてもプロトタイプを作り始めることができるのです。 しかしこれは、ごく微妙な違いを検出したいシナリオには Custom Vision Service があまり適さないということを意味します  (品質保証のシナリオにおける小さな割れやへこみなど)。

## <a name="release-notes"></a>リリース ノート

### <a name="may-7-2018"></a>2018 年 5 月 7 日
- 制限付き試用版プロジェクトに関してオブジェクト検出機能 (プレビュー) が導入されました。
- API が 2.0 にアップグレードされました。
- S0 レベルが最大 250 タグおよび 50,000 画像に拡張されました。 
- 画像分類プロジェクトの機械学習パイプラインに対し、重要なバックエンドの改良が行われました。 この更新の効果は、2018 年 4 月 27 日より後にトレーニングされたプロジェクトで得られます。
- Windows ML で使用できる ONNX へのモデルのエクスポートが追加されました。
- Dockerfile へのモデルのエクスポートが追加されました。 これにより、成果物 (DockerFile、TensorFlow モデル、サービス コードなど) をダウンロードして独自の Windows コンテナまたは Linux コンテナを構築することができます。 
- General (Compact) ドメインと Landmark (Compact) ドメインの TensorFlow に、新たにトレーニングされたモデルをエクスポートした場合、全プロジェクト間で一貫性を確保するために、[平均値は (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) になります。 

### <a name="march-1-2018"></a>2018 年 3 月 1 日
- 有料プレビューとなり、Azure portal への配布準備が行われました。 F0 (Free) レベルまたは S0 (Standard) レベルで Azure リソースにプロジェクトをアタッチできるようになりました。 S0 レベルのプロジェクトを導入しました。最大 100 タグおよび 25,000 画像が利用できます。 
- 機械学習パイプライン/正規化パラメーターに対するバックエンド変更。 これによりユーザーが確率のしきい値を調整する際に、精度とリコールのトレードオフを制御しやすくなります。 この変更の一環として、CustomVision.ai ポータルでは既定の確率のしきい値が 50% に設定されました。

### <a name="december-19-2017"></a>2017 年 12 月 19 日

- 既にリリースされている iOS へのエクスポート (CoreML) に加え、Android へのエクスポート (TensorFlow) が追加されました。これにより、トレーニング済みのコンパクト モデルをエクスポートして、アプリケーションでオフライン実行することができます。
- Retail および Landmark の "コンパクト" ドメインを追加し、これらのドメインのモデルのエクスポートに対応しました。
- [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) および [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) バージョンをリリースしました。 更新された API は、モデルのエクスポート、"Predictions" に画像を保存しない新しい予測操作をサポートします。また、Training API にバッチ操作が導入されています。
- UX が調整されています (イテレーションのトレーニングに使用されたドメインを確認する機能など)。
- [C# SDK およびサンプル](https://github.com/Microsoft/Cognitive-CustomVision-Windows)を更新しました。

## <a name="next-steps"></a>次の手順

[分類子の構築方法について](getting-started-build-a-classifier.md)
