---
title: 開発者ポータルに Google タグマネージャを統合する
titleSuffix: Azure API Management
description: Google タグマネージャを Azure API Management のマネージドまたはセルフホステッドの開発者ポータルに接続する方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: a3436727470ceb073f5431ef9677fbc17afdda84
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601674"
---
# <a name="integrate-google-tag-manager-to-api-management-developer-portal"></a>API Management 開発者ポータルに Google タグマネージャを統合する

[Google タグマネージャ](https://developers.google.com/tag-manager)は、Google によって作成されたタグ管理システムです。 これを使用して、Web サイトでの追跡と分析に使用される JavaScript と HTML のタグを管理できます。 たとえば、Google タグマネージャを使用して、Google アナリティクス、ヒートマップ、または LiveChat などのチャットボットを統合できます。

この記事の手順に従って、Google タグマネージャを Azure API Management のマネージドまたはセルフホステッドの開発者ポータルに接続します。

## <a name="add-google-tag-manager-to-your-portal"></a>Google タグマネージャをポータルに追加する

マネージドまたはセルフホステッドの開発者ポータルに Google タグマネージャを接続するには、これらの手順に従います。

> [!IMPORTANT]
> 手順 1 と 2 はマネージド ポータルには必要ありません。 マネージド ポータルを使用する場合は手順 4 に進んでください。

1. 開発者ポータルの最新リリース用に[ローカル環境](developer-portal-self-host.md#step-1-set-up-local-environment)を設定します。

1. **npm** パッケージをインストールして、[Paperbits for Google Tag Manager](https://github.com/paperbits/paperbits-gtm) を追加します。

    ```sh
    npm install @paperbits/gtm --save
    ```

1. `src` フォルダー内の `startup.publish.ts` ファイルで、GTM モジュールをインポートして登録します。

    ```typescript
    import { GoogleTagManagerPublishModule } from "@paperbits/gtm/gtm.publish.module";
    ...
    injector.bindModule(new GoogleTagManagerPublishModule());
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

1. Google タグマネージャ構成を使用して、前の手順のサイト構成を拡張します。

    ```http
    PUT /contentTypes/document/contentItems/configuration
    ```

    ```json
    {
        "nodes": [
            {
                "site": { ... },
                "integration": {
                    "googleTagManager": {
                        "containerId": "GTM-..."
                    }
                }
            }
        ]
    }
    ```

## <a name="next-steps"></a>次のステップ

- [ポータルのデプロイを自動化する](automate-portal-deployments.md)
- [開発者ポータルのセルフホスト](developer-portal-self-host.md)