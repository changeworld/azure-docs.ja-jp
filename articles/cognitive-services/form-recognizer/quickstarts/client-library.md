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
zone_pivot_groups: programming-languages-set-ten
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: フォーム処理, 自動データ処理
ms.openlocfilehash: eb3fc3ebe5c3d9f659758a14ba620018671d95f5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318916"
---
# <a name="quickstart-use-the-form-recognizer-client-library"></a>クイック スタート:Form Recognizer クライアント ライブラリを使用する

自分で選択した言語で Form Recognizer クライアント ライブラリの使用を開始します。 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 テキスト、キーと値のペア、テーブル データを特定し、form ドキュメントから抽出します。&mdash;元のファイルにおける関係を含む構造化データがこのサービスにより出力されます。 以下の手順に従って、SDK パッケージをインストールし、基本タスクのコード例を試してみましょう。

Form Recognizer クライアント ライブラリを使用して、次のことを行います。

* [フォーム コンテンツを認識する](#recognize-form-content)
* [領収書を認識する](#recognize-receipts)
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