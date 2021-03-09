---
title: 'クイックスタート: Form Recognizer クライアント ライブラリまたは REST API'
titleSuffix: Azure Cognitive Services
description: Form Recognizer クライアント ライブラリまたは REST API を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: フォーム処理, 自動データ処理
ms.openlocfilehash: d468f40d9de7fd7efb4ac7d8021a667e0c6c31f6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584610"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>クイックスタート: Form Recognizer クライアント ライブラリまたは REST API を使用する

好みの開発言語で Form Recognizer の使用を開始しましょう。 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 フォーム ドキュメントから、テキスト、キーと値のペア、選択マーク、テーブル データなどを特定して抽出します。このサービスによって、元のファイルにおけるリレーションシップを含む構造化データが出力されます。 Form Recognizer は、REST API または SDK を介して使用できます。 以下の手順に従って、SDK パッケージをインストールし、基本タスクのコード例を試してみましょう。 

Form Recognizer の用途:

* [Analyze Layout](#analyze-layout)
* [請求書を分析する](#analyze-invoices)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [領収書を分析する](#analyze-receipts)
* [名刺を分析する](#analyze-business-cards)
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
