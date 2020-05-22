---
title: Azure Functions による Azure Static Web Apps での API のサポート
description: Azure Static Web Apps でサポートされる API の機能について説明します
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594271"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>Azure Functions による Azure Static Web Apps プレビューでの API のサポート

Azure Static Web Apps では、[Azure Functions](../azure-functions/functions-overview.md) を介してサーバーレス API エンドポイントが提供されています。 Azure Functions を利用することで、API は需要に基づいて動的にスケーリングされ、API には次の機能があります。

- ユーザー[認証とロールベースの承認](user-information.md)データに直接アクセスできる**統合セキュリティ**。
- カスタム CORS ルールを必要とせずに、Web アプリで安全に _api_ ルートを使用できるようにする**シームレスなルーティング**。
- Node.js 12 と互換性がある **Azure Functions** v3。
- **HTTP トリガー**と出力バインディング。

## <a name="configuration"></a>構成

Web アプリでは、_api_ ルートを介して API エンドポイントを使用できます。 このルートは固定ですが、関連付けられる Azure Functions アプリを配置するフォルダーを制御できます。 リポジトリの _.github/workflows_ フォルダーにある[ワークフロー YAML ファイルを編集する](github-actions-workflow.md#build-and-deploy)ことにより、この場所を変更できます。

## <a name="constraints"></a>制約

Azure Static Web Apps では、Azure Functions を通じて API が提供されます。 Azure Functions の機能では、Web アプリ用の API を作成し、Web アプリが API に安全に接続できるようにする特定の機能セットに、焦点が当てられています。 これらの機能には、次のようないくつかの制約があります。

- API ルートのプレフィックスは、_api_ である必要があります。
- トリガーとバインドは、[HTTP](../azure-functions/functions-bindings-http-webhook.md) に限定されています。
  - 出力バインドを除く他のすべての [Azure Functions のトリガーとバインド](../azure-functions/functions-triggers-bindings.md#supported-bindings)は、制限されます。
- ログは、関数アプリに [Application Insights](../azure-functions/functions-monitoring.md) を追加した場合にのみ使用できます。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [API を追加する](add-api.md)
