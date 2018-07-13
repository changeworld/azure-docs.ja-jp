---
title: Custom Vision Service の制限とクォータ - Azure Cognitive Services | Microsoft Docs
description: Azure Cognitive Services Custom Vision Service の制限とクォータについて説明します。
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: article
ms.date: 03/16/2018
ms.author: anroth
ms.openlocfilehash: 44666d5d7f2a51e4017c704205d21b1f6d06908c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/23/2018
ms.locfileid: "35377632"
---
# <a name="limits-and-quotas"></a>制限とクォータ

Custom Vision Service のキーには 3 つの層があります。 F0 リソースと S0 リソースは Azure Portal 経由で取得されます。 価格設定とトランザクション定義の詳細は[価格設定ページ](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)にあります。  F0 プロジェクトは S0 プロジェクトにアップグレードできます。

期間限定試用版プロジェクト リソースは Custom Vision ログイン (つまり、AAD アカウントまたは MSA アカウント) に付属しています。これはサービスを短い期間試すことを意図するものです。  Azure プレビュー (2018 年 3 月 1 日) より前の早期無料プレビュー中に作成されたアカウントは、期間限定試用版の前のクォータを保持します。 

||**期間限定試用版**|**F0 (Azure)**|**S0 (Azure)**|
|-----|-----|-----|-----|
|プロジェクト|2|2|100|
|プロジェクトあたりのトレーニング画像|5,000|5,000|50,000|
|予測/月|10,000 |10,000|無制限|
|タグ/プロジェクト|50|50|250|
|反復回数 |10|10|10|
|タグあたりの最小ラベル付き画像、分類 (50+ 推奨) |5|5|5|
|タグあたりの最小ラベル付き画像、物体検出 (50+ 推奨)|15|15|15|
|予測画像の保存期間|30 日|30 日|30 日|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージあり (秒あたりトランザクション)|2|2|10|
|[予測](https://go.microsoft.com/fwlink/?linkid=865445)操作、ストレージなし (秒あたりトランザクション)|2|2|20|
|[TrainProject](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたり API 呼び出し)|2|2|10|
|[その他の API 呼び出し](https://go.microsoft.com/fwlink/?linkid=865446) (秒あたりトランザクション)|10|10|10|
|最大画像サイズ (トレーニング画像アップロード) |6 MB|6 MB|6 MB|
|最大画像サイズ (予測)|4 MB|4 MB|4 MB|

*プロジェクトあたりトレーニング画像数*と*プロジェクトあたりタグ数*の制限は、S0 プロジェクトの場合、時間の経過と共に増えることが予想されます。 
