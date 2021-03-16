---
title: Resource Manager テンプレートのサンプル - Azure Front Door
description: Azure Front Door 用に提供されているサンプルの Azure Resource Manager テンプレートに関する情報。
services: frontdoor
author: johndowns
ms.author: jodowns
ms.service: frontdoor
ms.topic: sample
ms.date: 03/05/2021
ms.openlocfilehash: 393c15761c16dbf04d2af48fe13c0bc41b2e15b1
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510344"
---
# <a name="azure-resource-manager-templates-for-azure-front-door"></a>Azure Front Door 用 Azure Resource Manager テンプレート

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

次の表には、Azure Front Door 用の Azure Resource Manager テンプレートへのリンクと、他の Azure サービスを含む参照アーキテクチャが含まれています。

| App Service | 説明 |
|-|-|
| [App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-app-service-public) | パブリック エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する App Service](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-private-link) | プライベート エンドポイントを使用する App Service アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する App Service 環境](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-app-service-environment-internal-private-link) | App Service 環境、プライベート エンドポイントを使用するアプリ、および Front Door プロファイルを作成します。  |
|**Azure Functions**| **説明** |
| [Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-function-public/) | パブリック エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
| [Private Link を使用する Azure Functions](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-function-private-link) | プライベート エンドポイントを使用する Azure Functions アプリと Front Door プロファイルを作成します。  |
|**API Management**| **説明** |
| [API Management (外部)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-api-management-external) | 外部 VNet 統合を使用する API Management インスタンスと Front Door プロファイルを作成します。  |
|**Storage**| **説明** |
| [Storage 静的 Web サイト](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-standard-premium-storage-static-website) | Azure ストレージ アカウントとパブリック エンドポイントを使用する静的 Web サイト、および Front Door プロファイルを作成します。  |
| [Private Link を使用する Storage BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-premium-storage-blobs-private-link) | Azure ストレージ アカウントとプライベート エンドポイントを使用する BLOB コンテナー、および Front Door プロファイルを作成します。  |
| | |
