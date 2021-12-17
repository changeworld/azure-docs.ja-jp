---
title: Azure Cognitive Service for Language のキー フレーズ抽出とは
titleSuffix: Azure Cognitive Services
description: 非構造化テキストの主な概念を特定するのに役立つ、Azure Cognitive Services のキー フレーズ抽出の概要。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-key-phrase, ignite-fall-2021
ms.openlocfilehash: ae7ac89fd1607d49c61421b97586a85d9d7ec945
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488246"
---
# <a name="what-is-key-phrase-extraction-in-azure-cognitive-service-for-language"></a>Azure Cognitive Service for Language のキー フレーズ抽出とは

キー フレーズ抽出は、[Azure Cognitive Service for Language](../overview.md) に用意されている機能の 1 つであり、書き表された言語が関係するインテリジェントなアプリケーションを開発するためのクラウド内の機械学習と AI アルゴリズムのコレクションです。 キー フレーズ抽出を使用して、テキスト内の主要な概念をすばやく特定します。 たとえば、「*食べ物はおいしくて、スタッフがすばらしかった*」というテキストの場合、キー フレーズ抽出から主なトピックとして "*食べ物*" と "*すばらしいスタッフ*" が返されます。

このドキュメントには、次のような記事が記載されています。

* [**クイックスタート**](quickstart.md) は、サービスへの要求の実行方法を説明する概要手順です。
* [**攻略ガイド**](how-to/call-api.md)には、より具体的またはカスタマイズした方法でサービスを使用するための手順が記載されています。

[!INCLUDE [Typical workflow for pre-configured language features](../includes/overview-typical-workflow.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Docker コンテナーを使用してオンプレミスにデプロイする

利用できる Docker コンテナーを使って、[この機能をオンプレミスに展開](how-to/use-containers.md)します。 これらの Docker コンテナーを使用すると、コンプライアンス、セキュリティ、またはその他の運用上の理由により、データにいっそう近いところにサービスを持ってくることができます。

## <a name="responsible-ai"></a>責任ある AI 

AI システムには、テクノロジだけでなく、それを使用する人、それによって影響を受ける人、それが展開される環境も含まれています。 システムでの責任ある AI の使用と展開については、[キー フレーズ抽出の透過性のためのメモ](/legal/cognitive-services/language-service/transparency-note-key-phrase-extraction?context=/azure/cognitive-services/language-service/context/context)の記事を参照してください。 詳細については、次の記事も参照してください。

[!INCLUDE [Responsible AI links](../includes/overview-responsible-ai-links.md)]

## <a name="next-steps"></a>次のステップ

エンティティ リンク設定機能を使い始めるには、2 つの方法があります。
* [Language Studio](../language-studio.md)。コードを記述することなく Azure Cognitive Service for Language の複数の機能を試すことができる Web ベースのプラットフォームです。
* [クイックスタート記事](quickstart.md)。REST API とクライアント ライブラリ SDK を使用してサービス要求を行う方法が記載されています。  
