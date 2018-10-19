---
title: 'チュートリアル: Computer Vision API Python'
titlesuffix: Azure Cognitive Services
description: Jupyter Notebook を使用することで、Computer Vision API を Python で使用する方法について説明します。 人気のあるライブラリを使用して結果を視覚化します。
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.date: 02/25/2017
ms.author: kefre
ms.openlocfilehash: 59e88ecb253bc6da803ddf34a4a02229b99e259d
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "45981713"
---
# <a name="tutorial-computer-vision-api-python"></a>チュートリアル: Computer Vision API Python

このチュートリアルでは、Python で Computer Vision API を使用する方法と、広く使われているいくつかのライブラリを使用して結果を視覚化する方法を示します。 Jupyter を使用してチュートリアルを実行します。 対話型の Jupyter Notebook を使い始める方法については、[Jupyter のドキュメント](http://jupyter.readthedocs.io/en/latest/index.html)を参照してください。 

### <a name="opening-the-tutorial-notebook-in-jupyter"></a>Jupyter でチュートリアル ノートブックを開く 

1. [GitHub でチュートリアル ノートブック](https://github.com/Microsoft/Cognitive-Vision-Python)に移動します。 
2. 緑色のボタンをクリックし、チュートリアルを複製またはダウンロードします。 
3. コマンド プロンプトを開き、_Cognitive-Vision-Python-master\Jupyter Notebook_ フォルダーに移動します。 
4. コマンド プロンプトから **jupyter notebook** コマンドを実行します。 これで Jupyter が起動します。
5. Jupyter のウィンドウで、_[Computer Vision API Example.ipynb]_ をクリックしてチュートリアル ノートブックを開きます 

### <a name="running-the-tutorial"></a>チュートリアルを実行する

このノートブック使用するには、Computer Vision API のサブスクリプション キーが必要です。 サインアップするため、[サブスクリプション ページ](https://azure.microsoft.com/try/cognitive-services/)に移動します。 [サインイン] ページで、Microsoft アカウントを使用してサインインすると、サブスクライブして無料のキーを取得できます。 サインアップ プロセスを完了したら、ノートブックの変数のセクション (下の例を参照) にキーを貼り付けます。 プライマリ キーとセカンダリ キーのどちらでもかまいません。 キーは引用符で囲み、文字列になるようにしてください。

```python
# Variables

_url = 'https://westcentralus.api.cognitive.microsoft.com/vision/v1/analyses'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```
