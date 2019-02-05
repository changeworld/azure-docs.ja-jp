---
title: チュートリアル:画像から顔の表情を認識する - Emotion API (Python)
titlesuffix: Azure Cognitive Services
description: Jupyter Notebook を使用して、Emotion API を Python で使用する方法について説明します。 人気のあるライブラリを使用して結果を視覚化します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ea96495fb9fa453f7c7f9d6a870291329d2793b2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220537"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>チュートリアル:Jupyter Notebook と Python で Emotion API を使用する。

> [!IMPORTANT]
> Emotion API は、2019 年 2 月 15 日に非推奨となる予定です。 現在は、[Face API](https://docs.microsoft.com/azure/cognitive-services/face/) の一部として感情認識機能が一般提供されています。 

Emotion API を簡単に使用し始められるように、下にリンクされた Jupyter Notebook には、Python での API の使用方法と、いくつかの人気のあるライブラリを使用して結果を視覚化する方法が示されています。

[GitHub のノートブックへのリンク](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Jupyter Notebook の使用

ノートブックを対話式に使用するには、それを複製し、Jupyter で実行する必要があります。 対話型の Jupyter Notebook を使い始める方法については、 http://jupyter.readthedocs.org/en/latest/install.html の指示に従ってください。

このノートブック使用するには、Emotion API のサブスクリプション キーが必要です。 サインアップするため、[サブスクリプション ページ](https://azure.microsoft.com/try/cognitive-services/)に移動します。 [サインイン] ページで、Microsoft アカウントを使用してサインインすると、サブスクライブして無料のキーを取得できます。 サインアップ プロセスを完了したら、下に示したセクションにキーを貼り付けます。 プライマリ キーまたはセカンダリ キーのどちらでもかまいません。

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
