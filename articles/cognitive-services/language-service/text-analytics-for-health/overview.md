---
title: Azure Cognitive Service for Language での Text Analytics for Health とは
titleSuffix: Azure Cognitive Services
description: 臨床ドキュメントなどの非構造化テキストから医療情報を抽出するのに役立つ Azure Cognitive Services での Text Analytics for Health の概要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-health, ignite-fall-2021
ms.openlocfilehash: 609d60d2094372cd5c1e476afd3abde1df7f68af
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131090080"
---
# <a name="what-is-text-analytics-for-health-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language での Text Analytics for Health とは

Text Analytics for Health は、[Azure Cognitive Service for Language](../overview.md) で提供される機能の 1 つで、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウドでの機械学習と AI アルゴリズムのコレクションです。 

Text Analytics for Health により、医師のメモ、退院要約、臨床ドキュメント、電子健康記録などの非構造化テキストから関連する医療情報が抽出されて、ラベルが付けられます。

このドキュメントには、次のような記事が記載されています。

* [**クイックスタート**](quickstart.md) は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to/call-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [**概念に関する記事**](concepts/health-entity-categories.md)では、サービスの機能と特長が詳しく説明されています。

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Introducing-Text-Analytics-for-Health/player]

## <a name="features"></a>特徴

[!INCLUDE [Text Analytics for health](includes/features.md)]

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

利用できる Docker コンテナーを使って、[この機能をオンプレミスに展開](how-to/use-containers.md)します。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにいっそう近いところにサービスを持ってくることができます。

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使う人、それによって影響を受ける人、それが展開される環境も含まれています。 システムでの責任ある AI の使用と展開については、「[Text Analytics for Health の透明性に関する注意](/legal/cognitive-services/language-service/transparency-note-health?context=/azure/cognitive-services/language-service/context/context)」をお読みください。 詳細については、次の記事もご覧ください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
