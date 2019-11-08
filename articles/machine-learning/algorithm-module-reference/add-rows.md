---
title: 行の追加:モジュール リファレンス
titleSuffix: Azure Machine Learning
description: Azure Machine Learning で Add Rows (行の追加) モジュールを使用して、2 つのデータセットを連結する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a16dc0bfc3fae8852c46435f6407c56149e1b14e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493948"
---
# <a name="add-rows-module"></a>Add Rows (行の追加) モジュール

この記事では Azure Machine Learning デザイナー (プレビュー) のモジュールについて説明します。

このモジュールを使用して、2 つのデータセットを連結します。 連結では、2 番目のデータセットの行が最初のデータセットの末尾に追加されます。  
  
行の連結は、次のようなシナリオで役立ちます。  
  
+ 一連の評価統計を生成した後、レポート作成を容易にするためにそれらを 1 つの表に結合する。  
  
+ さまざまなデータセットを使用していて、それらのデータセットを結合して最終的なデータセットを作成する。  

## <a name="how-to-use-add-rows"></a>行の追加の使用方法  

2 つのデータセットの行を連結するには、それぞれの行のスキーマがまったく同じである必要があります。 つまり、列の数と列内のデータの型が同じである必要があります。

1.  **Add Rows (行の追加)** モジュールをパイプラインにドラッグします。これは **[Manipulate]\(操作\)** カテゴリの **[Data Transformation]\(データ変換\)** の下にあります。

2. データセットを 2 つの入力ポートに接続します。 追加するデータセットは、2 番目 (右) のポートに接続する必要があります。 
  
3.  パイプラインを実行します。 出力データセットの行数は、両方の入力データセットの行の合計に等しくなければなりません。

    **Add Rows (行の追加)** モジュールの両方の入力に同じデータセットを追加すると、データセットは複製されます。 

## <a name="next-steps"></a>次の手順

Azure Machine Learning で[使用できる一連のモジュール](module-reference.md)を参照してください。 