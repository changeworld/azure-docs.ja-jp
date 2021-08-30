---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 08/26/2021
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 3fec700c30f33d72d632c805bf2874d7d1d9dd02
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035737"
---
特定のオンボード シナリオに対応するさまざまなテンプレートが用意されています。 最適なオプションを選択し、環境に合わせてパラメーター ファイルを変更してください。 デプロイでこれらのファイルを使用する方法の詳細については、「[Azure Lighthouse への顧客のオンボード](../articles/lighthouse/how-to/onboard-customer.md)」を参照してください。

| **テンプレート** | **説明** |
|---------|---------|
| [subscription](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/subscription) | 顧客のサブスクリプションを Azure Lighthouse にオンボードします。 デプロイは、サブスクリプションごとに個別に実行する必要があります。 |
| [rg と multi-rg](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management/rg) | 顧客の 1 つまたは複数のリソース グループを Azure Lighthouse にオンボードします。 1 つのリソース グループをオンボードするには rg.json を使用し、サブスクリプション内の複数のリソース グループをオンボードするには multi-rg.json を使用します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | [Azure Marketplace にマネージド サービス オファーを発行](../articles/lighthouse/how-to/publish-managed-services-offers.md)している場合、このオファーを承諾した顧客のリソースをオンボードするためにオプションでこのテンプレートを使用できます。 このパラメーター ファイル内の marketplace 値は、プランの発行時に使用した値と一致している必要があります。 |

[適格な認可](../articles/lighthouse/how-to/create-eligible-authorizations.md) (現在はパブリック プレビュー) を含めるには、サンプル リポジトリの [delegated-resource-management-eligible-authorizations](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-eligible-authorizations) セクションから対応するテンプレートを選択します。

通常、オンボードするサブスクリプションごとに個別に展開する必要がありますが、複数のサブスクリプションにテンプレートをデプロイすることもできます。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |


