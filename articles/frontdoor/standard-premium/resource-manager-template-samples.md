---
title: Resource Manager テンプレートのサンプル - Azure Front Door
description: Azure Front Door 用に提供されているサンプルの Azure Resource Manager テンプレートに関する情報。
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 04/16/2021
ms.openlocfilehash: e939aec8f1cf5fa615904bb81bad82ea3cf22c61
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587478"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Front Door 用 Azure Resource Manager テンプレート

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

次の表には、Azure Front Door 用の Azure Resource Manager テンプレートへのリンクと、他の Azure サービスを含む参照アーキテクチャが含まれています。

| サンプル | 説明 |
|-|-|
| [Front Door (簡易作成)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium/) | エンドポイント、配信元グループ、配信元、ルートを含む基本的な Front Door プロファイルを作成します。  |
| [[ルール セット]](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-rule-set/) | Front Door のプロファイルとルール セットを作成します。  |
| [マネージド ルール セットを使用する WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-waf-managed/) | マネージド ルール セットを使用して、Front Door プロファイルと WAF を作成します。  |
| [カスタム ルールを使用する WAF ポリシー](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-waf-custom/) | カスタム ルールを使用して、Front Door プロファイルと WAF を作成します。  |
|**App Service オリジン**| **説明** |
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | パブリック エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | プライベート エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する App Service 環境](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | App Service 環境、プライベート エンドポイントを使用するアプリ、および Front Door プロファイルを作成します。  |
|**Azure Functions オリジン**| **説明** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | パブリック エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | プライベート エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
|**API Management オリジン**| **説明** |
| [API Management (外部)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | 外部 VNet 統合を使用する API Management インスタンスと Front Door プロファイルを作成します。  |
|**Storage オリジン**| **説明** |
| [Storage 静的 Web サイト](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Azure ストレージ アカウントとパブリック エンドポイントを使用する静的 Web サイト、および Front Door プロファイルを作成します。  |
| [Private Link を使用する Storage BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Azure ストレージ アカウントとプライベート エンドポイントを使用する BLOB コンテナー、および Front Door プロファイルを作成します。  |
|**Application Gateway オリジン**| **説明** |
| [Application Gateway](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-application-gateway-public) | Application Gateway、および Front Door プロファイルを作成します。 |
|**仮想マシン オリジン**| **説明** |
| [Private Link サービスを使用した仮想マシン](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-vm-private-link) | 仮想マシンと Private Link サービス、および Front Door プロファイルを作成します。 |
| | |
