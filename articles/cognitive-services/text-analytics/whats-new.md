---
title: Text Analytics API の新機能
titlesuffix: Text Analytics - Azure Cognitive Services
description: Text Analytics サービスで行われた新しい開発について説明します
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: aahi
ms.openlocfilehash: 44ef6fb118be4d1110a693faded6c57bc8b4e2fd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499950"
---
# <a name="whats-new-in-the-text-analytics-api"></a>Text Analytics API の新機能

Text Analytics API は継続的に更新されます。 常に最新の開発情報を把握していただけるよう、この記事では新しいリリースと機能に関する情報を提供します。

## <a name="named-entity-recognition-v3-public-preview---october-2019"></a>名前付きエンティティの認識 v3 パブリック プレビュー - 2019 年 10 月

次のバージョンの名前付きエンティティの認識 (NER) が、パブリック プレビューで使用できるようになりました。テキスト内のエンティティの検出と分類が拡張されています。 共有サービスには次のものが含まれています。

* 次の新しいエンティティ型の認識:
    * 電話番号
    * IP アドレス

* 個人情報エンティティ型の認識に対する新しいエンドポイント (英語のみ)
* エンティティの認識とエンティティのリンクに対する個別のエンドポイント。

エンティティのリンクでは、英語とスペイン語がサポートされています。 NER の言語サポートは、エンティティ型によって異なります。 詳細については、次のリンクを参照してください。 

> [!div class="nextstepaction"]
> [名前付きエンティティの認識 v3 に関する詳細](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-v3-public-preview)

## <a name="sentiment-analysis-v3-public-preview---october-2019"></a>感情分析 v3 のパブリック プレビュー - 2019 年 10 月

現在、感情分析の次期バージョンはパブリック プレビューで利用できます。API のテキスト分類とスコア付けの精度と詳細が向上しました。 また、次の機能も提供されます。

* テキスト内のさまざまなセンチメントに対する自動ラベル付け。
* ドキュメントおよび文レベルでの感情分析と出力。 

英語 (`en`)、日本語 (`ja`)、簡体中国語 (`zh-Hans`)、繁体中国語 (`zh-Hant`)、フランス語 (`fr`)、イタリア語 (`it`)、スペイン語 (`es`)、オランダ語 (`nl`)、ポルトガル語 (`pt`)、ドイツ語 (`de`) がサポートされており、次のリージョンで利用できます: `Australia East`、`Central Canada`、`Central US`、`East Asia`、`East US`、`East US 2`、`North Europe`、`Southeast Asia`、`South Central US`、`UK South`、`West Europe`、`West US 2`。

> [!div class="nextstepaction"]
> [感情分析 v3 の詳細情報](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-v3-public-preview)

## <a name="next-steps"></a>次の手順

* [Text Analytics API とは](overview.md)  
* [ユーザー シナリオの例](text-analytics-user-scenarios.md)
* [感情分析](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [言語検出](how-tos/text-analytics-how-to-language-detection.md)
* [エンティティの認識](how-tos/text-analytics-how-to-entity-linking.md)
* [キー フレーズ抽出](how-tos/text-analytics-how-to-keyword-extraction.md)
