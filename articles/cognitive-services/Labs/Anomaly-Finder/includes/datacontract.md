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
ms.openlocfilehash: e37d3ef5b6f65ad31bc19f9f8c15350014d1c9ad
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35375368"
---
[Anomaly Finder API](https://labs.cognitive.microsoft.com/en-us/project-anomaly-finder) を使用すると、JSON 形式の時系列データを API エンドポイントにアップロードし、API 応答から結果を読み取ることができます。 時系列データはアップロード可能で、各データ ポイントには以下が含まれています。  
* Timestamp - データ ポイントのタイムスタンプ。 UTC で日時を示した文字列 (例: 2017-08-01T00:00:00Z) を使用するようにしてください
* Value - そのデータ ポイントの測定値

結果は以下から構成されています。
* Period - API が異常検出のために使用する周期性
* WarningText - ありえる警告の情報
* ExpectedValue - 学習ベースのモデルによって予測された値
* IsAnomaly - データ ポイントが異常値であるかどうか、両方向 (急激な上昇または下落) でないかに関する結果
* IsAnomaly_Neg - データ ポイントがマイナス方向の異常値 (急激な下落) であるかどうかに関する結果
* IsAnomaly_Pos - データ ポイントがプラス方向の異常値 (急激な上昇) であるかどうかに関する結果
* UpperMargin - ExpectedValue と UpperMargin の合計によって、データ ポイントがまだ正常であると判断される上の境界が決まります
* LowerMargin - ExpectedValue から LowerMargin を引いた値によって、データ ポイントがまだ正常であると判断される下の境界が決まります

データ コントラクトの詳細については、[ここ](../apiref.md)を参照してください。

