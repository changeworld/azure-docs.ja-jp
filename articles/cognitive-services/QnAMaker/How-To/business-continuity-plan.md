---
title: ビジネス継続性計画 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: f920a789f1ec631c5d70863b10d8364b3eb81b6c
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650471"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>QnA Maker サービスのビジネス継続性計画を作成する

ビジネス継続性計画の主な目的は、回復性に優れたナレッジ ベース エンドポイントを作成し、そのナレッジ ベース エンドポイントを使用するボットまたはアプリケーションにダウン タイムが発生しないようにすることです。

![QnA Maker の BCP 計画](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上に示した概念の説明は次のとおりです。

1. [ペアになっている Azure リージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)に、同一の [QnA Maker サービス](../How-To/set-up-qnamaker-service-azure.md)を 1 つずつ、計 2 つ設定します。

2. プライマリとセカンダリの Azure 検索インデックスを常に同期しておきます。Azure のインデックスをバックアップおよび復元する方法については、[GitHub](https://github.com/pchoudhari/QnAMakerBackupRestore) でサンプルを参照してください。

3. [連続エクスポート](https://docs.microsoft.com/azure/application-insights/app-insights-export-telemetry)を使って Application Insights をバックアップします。

4. プライマリとセカンダリのスタックを設定したら、[トラフィック マネージャー](https://docs.microsoft.com/azure/traffic-manager/)を使用して 2 つのエンドポイントを構成し、ルーティング方法を設定します。

5. トラフィック マネージャー エンドポイントの Secure Sockets Layer (SSL) 証明書を作成する必要があります。 アプリ サービスで [SSL 証明書](https://docs.microsoft.com/azure/app-service/configure-ssl-bindings)をバインドします。

6. 最後に、ボットまたはアプリでトラフィック マネージャー エンドポイントを使用します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [容量の選択](./improve-knowledge-base.md)
