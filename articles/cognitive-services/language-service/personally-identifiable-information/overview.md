---
title: Azure Cognitive Service for Language の個人を特定できる情報 (PII) の検出機能とは
titleSuffix: Azure Cognitive Services
description: はテキスト内のエンティティや機密情報 (PII) の抽出するのに役立つ、Azure Cognitive Services の PII 検出機能の概要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-pii, ignite-fall-2021
ms.openlocfilehash: f78a0caf3c53e44e1465a9bd5c81f2b414d6aa15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092557"
---
# <a name="what-is-personally-identifiable-information-pii-detection-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language の個人を特定できる情報 (PII) の検出とは

PII の検出は、[Azure Cognitive Service for Language](../overview.md) に用意されている機能の 1 つであり、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウド内の機械学習と AI アルゴリズムのコレクションです。 PII 検出機能を使用すると、非構造化テキストに含まれる機密情報を特定、分類、編集することができます。 たとえば、電話番号、メール アドレス、身分証明書のフォームなどです。 

* [**クイックスタート**](quickstart.md)は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to-call.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。
* [**概念の記事**](concepts/entity-categories.md)では、このサービスの機能と特長について詳しく説明します。


[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使用する人、それによって影響を受ける人、それが展開される環境も含まれています。 システムでの責任ある AI の使用と展開については、[PII の透過性のためのメモ](/legal/cognitive-services/language-service/transparency-note-personally-identifiable-information?context=/azure/cognitive-services/language-service/context/context)の記事を参照してください。 詳細については、次の記事も参照してください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
