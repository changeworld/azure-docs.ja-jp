---
title: Form Recognizer クライアント ライブラリ SDK または REST API を使用する
titleSuffix: Azure Applied AI Services
description: Form Recognizer クライアント ライブラリ SDK または REST API を使用して、カスタム ドキュメントからキーと値のペアとテーブル データを抽出するフォーム処理アプリを作成します。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 11/02/2021
ms.author: lajanuar
zone_pivot_groups: programming-languages-set-formre
recommendations: false
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0402041121e34902d9801307dad8a759f8fe9516
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089909"
---
# <a name="use-form-recognizer-sdks-or-rest-api"></a>Form Recognizer SDK または REST API を使用する

 この攻略ガイドでは、SDK、選択したプログラミング言語、または REST API を使用して、アプリケーションとワークフローに Form Recognizer を追加する方法について説明します。 Azure Form Recognizer は、機械学習を使用してドキュメントからフォーム フィールド、テキスト、テーブルを抽出して分析するクラウドベースの Azure Applied AI Service です。 テクノロジを学習している場合は、無料のサービスを使用することをお勧めします。 無料のページは 1 か月あたり 500 ページに制限されていることに注意してください。

次の API を使用して、フォームとドキュメントから構造化データを抽出します。

* [クライアントを認証する](#authenticate-the-client)
* [Analyze Layout](#analyze-layout)
* [領収書を分析する](#analyze-receipts)
* [名刺を分析する](#analyze-business-cards)
* [請求書を分析する](#analyze-invoices)
* [身分証明書を分析する](#analyze-id-documents)
* [カスタム モデルをトレーニングする](#train-a-custom-model)
* [カスタム モデルを使用してフォームを分析する](#analyze-forms-with-a-custom-model)
* [カスタム モデルを管理する](#manage-custom-models)

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](../includes/how-to-guides/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](../includes/how-to-guides/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](../includes/how-to-guides/javascript-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](../includes/how-to-guides/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/how-to-guides/rest-api.md)]

::: zone-end
