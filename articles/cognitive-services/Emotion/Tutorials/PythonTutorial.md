---
title: Emotion API Python チュートリアル | Microsoft Docs
description: Jupyter Notebook を使用して、Cognitive Services Emotion API を Python で使用する方法について説明します。 人気のあるライブラリを使用して結果を視覚化します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374093"
---
# <a name="emotion-api-using-python-tutorial"></a>Python チュートリアルを使用した Emotion API

> [!IMPORTANT]
> Video API のプレビューは 2017 年 10 月 30 日をもって終了します。 新しい [Video Indexer API のプレビュー](https://azure.microsoft.com/services/cognitive-services/video-indexer/)をお試しください。ビデオから分析情報を手軽に抽出でき、ビデオ内で話される言葉や表情、性格、感情を検知することで、検索結果などのコンテンツの検索エクスペリエンスを強化できます。 [詳細情報](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)。

Emotion API を簡単に使用し始められるように、下にリンクされた Jupyter Notebook には、Python での API の使用方法と、いくつかの人気のあるライブラリを使用して結果を視覚化する方法が示されています。 

[GitHub のノートブックへのリンク](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Jupyter Notebook の使用

ノートブックを対話式に使用するには、それを複製し、Jupyter で実行する必要があります。 対話型の Jupyter Notebook を使い始める方法については、http://jupyter.readthedocs.org/en/latest/install.html の指示に従ってください。 

このノートブック使用するには、Emotion API のサブスクリプション キーが必要です。 サインアップするため、[サブスクリプション ページ](https://azure.microsoft.com/try/cognitive-services/)に移動します。 [サインイン] ページで、Microsoft アカウントを使用してサインインすると、サブスクライブして無料のキーを取得できます。 サインアップ プロセスを完了したら、下に示したセクションにキーを貼り付けます。 プライマリ キーまたはセカンダリ キーのどちらでもかまいません。

```
Python Example 

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
