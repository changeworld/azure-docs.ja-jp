---
title: クイック スタート:REST API で意図を取得する - LUIS
description: この REST API のクイックスタートでは、利用可能なパブリック LUIS アプリを使用して、会話形式のテキストからユーザーの意図を判断します。
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: d03ebc1ec5730f1905ce1ee5b9c484312f5aeee1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316351"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>クイック スタート:REST API で意図を取得する

このクイック スタートでは、LUIS アプリを使用し、会話形式のテキストからユーザーの意図を判断します。 Pizza アプリの HTTP 予測エンドポイントにユーザーの意図をテキストとして送信します。 エンドポイントでは、LUIS によって Pizza アプリのモデルが適用されます。これにより自然言語テキストの意味が分析され、全体的な意図が特定されて、アプリのサブジェクト ドメインに関連したデータが抽出されます。

この記事には、無料の [LUIS](https://www.luis.ai) アカウントが必要です。

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end
