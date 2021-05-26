---
title: 'クイックスタート: Form Recognizer クライアント ライブラリまたは REST API'
titleSuffix: Azure Applied AI Services
description: Form Recognizer クライアント ライブラリまたは REST API を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 04/14/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
ms.custom: devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
keywords: フォーム処理, 自動データ処理
ms.openlocfilehash: 34b2e591c8d050a168e82bb40c5167823c136e52
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374161"
---
# <a name="quickstart-use-the-form-recognizer-client-library-or-rest-api"></a>クイックスタート: Form Recognizer クライアント ライブラリまたは REST API を使用する

好みの開発言語で Form Recognizer の使用を開始しましょう。 Azure Form Recognizer は、機械学習テクノロジを利用して自動データ処理ソフトウェアを構築ことを可能にするコグニティブ サービスです。 フォーム ドキュメントから、テキスト、キーと値のペア、選択マーク、テーブル データなどを特定して抽出します。このサービスによって、元のファイルにおけるリレーションシップを含む構造化データが出力されます。 Form Recognizer は、REST API または SDK を介して使用できます。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

次の API を使用して、フォームとドキュメントから構造化データを抽出します。

|Name |Description |
|---|---|
| **[レイアウトを分析する](#analyze-layout)** | ストリームとして渡されたドキュメントを分析して、ドキュメントからテキスト、選択マーク、テーブル、および構造を抽出します。 |
| **[領収書を分析する](#analyze-receipts)** | レシート ドキュメントを分析して、主要な情報と、レシートのその他のテキストを抽出します。|
| **[名刺を分析する](#analyze-business-cards)** | 名刺を分析し、主要な情報とテキストを抽出します。|
| **[請求書を分析する](#analyze-invoices)** | 請求書を分析して、主要な情報、テーブル、およびその他の請求書テキストを抽出します。|
| **[身分証明書を分析する](#analyze-identity-documents)** | 身分証明書を分析して、主要な情報やその他の ID カード テキストを抽出します。|
| **[カスタム モデルをトレーニングする](#train-a-custom-model)**| 同じ種類の 5 つのフォームを使用して、フォームを分析する新しいモデルをトレーニングします。 手動でラベル付けしたデータを使ってトレーニングを行うには、_useLabelFile_ パラメーターを `true` に設定します。 |
| **[カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)**|ストリームとして渡されたフォームを分析して、カスタム モデルを使用してフォームからテキスト、キーと値のペア、およびテーブルを抽出します。  |
|**[カスタム モデルを管理する](#manage-custom-models)**| Form Recognizer アカウントのカスタム モデルの数を確認し、その ID を使用して特定のモデルを取得し、アカウントからモデルを削除することができます。|

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
