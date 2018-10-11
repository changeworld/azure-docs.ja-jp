---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: a1cda1cea2089363331ae437cb7ad802429779f4
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888698"
---
予期される値と既定の余白の上限と下限を含むデータが返されます。 実際には、[sensitivity] パラメーターを定義し、(ExpectedValue + sensitivity * UpperMargin) を境界の上限、(ExpectedValue - sensitivity * LowerMargin) を境界の下限として使用して異常ポイントをチューニングすることができます。 [sensitivity] の値は、1 より大きい必要があります。 チューニング用のダイアグラムを以下にいくつか示します。

> [!NOTE]
> ダイアグラムは、サンプル アプリケーションで生成されたものではありません。 サンプル アプリケーションと別のツールを使って作成されています。

![チューニング: sensitivity = 1.0](../media/sensitivity_1.png)
![チューニング: sensitivity = 1.5](../media/sensitivity_1.5.png)
![チューニング: sensitivity = 2](../media/sensitivity_2.png)
![チューニング: sensitivity = 3.5](../media/sensitivity_3.5.png)