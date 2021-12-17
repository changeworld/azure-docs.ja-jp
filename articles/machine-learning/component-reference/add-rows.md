---
title: '行の追加: コンポーネント リファレンス'
titleSuffix: Azure Machine Learning
description: Azure Machine Learning デザイナーで行の追加コンポーネントを使用して、2 つのデータセットを連結する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 856821f810397f6b14b383281d2e566b9c6e16d2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566076"
---
# <a name="add-rows-component"></a>行の追加コンポーネント

この記事では Azure Machine Learning デザイナーのコンポーネントについて説明します。

このコンポーネントを使用して、2 つのデータセットを連結します。 連結では、2 番目のデータセットの行が最初のデータセットの末尾に追加されます。  
  
行の連結は、次のようなシナリオで役立ちます。  
  
+ 一連の評価統計を生成した後、レポート作成を容易にするためにそれらを 1 つの表に結合する。  
  
+ さまざまなデータセットを使用していて、それらのデータセットを結合して最終的なデータセットを作成する。  

## <a name="how-to-use-add-rows"></a>行の追加の使用方法  

2 つのデータセットの行を連結するには、それぞれの行のスキーマがまったく同じである必要があります。 つまり、列の数と列内のデータの型が同じである必要があります。

1.  **Add Rows (行の追加)** コンポーネントをパイプラインにドラッグします。これは **[データ変換]** の下で見つけることができます。

2. データセットを 2 つの入力ポートに接続します。 追加するデータセットは、2 番目 (右) のポートに接続する必要があります。 
  
3.  パイプラインを送信します。 出力データセットの行数は、両方の入力データセットの行の合計に等しくなければなりません。

    **Add Rows (行の追加)** コンポーネントの両方の入力に同じデータセットを追加すると、データセットは複製されます。 

## <a name="next-steps"></a>次の手順

Azure Machine Learning で[使える一連のコンポーネント](component-reference.md)を参照してください。 