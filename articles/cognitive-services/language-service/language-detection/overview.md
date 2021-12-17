---
title: Azure Cognitive Service for Language での言語検出とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services での言語検出の概要。これは、テキストが書かれている言語のコードを取得することで言語を検出するのに役立ちます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: sample
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-language-detection, ignite-fall-2021
ms.openlocfilehash: 5ca0304ca413e17a70d39641f145272a2c58a792
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092564"
---
# <a name="what-is-language-detection-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language での言語検出とは

言語の検出は、[Azure Cognitive Service for Language](../overview.md) で提供される機能の 1 つで、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウドでの機械学習と AI アルゴリズムのコレクションです。 言語検出を使用すると、ドキュメントを書くのに使われている言語を検出でき、広範な言語、バリエーション、方言、一部の地域や文化で使われる言語の言語コードが返されます。 

このドキュメントには、次のような記事が記載されています。

* [**クイックスタート**](quickstart.md) は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to/call-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

利用できる Docker コンテナーを使って、[この機能をオンプレミスに展開](how-to/use-containers.md)します。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにいっそう近いところにサービスを持ってくることができます。

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使う人、それによって影響を受ける人、それが展開される環境も含まれています。 システムでの責任ある AI の使用と展開については、「[言語検出の透明性に関する注意](/legal/cognitive-services/language-service/transparency-note-language-detection?context=/azure/cognitive-services/language-service/context/context)」をお読みください。 詳細については、次の記事もご覧ください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
