---
title: Azure Containers Apps プレビューを使用したマイクロサービス
description: Azure Container Apps でマイクロサービスを構築します。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 39d095f28c9f642815e5463975b9836e228d9c3a
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028745"
---
# <a name="microservices-with-azure-containers-apps-preview"></a>Azure Containers Apps プレビューを使用したマイクロサービス

[マイクロサービス アーキテクチャ](https://azure.microsoft.com/solutions/microservice-applications/#overview) を使用すると、システム全体の機能のコア領域を個別に開発、アップグレード、バージョン管理、およびスケーリングできます。 Azure Container Apps は、次の機能を備えるマイクロサービスをデプロイする基盤を提供します。

- 独立した[スケーリング](scale-app.md)、[バージョン管理](application-lifecycle-management.md)、および[アップグレード](application-lifecycle-management.md)
- [サービス検出](connect-apps.md)
- ネイティブ[Dapr 統合](microservices-dapr.md)

:::image type="content" source="media/microservices/azure-container-services-microservices.png" alt-text="Containers Apps はマイクロサービスとしてデプロイされます。":::

Containers Apps [環境](environment.md)は、コンテナー アプリのグループを囲むセキュリティ境界を提供します。 1 つの Containers Apps は、通常、1 つ以上の[コンテナー](containers.md)で作られたポッドで構成されるマイクロサービスを表します。

## <a name="dapr-integration"></a>Dapr 統合

マイクロサービスで構成されるシステムを実装する場合、関数呼び出しはネットワーク全体に分散されます。 マイクロサービスの分散の性質をサポートするには、エラー、再試行、タイムアウトを考慮する必要があります。 Container Apps ではマイクロサービスを実行するための構成要素を備えていますが、[Dapr](https://docs.dapr.io/concepts/overview/) を使用すると、さらに豊富なマイクロサービス プログラミング モデルが提供されます。 Dapr には、監視、pub/sub、相互 TLS を使用したサービス間呼び出し、再試行などの機能が含まれています。

Dapr の使用方法について詳しくは、「[Dapr を使用してマイクロサービスを構築する](microservices-dapr.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [スケーリング](scale-app.md)
