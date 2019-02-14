---
title: データをエクスポートまたは削除する Custom Vision Service
titlesuffix: Azure Cognitive Services
description: Custom Vision Service のデータをエクスポートまたは削除する方法について説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 05/25/2018
ms.author: pafarley
ms.openlocfilehash: 7f27170a5e97d7860bcb059f16273b38757de747
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55867037"
---
# <a name="export-or-delete-user-data-in-custom-vision"></a>Custom Vision のユーザー データをエクスポートまたは削除する

Content Moderator は、サービスの運用にユーザー データを収集しますが、データの参照、エクスポート、削除は、ユーザーが Custom Vision Service [Training API](https://go.microsoft.com/fwlink/?linkid=865446) を使用して完全に制御できます。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Custom Vision のユーザー データをエクスポートおよび削除する方法については、次の表を参照してください。

| データ | エクスポート操作 | 削除操作。 |
| ---- | ---------------- | ---------------- |
| アカウントの情報 (サブスクリプション キー) | [GetAccountInfo](https://go.microsoft.com/fwlink/?linkid=865446) | Azure Portal を使用して削除します (Azure サブスクリプション)。 または、CustomVision.ai 設定ページ (Microsoft アカウント サブスクリプション) の [アカウントの削除] ボタンを使用します。 | 
| イテレーションの詳細 | [GetIteration](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| イテレーションのパフォーマンスの詳細 | [GetIterationPerformance](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) | 
| イテレーションの一覧 | [GetIterations](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteIteration](https://go.microsoft.com/fwlink/?linkid=865446) |
| プロジェクトおよびプロジェクトの詳細 | [GetProject](https://go.microsoft.com/fwlink/?linkid=865446) および [GetProjects](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteProject](https://go.microsoft.com/fwlink/?linkid=865446) | 
| 画像タグ | [GetTag](https://go.microsoft.com/fwlink/?linkid=865446) および [GetTags](https://go.microsoft.com/fwlink/?linkid=865446) | [DeleteTag](https://go.microsoft.com/fwlink/?linkid=865446) | 
| イメージ | [GetTaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (画像のダウンロードに uri を提供) と [GetUntaggedImages](https://go.microsoft.com/fwlink/?linkid=865446) (画像のダウンロードに uri を提供) | [DeleteImages](https://go.microsoft.com/fwlink/?linkid=865446) | 
| エクスポートされたモデル | [GetExports](https://go.microsoft.com/fwlink/?linkid=865446) | アカウントの削除時に削除 | 
