---
title: インクルード ファイル
description: インクルード ファイル
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: e32b55ed655b1e47f85640eb7f494a89f3274667
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456745"
---
特定のオンボード シナリオに対応するさまざまなテンプレートが用意されています。 最適なオプションを選択し、環境に合わせてパラメーター ファイルを変更してください。 展開でこれらのファイルを使用する方法の詳細については、「[Azure の委任されたリソース管理に顧客をオンボードする](../articles/lighthouse/how-to/onboard-customer.md)」を参照してください。

| **テンプレート** | **説明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Azure の委任されたリソース管理に顧客のサブスクリプションをオンボードします。 デプロイは、サブスクリプションごとに個別に実行する必要があります。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Azure の委任されたリソース管理に顧客のリソース グループを 1 つまたは複数オンボードします。 1 つのリソース グループをオンボードするには、**rgDelegatedResourceManagement**  を使用します。同じサブスクリプション内の複数のリソース グループをオンボードするには、**multipleRgDelegatedResourceManagement** を使用します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | [Azure Marketplace にマネージド サービス オファーを発行](../articles/lighthouse/how-to/publish-managed-services-offers.md)している場合、このオファーを承諾した顧客のリソースをオンボードするためにオプションでこのテンプレートを使用できます。 このパラメーター ファイル内の marketplace 値は、プランの発行時に使用した値と一致している必要があります。 |

通常、オンボードするサブスクリプションごとに個別に展開する必要がありますが、複数のサブスクリプションにテンプレートをデプロイすることもできます。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |
