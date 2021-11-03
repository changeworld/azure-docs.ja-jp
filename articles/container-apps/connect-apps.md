---
title: Azure Container Apps プレビューでアプリケーションを接続する
description: Azure Container Apps で通信する複数のアプリケーションをデプロイする方法について説明します。
services: app-service
author: craigshoemaker
ms.service: app-service
ms.topic: conceptual
ms.date: 10/18/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 10fe890ecd4bb2bc89cea71d1a70f7df9eb38e1e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131089841"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでアプリケーションを接続する

[イングレス](ingress.md)が有効になっている場合、Azure Container Apps によりドメイン名を通して各コンテナー アプリが公開されます。 イングレス エンドポイントは、世界に向けてパブリックに公開することも、同じ[環境](environment.md)内の他のコンテナー アプリのみが使用できるように内部的に公開することもできます。

コンテナー アプリのドメイン名がわかっていれば、アプリケーション コード内でその場所を呼び出して、複数のコンテナー アプリをまとめて接続できます。

## <a name="location"></a>Location

コンテナー アプリの場所は、その環境、名前、リージョンに関連付けられている値で構成されます。 トップレベル ドメイン `azurecontainerapps.io` を通して使用でき、完全修飾ドメイン名では次のものが使われます。

- コンテナー アプリ名
- 環境の一意識別子
- リージョン名

次の図は、これらの値を使ってコンテナー アプリの完全修飾ドメイン名を構成する方法を示したものです。

:::image type="content" source="media/connect-apps/azure-container-apps-location.png" alt-text="Azure Container Apps のコンテナー アプリの完全修飾ドメイン名。":::

[!INCLUDE [container-apps-get-fully-qualified-domain-name](../../includes/container-apps-get-fully-qualified-domain-name.md)]

## <a name="dapr-location"></a>Dapr の場所

マイクロサービスを開発するには、多くの場合、分散アーキテクチャに共通のパターンを実装する必要があります。 Dapr を使うと、相互 TLS でマイクロサービスをセキュリティ保護し、エラーが発生したときに再試行をトリガーし、Azure Application Insights が有効になっているときは分散トレースを利用できます。

Dapr が使われているマイクロサービスは、次の URL パターンで利用できます。

:::image type="content" source="media/connect-apps/azure-container-apps-location-dapr.png" alt-text="Dapr での Azure Container Apps のコンテナー アプリの場所。":::

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [開始するには](get-started.md)
