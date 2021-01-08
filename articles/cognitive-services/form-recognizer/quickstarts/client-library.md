---
title: クイックスタート:Form Recognizer クライアント ライブラリ
titleSuffix: Azure Cognitive Services
description: Form Recognizer クライアント ライブラリを使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 09/21/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: フォーム処理, 自動データ処理
ms.openlocfilehash: f01adc472f94d679366af50c136ddc020b79b811
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808574"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>クイック スタート:Form Recognizer クライアント ライブラリを使用する

好みの言語で Form Recognizer の使用を開始しましょう。 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 テキスト、キーと値のペア、テーブル データを特定し、form ドキュメントから抽出します。&mdash;元のファイルにおける関係を含む構造化データがこのサービスにより出力されます。 以下の手順に従って、SDK パッケージをインストールし、基本タスクのコード例を試してみましょう。 Form Recognizer クライアント ライブラリは現在、From Recognizer サービスの v2.0 を対象としています。

Form Recognizer クライアント ライブラリを使用して、次のことを行います。

* [フォーム コンテンツを認識する](#recognize-form-content)
* [領収書を認識する](#recognize-receipts)
* [名刺を認識する](#recognize-business-cards)
* [請求書を認識する](#recognize-invoices)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-your-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/quickstarts/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end