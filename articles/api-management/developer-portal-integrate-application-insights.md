---
title: 開発者ポータルに Application Insights を統合する
titleSuffix: Azure API Management
description: マネージドまたはセルフホステッドの開発者ポータルに Application Insights を統合する方法について説明します。
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 5e1c9caa55d0b3b7820f766a30c878fdc01f5137
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741395"
---
# <a name="integrate-application-insights-to-developer-portal"></a>開発者ポータルに Application Insights を統合する

Azure Monitor のよく使用されている機能の 1 つに Application Insights があります。 これは開発者と DevOps プロフェッショナル向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 これを使用して、開発者ポータルを監視し、パフォーマンスの異常を検出します。 Application Insights には、ユーザーが開発者ポータルにアクセスしているときに実際に何をしているかを把握するのに役立つ強力な分析ツールが含まれています。

## <a name="add-application-insights-to-your-portal"></a>Application Insights をポータルに追加する

マネージドまたはセルフホステッドの開発者ポータルに Application Insights を接続するには、これらの手順に従います。

> [!IMPORTANT]
> 手順 1 と 2 はマネージド ポータルには必要ありません。 マネージド ポータルを使用する場合は手順 4 に進んでください。

1. 開発者ポータルの最新リリース用に[ローカル環境](developer-portal-self-host.md#step-1-set-up-local-environment)を設定します。

1. **npm** パッケージをインストールして、[Paperbits for Azure](https://github.com/paperbits/paperbits-azure) を追加します。

    ```console
    npm install @paperbits/azure --save
    ```

1. `src` フォルダー内の`startup.publish.ts` ファイルで、Application Insights モジュールをインポートして登録します。

    ```typescript
    import { AppInsightsPublishModule } from "@paperbits/azure";
    ...
    injector.bindModule(new AppInsightsPublishModule());
    ```

1. ポータルの構成を取得します。

    ```http
    GET /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": {
                    "title": "Microsoft Azure API Management - developer portal",
                    "description": "Discover APIs, learn how to use them, try them out interactively, and sign up to acquire keys.",
                    "keywords": "Azure, API Management, API, developer",
                    "faviconSourceId": null,
                    "author": "Microsoft Azure API Management"
                }
            }
        ]
    }
    ```

1. Application Insights 構成を使用して、前の手順のサイト構成を拡張します。

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "appInsights": {
                        "instrumentationKey": "xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxxxxxxx"
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>次の手順

開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)
- [ポータルのデプロイを自動化する](automate-portal-deployments.md)
- [開発者ポータルのセルフホスト](developer-portal-self-host.md)
