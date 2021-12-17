---
title: 事前構築済み Docker イメージ
titleSuffix: Azure Machine Learning
description: Azure Machine Learning の推論 (スコアリング) 用の事前構築済み Docker イメージ
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 10/21/2021
ms.topic: conceptual
ms.reviewer: larryfr
ms.custom: deploy, docker, prebuilt, curated environments
ms.openlocfilehash: 0f7fc034ee6df962ecfe6e00b62b8899e223505c
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563703"
---
# <a name="prebuilt-docker-images-for-inference"></a>推論用の事前構築済み Docker イメージ

推論用の事前構築済みの Docker コンテナー イメージは、Azure Machine Learning でモデルをデプロイする際に使用します。  これらのイメージは、一般的な機械学習フレームワークと Python パッケージを使用して事前に構築されています。 また、次のいずれかの方法を使用して、パッケージを拡張して他のパッケージを追加することもできます。

* [Python パッケージを追加する](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [新しい Dockerfile のベースとして事前構築済みの推論イメージを使用する](how-to-extend-prebuilt-docker-image-inference.md)。 この方法を使用すると、**Python パッケージと apt パッケージ** の両方をインストールできます。

## <a name="why-should-i-use-prebuilt-images"></a>事前構築済みのイメージを使用する理由

* モデル デプロイの待ち時間が短縮される。
* モデル デプロイの成功率が向上する。
* モデル デプロイ時に不要なイメージのビルドを回避できる。
* イメージまたはコンテナーに必要な依存関係とアクセス権のみが付与される。 

## <a name="list-of-prebuilt-docker-images-for-inference"></a>推論用の事前構築済み Docker イメージの一覧 

[!INCLUDE [list-of-inference-prebuilt-docker-images](../../includes/aml-inference-list-prebuilt-docker-images.md)]

## <a name="next-steps"></a>次の手順

* [Python パッケージを事前構築済みのイメージに追加する](how-to-prebuilt-docker-images-inference-python-extensibility.md)。
* [新しい Dockerfile のベースとして事前構築済みのパッケージを使用する](how-to-extend-prebuilt-docker-image-inference.md)。