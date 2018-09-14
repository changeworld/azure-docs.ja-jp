---
title: Custom Vision Service リリース ノート
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.date: 08/28/2018
ms.author: anroth
ms.openlocfilehash: 8423051fa5169eb8acddc7297e36188e297cb9e4
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43288658"
---
# <a name="custom-vision-service-release-notes"></a>Custom Vision Service リリース ノート

## <a name="august-14-2018"></a>2018 年 8 月 14 日
- ユーザーにプロジェクトのトレーニングを案内する "作業の開始" ウィジェットが customvision.ai サイトに追加されました。 
- マルチラベル プロジェクト (新しい損失層) で役立つように機械学習パイプラインがさらに機能強化されました。

## <a name="june-28-2018"></a>2018 年 6 月 28 日
- バグ修正とバックエンドの機能強化。
- 複数のイメージが正確に 1 つのラベルを持っているプロジェクトで、多クラス分類が有効になりました。 多クラス モードの予測では、確率が 1 つに合計されます (すべてのイメージが指定されたタグの間で分類されます)。

## <a name="june-13-2018"></a>2018 年 6 月 13 日
- 使いやすさとアクセシビリティに重点を置いて、UX が更新されました。 
- 多数のタグがあるマルチラベル プロジェクト で役立つように機械学習パイプラインが機能強化されました。
- TensorFlow のエクスポートのバグが修正されました。 エクスポートされたモデルのバージョン管理が有効になったため、イテレーションを複数回エクスポートできるようになりました。 

## <a name="may-7-2018"></a>2018 年 5 月 7 日
- 制限付き試用版プロジェクトに関してオブジェクト検出機能 (プレビュー) が導入されました。
- API が 2.0 にアップグレードされました。
- S0 レベルが最大 250 タグおよび 50,000 画像に拡張されました。 
- 画像分類プロジェクトの機械学習パイプラインに対し、重要なバックエンドの改良が行われました。 この更新の効果は、2018 年 4 月 27 日より後にトレーニングされたプロジェクトで得られます。
- Windows ML で使用できる ONNX へのモデルのエクスポートが追加されました。
- Dockerfile へのモデルのエクスポートが追加されました。 これにより、成果物 (DockerFile、TensorFlow モデル、サービス コードなど) をダウンロードして独自の Windows コンテナまたは Linux コンテナを構築することができます。 
- General (Compact) ドメインと Landmark (Compact) ドメインの TensorFlow に、新たにトレーニングされたモデルをエクスポートした場合、全プロジェクト間で一貫性を確保するために、[平均値は (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample) になります。 

## <a name="march-1-2018"></a>2018 年 3 月 1 日
- 有料プレビューとなり、Azure portal への配布準備が行われました。 F0 (Free) レベルまたは S0 (Standard) レベルで Azure リソースにプロジェクトをアタッチできるようになりました。 S0 レベルのプロジェクトを導入しました。最大 100 タグおよび 25,000 画像が利用できます。 
- 機械学習パイプライン/正規化パラメーターに対するバックエンド変更。 これによりユーザーが確率のしきい値を調整する際に、精度とリコールのトレードオフを制御しやすくなります。 この変更の一環として、CustomVision.ai ポータルでは既定の確率のしきい値が 50% に設定されました。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 既にリリースされている iOS へのエクスポート (CoreML) に加え、Android へのエクスポート (TensorFlow) が追加されました。これにより、トレーニング済みのコンパクト モデルをエクスポートして、アプリケーションでオフライン実行することができます。
- Retail および Landmark の "コンパクト" ドメインを追加し、これらのドメインのモデルのエクスポートに対応しました。
- [1.2 Training API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) および [1.1 Prediction API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164) バージョンをリリースしました。 更新された API は、モデルのエクスポート、"Predictions" に画像を保存しない新しい予測操作をサポートします。また、Training API にバッチ操作が導入されています。
- UX が調整されています (イテレーションのトレーニングに使用されたドメインを確認する機能など)。
- [C# SDK およびサンプル](https://github.com/Microsoft/Cognitive-CustomVision-Windows)を更新しました。

