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
ms.openlocfilehash: d2daf7c211f9474f5636b6af69c5b700d597aa14
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43285246"
---
# <a name="what-is-the-custom-vision-service"></a>Custom Vision Service とは

Custom Vision Service は、カスタム画像分類器を構築できる Microsoft Cognitive Services です。 画像の分類器を簡単に素早く構築、デプロイし、その性能を向上させることができます。 Custom Vision Service には、画像をアップロードして分類器をトレーニングするための REST API や Web インターフェイスが用意されています。

## <a name="what-does-custom-vision-service-do-well"></a>Custom Vision Service でできること

Custom Vision Service は、分類したい項目が画像の中で目立つ場合に最も良く動作します。 

分類器を作るうえで画像はほとんど必要ありません。 クラスごとに 50 枚もあれば、プロトタイプを作り始めることができます。 Custom Vision Service は差異の影響を受けづらいため、データが少なくてもプロトタイプを作り始めることができます。 しかしこれは、ごく微妙な違いを検出したいシナリオには Custom Vision Service はあまり適さないということを意味します (品質保証のシナリオにおける小さな割れやへこみなど)。

## <a name="next-steps"></a>次の手順

[分類器の構築方法について](getting-started-build-a-classifier.md)
