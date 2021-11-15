---
title: Azure Resource Manager テンプレートのサンプル - Azure Front Door
description: Azure Front Door に使用される Resource Manager テンプレートのサンプルについて説明します。基本的な Front Door を作成するためのテンプレートや Front Door のレート制限を構成するためのテンプレートなどを取り上げます。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/01/2021
ms.author: duau
ms.openlocfilehash: db0bf4783de324af2ab4e5a5ec6bfbaa18ad33cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449775"
---
# <a name="azure-resource-manager-deployment-model-templates-for-front-door"></a>Front Door のための Azure Resource Manager デプロイ モデル テンプレート

次の表は、Azure Front Door のための Azure Resource Manager デプロイ モデル テンプレートのリンク一覧です。

## <a name="azure-front-door"></a>Azure Front Door

| Template | 説明 |
| ---| ---|
| [基本的な Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-basic)| 1 つのバックエンドを備えた基本的な Front Door 構成を作成します。 |
| [複数のバック エンドおよびバックエンド プールと URL ベースのルーティングを備えた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-multiple-backends)| 複数のバックエンドおよびバックエンド プール用に負荷分散が構成され、URL パスに基づくバックエンド プール間の Front Door を作成します。 |
| [Front Door を使用したカスタム ドメインとマネージド TLS 証明書のオンボード](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)| Front Door にカスタム ドメインを追加し、Front Door で管理される TLS 証明書を使用します。 |
| [Front Door を使用したカスタム ドメインとカスタマー マネージド TLS 証明書のオンボード](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain-customer-certificate)| Front Door にカスタム ドメインを追加し、Key Vault を使用することで独自の TLS 証明書を使用します。 |
| [地域フィルタリングされた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-geo-filtering)| 特定の国/地域からのトラフィックを許可/ブロックするフロント ドアを作成します。 |
| [Front Door のバックエンドの正常性プローブのコントロール](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-health-probes)| プローブ パスおよびプローブ送信間隔を更新することによって、Front Door を更新し、正常性プローブの設定を変更します。 |
| [アクティブ/スタンバイ バックエンド構成を備えた Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-priority-lb)| アクティブ/スタンバイ アプリケーション トポロジの優先順位ベースのルーティングをデモする Front Door を作成します。つまり、デフォルトではプライマリ (最高の優先順位) バックエンドが使用できなくなるまですべてのトラフィックをこのバックエンドに送信します。 |
| [特定のルートのキャッシュを有効にした Front Door の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-create-caching)| 定義済みのルーティング構成でキャッシュが有効になっている、つまりワークロードの任意の静的資産をキャッシュする Front Door を作成します。 |
| [Front Door のホスト名のセッション アフィニティの構成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-session-affinity) | Front Door を更新してフロントエンド ホストのセッション アフィニティを有効にすることによって、後続のトラフィックを同じユーザー セッションから同じバックエンドに送信します。 |
| [クライアント IP の許可リスト登録またはブロックリスト登録用の Front Door の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-clientip)| Front Door を構成して、クライアント IP を使用するカスタム アクセス コントロールを使用して特定のクライアント IP のトラフィックを制限します。 |
| [特定の http パラメーターでアクションを実行する Front Door の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-waf-http-params)| Front Door を構成して、http パラメーターを使用するアクセス コントロールのカスタム ルールを使用して受信要求の http パラメーターに基づいて特定のトラフィックを許可またはブロックします。 |
| [Front Door レート制限の構成](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-rate-limiting)| Front Door を構成して、特定のフロントエンド ホストの受信トラフィックのレート制限を行います。 |
| | |

## <a name="azure-front-door-standardpremium-preview"></a>Azure Front Door Standard または Premium (プレビュー)

| サンプル | 説明 |
|-|-|
| [Front Door (簡易作成)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium/) | エンドポイント、配信元グループ、配信元、ルートを含む基本的な Front Door プロファイルを作成します。  |
| [[ルール セット]](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rule-set/) | Front Door のプロファイルとルール セットを作成します。  |
| [マネージド ルール セットを使用する WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-waf-managed/) | マネージド ルール セットを使用して、Front Door プロファイルと WAF を作成します。  |
| [カスタム ルールを使用する WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-waf-custom/) | カスタム ルールを使用して、Front Door プロファイルと WAF を作成します。  |
| [レート制限を含む WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-rate-limit/) | レート制限を実行するためのカスタム ルールを使用して、Front Door プロファイルと WAF を作成します。  |
| [ジオフィルタリングを含む WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-geo-filtering/) | ジオフィルタリングを実行するためのカスタム ルールを使用して、Front Door プロファイルと WAF を作成します。  |
|**App Service オリジン**| **説明** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-app-service-public) | パブリック エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-app-service-private-link) | プライベート エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
|**Azure Functions オリジン**| **説明** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-function-public/) | パブリック エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-function-private-link) | プライベート エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
|**API Management オリジン**| **説明** |
| [API Management (外部)](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-api-management-external) | 外部 VNet 統合を使用する API Management インスタンスと Front Door プロファイルを作成します。  |
|**Storage オリジン**| **説明** |
| [Storage 静的 Web サイト](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-storage-static-website) | Azure ストレージ アカウントとパブリック エンドポイントを使用する静的 Web サイト、および Front Door プロファイルを作成します。  |
| [Private Link を使用する Storage BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-storage-blobs-private-link) | Azure ストレージ アカウントとプライベート エンドポイントを使用する BLOB コンテナー、および Front Door プロファイルを作成します。  |
|**Application Gateway オリジン**| **説明** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-standard-premium-application-gateway-public) | Application Gateway、および Front Door プロファイルを作成します。 |
|**仮想マシン オリジン**| **説明** |
| [Private Link サービスを使用した仮想マシン](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-premium-vm-private-link) | 仮想マシンと Private Link サービス、および Front Door プロファイルを作成します。 |
| | |

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
- [Front Door のしくみ](front-door-routing-architecture.md)について学習します。
