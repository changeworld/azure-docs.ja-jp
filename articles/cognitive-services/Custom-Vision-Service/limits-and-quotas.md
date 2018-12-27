---
title: 価格と制限 - Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Custom Vision Service の制限とクォータについて説明します。
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: custom-vision
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: anroth
ms.openlocfilehash: d0a6b7be62afcd8e9f51b6a9387c9aa33f62d9a9
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49430070"
---
# <a name="pricing-and-limits"></a>料金と制限

Custom Vision Service のキーには 3 つのレベルがあります。 期間限定試用版プロジェクト リソースは Custom Vision ログイン (つまり、Azure Active Directory アカウントまたは MSA アカウント) に付属しています。 これはサービスを短い期間試すことを意図するものです。 Azure portal を通じて F0 (Free) または S0 (Standard) サブスクリプションにサインアップすることができます。 価格とトランザクションについて詳しくは、対応する [Cognitive Services の価格ページ](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)をご覧ください。

Azure プレビュー (2018 年 3 月 1 日) より前の早期無料プレビュー中に作成されたアカウントは、期間限定試用版の前のクォータを保持します。 

プロジェクトあたりトレーニング画像数とプロジェクトあたりタグ数は、S0 プロジェクトの場合、時間の経過と共に増えることが予想されます。

||**期間限定試用版**|**F0**|**S0**|
|-----|-----|-----|-----|
|プロジェクト|2|2|100|
|プロジェクトごとのトレーニング画像、分類|5,000|5,000|50,000|
|プロジェクトごとのトレーニング画像、オブジェクトの検出|5,000|5,000|10,000|
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


