---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: df7326cb8e671d0f71924e813a1354dfef1e20c7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375296"
---
予期される値と既定の余白の上限と下限を含むデータが返されます。 実際には、[sensitivity] パラメーターを定義し、(ExpectedValue + sensitivity * UpperMargin) を境界の上限、(ExpectedValue - sensitivity * LowerMargin) を境界の下限として使用して異常ポイントをチューニングすることができます。 [sensitivity] の値は、1 より大きい必要があります。 チューニング用のダイアグラムを以下にいくつか示します。

> [!NOTE]
> ダイアグラムは、サンプル アプリケーションで生成されたものではありません。 サンプル アプリケーションと別のツールを使って作成されています。

![チューニング: sensitivity = 1.0](../media/sensitivity_1.png)
![チューニング: sensitivity = 1.5](../media/sensitivity_1.5.png)
![チューニング: sensitivity = 2](../media/sensitivity_2.png)
![チューニング: sensitivity = 3.5](../media/sensitivity_3.5.png)