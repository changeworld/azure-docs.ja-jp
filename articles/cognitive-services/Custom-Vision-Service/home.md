---
title: Custom Vision Service とは
titlesuffix: Azure Cognitive Services
description: Custom Vision Service を使用すると、Azure クラウド内にカスタム画像分類器を構築できます。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: overview
ms.date: 05/02/2018
ms.author: anroth
ms.openlocfilehash: 1d3d1a82cf59b06625487fb241a63f51352e18e5
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/19/2018
ms.locfileid: "46365861"
---
# <a name="what-is-the-custom-vision-service"></a>Custom Vision Service とは

Custom Vision Service は、カスタム画像分類器を構築できる Azure Cognitive Service です。 画像の分類器を簡単に素早く構築、デプロイし、その性能を向上させることができます。 Custom Vision Service には、画像をアップロードして分類器をトレーニングするための REST API や Web インターフェイスが用意されています。

## <a name="what-does-custom-vision-service-do-well"></a>Custom Vision Service でできること

Custom Vision Service は、分類したい項目が画像の中で目立つ場合に最も良く動作します。 

分類子を作るうえで画像はほとんど必要ありません。 クラスごとに 50 枚もあれば、プロトタイプを作り始めることができます。 Custom Vision Service は差異の影響を受けづらいため、データが少なくてもプロトタイプを作り始めることができます。 しかしこれは、ごく微妙な違いを検出したいシナリオには Custom Vision Service はあまり適さないということを意味します (品質保証のシナリオにおける小さな割れやへこみなど)。

## <a name="next-steps"></a>次の手順

[分類子の構築方法について](getting-started-build-a-classifier.md)
