---
title: Azure Cognitive Service for Language のエンティティ リンク設定とは
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services でのエンティティ リンク設定の概要。これは、テキストからエンティティを抽出したり、オンライン ナレッジ ベースへのリンクを提供したりするのに役立ちます。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-entity-linking, ignite-fall-2021
ms.openlocfilehash: bbd4483702055a4bebb05251606fb705b1e0deb2
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487469"
---
# <a name="what-is-entity-linking-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language のエンティティ リンク設定とは

エンティティ リンク設定は、[Azure Cognitive Service for Language](../overview.md) に用意されている機能の 1 つであり、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウド内の機械学習と AI アルゴリズムのコレクションです。 エンティティ リンク設定は、テキストで見つかったエンティティの id を識別し、あいまいさを解消します。 たとえば、"*先週シアトルに出かけました。* " という文では、"*Seattle*" という単語が識別され、Wikipedia の詳細情報へのリンクが示されます。

このドキュメントには、次のような記事が記載されています。

* [**クイックスタート**](quickstart.md) は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to/call-api.md)には、より具体的な方法でサービスを使用するための手順が記載されています。

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

結果として、テキスト内の認識されたエンティティのコレクションと、オンライン ナレッジ ベースとしての Wikipedia の URL が得られます。 

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使う人、それによって影響を受ける人、それがデプロイされる環境も含まれています。 システムでの責任ある AI の使用とデプロイについては、[エンティティ リンク設定の透明性に関する注意](/legal/cognitive-services/language-service/transparency-note?context=/azure/cognitive-services/language-service/context/context)の記事を参照してください。 詳細については、次の記事も参照してください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
