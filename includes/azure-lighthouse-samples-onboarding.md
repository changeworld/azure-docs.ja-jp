---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/17/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 5c4a4ab57e2e4a63579cc753e183cd9cecc4b765
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868057"
---
特定のオンボード シナリオに対応するさまざまなテンプレートが用意されています。 最適なオプションを選択し、環境に合わせてパラメーター ファイルを変更してください。 デプロイでこれらのファイルを使用する方法の詳細については、「[Azure Lighthouse への顧客のオンボード](../articles/lighthouse/how-to/onboard-customer.md)」を参照してください。

| **テンプレート** | **説明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | 顧客のサブスクリプションを Azure Lighthouse にオンボードします。 デプロイは、サブスクリプションごとに個別に実行する必要があります。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | 顧客の 1 つまたは複数のリソース グループを Azure Lighthouse にオンボードします。 1 つのリソース グループをオンボードするには、**rgDelegatedResourceManagement**  を使用します。同じサブスクリプション内の複数のリソース グループをオンボードするには、**multipleRgDelegatedResourceManagement** を使用します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | [Azure Marketplace にマネージド サービス オファーを発行](../articles/lighthouse/how-to/publish-managed-services-offers.md)している場合、このオファーを承諾した顧客のリソースをオンボードするためにオプションでこのテンプレートを使用できます。 このパラメーター ファイル内の marketplace 値は、プランの発行時に使用した値と一致している必要があります。 |

[適格な認可](../articles/lighthouse/how-to/create-eligible-authorizations.md) (現在はパブリック プレビュー) を含めるには、サンプル リポジトリの [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) セクションから対応するテンプレートを選択します。

通常、オンボードするサブスクリプションごとに個別に展開する必要がありますが、複数のサブスクリプションにテンプレートをデプロイすることもできます。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |


