---
title: Azure Functions による Azure Static Web Apps での API のサポート
description: Azure Static Web Apps でサポートされる API の機能について説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 11/08/2021
ms.author: cshoe
ms.openlocfilehash: b7ab83ddf6d3f20c28d030d48b50ebb88d6676ae
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027472"
---
# <a name="api-support-in-azure-static-web-apps-with-azure-functions"></a>Azure Functions による Azure Static Web Apps での API のサポート

Azure Static Web Apps では、[Azure Functions](../azure-functions/functions-overview.md) を介してサーバーレス API エンドポイントが提供されています。 Azure Functions を使用することで、API は需要に基づいて動的にスケーリングされ、API には次の機能があります。

- ユーザー [認証とロールベースの承認](user-information.md)データに直接アクセスできる **統合セキュリティ**。

- カスタム CORS ルールを必要とせずに、Web アプリで安全に _api_ ルートを使用できるようにする **シームレスなルーティング**。

Azure Static Web Apps の API は、[ホスティング プラン](plans.md#features)に基づき、使用できる次の 2 つの構成でサポートされています。

- **マネージド関数**: 既定では、静的 Web アプリの API は、いくつかの制限が関連付けられた、Azure Static Web Apps によって管理およびデプロイされる Azure Functions アプリです。

- **独自の関数の持ち込み**: 必要に応じて、任意のプランの種類の [既存のAzure Functionsアプリケーションを用意する](functions-bring-your-own.md)ことができます (Azure Functions のすべての機能に付いています)。 この構成の場合、Functions アプリの個別のデプロイを処理する必要があります。

次の表では、マネージド関数と既存の関数の使用の違いを対比したものです。

| 機能 | マネージド関数 | 独自の関数の持ち込み |
| --- | --- | --- |
| Azure Functions [トリガー](../azure-functions/functions-triggers-bindings.md#supported-bindings)へのアクセス | HTTP のみ | All |
| サポートされている Azure Functions [ランタイム](../azure-functions/supported-languages.md#languages-by-runtime-version) | Node.js 12<br>.NET Core 3.1<br>.NET 6.0<br>Python 3.8 | All |
| サポートされている Azure Functions の[ホスティング プラン](../azure-functions/functions-scale.md) | 従量課金 | 従量課金<br>Premium<br>専用 |
| ユーザー認証とロールベースの承認データに直接アクセスできる[統合セキュリティ](user-information.md)。 | ✔ | ✔ |
| カスタム CORS ルールを必要とせずに、Web アプリで安全に _api_ ルートを使用できるようにする [ルーティングの統合](./configuration.md?#routes)。 | ✔ | ✔ |
| [Durable Functions](../azure-functions/durable/durable-functions-overview.md) プログラミング モデル | ✕ | ✔ |
| [管理対象 ID](../app-service/overview-managed-identity.md) | ✕ | ✔ |
| [Azure App Service の認証と承認](../app-service/configure-authentication-provider-aad.md)のトークン管理 | ✕ | ✔ |
| Azure Static Web Apps の外部で使用できる API 関数 | ✕ | ✔ |
| [Key Vault 参照](../app-service/app-service-key-vault-references.md) | ✕ | ✔ |

## <a name="configuration"></a>構成

Web アプリでは、_api_ ルートを介して API エンドポイントを使用できます。

| マネージド関数 | 独自の関数の持ち込み |
| --- | --- |
| _api_ ルートは固定されていますが、マネージド関数アプリのソース コードのフォルダーの場所は制御できます。 リポジトリの _.github/workflows_ フォルダーにある [ワークフロー YAML ファイルを編集する](build-configuration.md)ことにより、この場所を変更できます。 | _api_ ルートへの要求は、既存の Azure Functions アプリに送信されます。 |

## <a name="troubleshooting-and-logs"></a>トラブルシューティングとログ

ログは、[Application Insights](monitor.md) を追加した場合にのみ使用できます。

| マネージド関数 | 独自の関数の持ち込み |
| --- | --- |
| 静的 Web アプリ上で Application Insights を有効にして、ログ記録を有効にします。 | Azure Functions アプリ上で Application Insights を有効にして、ログ記録を有効にします。 |

## <a name="constraints"></a>制約

- API ルートのプレフィックスは、_api_ である必要があります。
- API 関数のルート規則では、[リダイレクト](configuration.md#defining-routes)と[ロールによるルートのセキュリティ保護](configuration.md#securing-routes-with-roles)のみがサポートされます。

| マネージド関数 | 独自の関数の持ち込み |
| --- | --- |
| <ul><li>トリガーは、[HTTP](../azure-functions/functions-bindings-http-webhook.md) に限定されています。</li><li>Azure Functions アプリは Node.js 12、.NET Core 3.1、.NET 6.0、または Python 3.8 形式である必要があります。</li><li>一部のアプリケーション設定は、サービスによって管理されているため、次のプレフィックスはランタイムで予約されています。<ul><li>*APPSETTING\_、AZUREBLOBSTORAGE\_、AZUREFILESSTORAGE\_、AZURE_FUNCTION\_、CONTAINER\_、DIAGNOSTICS\_、DOCKER\_、FUNCTIONS\_、IDENTITY\_、MACHINEKEY\_、MAINSITE\_、MSDEPLOY\_、SCMSITE\_、SCM\_、WEBSITES\_、WEBSITE\_、WEBSOCKET\_、AzureWeb*</li></ul></li></ul> | <ul><li>Functions アプリのデプロイを管理する必要があります。</li></ul> |

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
