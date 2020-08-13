---
title: クイック スタート:SDK を使用して学習ループを作成および使用する - Personalizer
description: このクイックスタートでは、クライアント SDK を使用してナレッジ ベースを作成および管理する方法について説明します。
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 480e860952c2659d053f8a5659e312feacff2d2b
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874488"
---
# <a name="quickstart-personalizer-client-library"></a>クイック スタート:Personalizer クライアント ライブラリ

Personalizer サービスを使用するこのクイックスタートでパーソナライズしたコンテンツを表示します。

Personalizer クライアント ライブラリの概要 以下の手順に従って、パッケージをインストールし、基本タスクのコード例を試してみましょう。

 * Rank API - コンテンツとコンテキストに関して指定したリアルタイム情報に基づき、最も適切な項目をアクションから選択します。
 * Reward API - ビジネス ニーズに基づいて報酬スコアを決定し、この API を使用してこれを Personalizer に送信します。 このスコアは、適切な場合は 1、不適切な場合は 0 というように 1 つの値にすることができます。また、ビジネス ニーズに基づいてアルゴリズムを作成することもできます。

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
>[Personalizer のしくみ](how-personalizer-works.md)

* [Personalizer とは](what-is-personalizer.md)
* [Personalizer を使用できる状況](where-can-you-use-personalizer.md)
* [トラブルシューティング](troubleshooting.md)
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py) にあります。
