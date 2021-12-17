---
title: Conversational Language Understanding の制限
titleSuffix: Azure Cognitive Services
description: Conversational Language Understanding に関するデータ、リージョン、スループットの制限について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: ec9a32c4f73e174798a3422a1c152f3972bd076f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091510"
---
# <a name="service-limits-for-conversational-language-understanding"></a>Conversational Language Understanding のサービスの制限

Conversational Language Understanding サービスに関するデータ、リージョン、スループットの制限について説明します

## <a name="region-limits"></a>リージョンの制限

- Conversational Language Understanding は、**米国西部 2** と **西ヨーロッパ** の 2 つのリージョンでのみ利用できます。 
    - 西ヨーロッパでは、オーケストレーション ワークフロー プロジェクトを使って、**会話プロジェクト**、**QnA Maker**、**LUIS** を接続できます。
    - 米国西部 2 では、オーケストレーション ワークフロー プロジェクトを使って、**会話プロジェクト** と **QnA Maker** だけを接続できます。 LUIS 用の米国西部 2 オーサリング リージョンはありません。 
- CLU にアクセスするために使用できる SKU は、**S** SKU を使う **言語** リソースのみです。

## <a name="data-limits"></a>データ制限

Conversational Language Understanding サービスには次の制限があります。

|Item|制限|
| --- | --- |
|発話|プロジェクトあたり 15,000*|
|意図|プロジェクトあたり 500|
|エンティティ|プロジェクトあたり 100|
|発話の長さ|500 文字|
|意図とエンティティ名の長さ|50 文字|
|モデル|プロジェクトあたり 10|
|プロジェクト|リソースあたり 500|
|Synonyms|リスト コンポーネントあたり 20,000|

\**ユーザーによって追加された発話のみが含まれます。オーケストレーション ワークフロー プロジェクト用にプルされたデータは、合計にカウントされません。*


## <a name="throughput-limits"></a>スループットの制限

|Item | 制限 |
--- | --- 
|呼び出しの作成| 1 分あたり 60 要求|
|予測呼び出し| 1 分あたり 60 要求|

## <a name="quota-limits"></a>クォータ制限

| Item | 制限 |
--- | --- 
|呼び出しの作成| 無制限|
|予測呼び出し| 1 か月あたり 100,000|

## <a name="next-steps"></a>次のステップ

[Conversational Language Understanding の概要](overview.md)
