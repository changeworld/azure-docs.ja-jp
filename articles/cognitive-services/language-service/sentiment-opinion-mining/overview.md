---
title: Azure Cognitive Service for Language の感情分析とオピニオン マイニングとは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services の感情分析機能の概要。テキストをマイニングして手がかりを得ることで、人々があるトピックについてどう考えているかを把握することができます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-sentiment-opinion-mining, ignite-fall-2021
ms.openlocfilehash: 8807b12f9920fd62901aebed862ecba483e77bb0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092102"
---
# <a name="what-is-sentiment-analysis-and-opinion-mining-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language の感情分析とオピニオン マイニングとは

感情分析とオピニオン マイニングは、[Azure Cognitive Service for Language](../overview.md) に用意されている機能であり、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウドでの機械学習と AI アルゴリズムのコレクションです。 これらの機能を使用すると、テキストをマイニングして正または負の感情に関する手がかりを得ることで、人々がブランドやトピックについてどう考えているかを知ることができます。また、テキストの特定の側面と関連付けることができます。 

* [**クイックスタート**](quickstart.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to/call-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

## <a name="sentiment-analysis"></a>センチメント分析 

感情分析機能には、文章とドキュメントレベルでサービスによって検出された最も高い信頼度スコアに基づいて、感情ラベル ("negative"、"neutral"、"positive" など) が用意されています。 また、この機能は、positive、neutral、negative (肯定的、中立的、否定的) のセンチメントに関し、各ドキュメントとその中の文章に対して 0 と 1 の間の信頼度スコアを返します。 

### <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

使用できる Docker コンテナーを使用して、[オンプレミスで感情分析をデプロイします](how-to/use-containers.md)。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにいっそう近いところにサービスを持ってくることができます。

## <a name="opinion-mining"></a>意見マイニング

オピニオン マイニングは、感情分析の機能の 1 つです。 この機能は、自然言語処理 (NLP) ではアスペクトベースの感情分析とも呼ばれます。テキストに含まれる単語 (製品やサービスの属性など) に関連した意見について、より粒度の細かい情報が得られます。

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使用する人、それによって影響を受ける人、それが展開される環境も含まれています。 システムでの責任ある AI の使用とデプロイについては、[感情分析の透過性のためのメモ](/legal/cognitive-services/language-service/transparency-note-sentiment-analysis?context=/azure/cognitive-services/language-service/context/context)の記事を参照してください。 詳細については、次の記事も参照してください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
