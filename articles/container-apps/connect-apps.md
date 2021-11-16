---
title: Azure Container Apps プレビューでアプリケーションを接続する
description: Azure Container Apps で通信する複数のアプリケーションをデプロイする方法について説明します。
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: 75ccb6730d9fdad76f7b7f6f78d3695e86a349f0
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "132027396"
---
# <a name="connect-applications-in-azure-container-apps-preview"></a>Azure Container Apps プレビューでアプリケーションを接続する

[イングレス](ingress.md)が有効になっている場合、Azure Container Apps によりドメイン名を通して各コンテナー アプリが公開されます。 イングレス エンドポイントは、世界に向けてパブリックに公開することも、同じ[環境](environment.md)内の他のコンテナー アプリのみが使用できるように内部的に公開することもできます。

コンテナー アプリのドメイン名がわかっていれば、アプリケーション コード内でその場所を呼び出して、複数のコンテナー アプリをまとめて接続できます。

> [!NOTE]
> FQDN を使用して同じ環境で別のコンテナーを呼び出す場合、ネットワーク トラフィックが環境から出ることはありません。

FQDN による場所または Dapr の両方を使用してコンテナー間で呼び出す方法を示すサンプル ソリューションについては、[Azure のサンプル](https://github.com/Azure-Samples/container-apps-connect-multiple-apps)を参照してください。

## <a name="location"></a>Location

コンテナー アプリの場所は、その環境、名前、リージョンに関連付けられている値で構成されます。 トップレベル ドメイン `azurecontainerapps.io` を通して使用でき、完全修飾ドメイン名 (FQDN) では次のものが使われます。

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
