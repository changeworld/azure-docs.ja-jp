---
title: Azure Lighthouse のサンプルとテンプレート
description: これらのサンプルと Azure Resource Manager テンプレートでは、Azure の委任されたリソース管理に顧客をオンボードする方法を示し、Azure Lighthouse のシナリオをサポートしています。
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553685"
---
# <a name="azure-lighthouse-samples"></a>Azure Lighthouse サンプル

次の表は、Azure Lighthouse 用の主要な Azure Resource Manager テンプレートのリンク一覧です。 [Azure Lighthouse サンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)にはこれらのファイル以外にさらに多くのファイルが用意されています。

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Azure の委任されたリソース管理に顧客をオンボードする

特定のオンボード シナリオに対応するさまざまなテンプレートが用意されています。 最適なオプションを選択し、環境に合わせてパラメーター ファイルを変更してください。 展開でこれらのファイルを使用する方法の詳細については、「[Azure の委任されたリソース管理に顧客をオンボードする](../how-to/onboard-customer.md)」を参照してください。

| **テンプレート** | **説明** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Azure の委任されたリソース管理に顧客のサブスクリプションをオンボードします。 デプロイは、サブスクリプションごとに個別に実行する必要があります。 |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Azure の委任されたリソース管理に顧客のリソース グループを 1 つまたは複数オンボードします。 1 つのリソース グループをオンボードするには、**rgDelegatedResourceManagement**  を使用します。同じサブスクリプション内の複数のリソース グループをオンボードするには、**multipleRgDelegatedResourceManagement** を使用します。 |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | [Azure Marketplace にマネージド サービス オファーを発行](../how-to/publish-managed-services-offers.md)している場合、このオファーを承諾した顧客のリソースをオンボードするためにオプションでこのテンプレートを使用できます。 このパラメーター ファイル内の marketplace 値は、プランの発行時に使用した値と一致している必要があります。 |

通常、オンボードするサブスクリプションごとに個別に展開する必要がありますが、複数のサブスクリプションにテンプレートをデプロイすることもできます。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | 複数のサブスクリプションをまたいで Azure Resource Manager テンプレートをデプロイします。 |

## <a name="azure-policy"></a>Azure Policy

これらのサンプルでは、Azure の委任されたリソース管理用にオンボードされたサブスクリプションで Azure Policy を使用する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | 委任されたサブスクリプションに (modify 効果を使用して) タグを追加または削除するポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | 委任の割り当てを監査するポリシーを割り当てます。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | (deployIfNotExists 効果を使用して) 委任されたサブスクリプションの Azure Key Vault リソースの診断を有効にするポリシーを割り当てます。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | 委任されたサブスクリプションに対する診断を有効にするいくつかのポリシーを割り当て、Windows と Linux のすべての VM を、このポリシーで作成された Log Analytics のワークスペースに接続します。 詳細については、[委任されたサブスクリプション内での修復できるポリシーのデプロイ](../how-to/deploy-policy-remediation.md)に関する説明を参照してください。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | 1 つのイニシアチブ (複数の関連するポリシー定義) を委任されたサブスクリプションに適用します。 |

## <a name="azure-monitor"></a>Azure Monitor

これらのサンプルでは、Azure Monitor を使用して、Azure の委任されたリソース管理用にオンボードされたサブスクリプションのアラートを作成する方法を示します。

| **テンプレート** | **説明** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | このテンプレートは、Azure アラートを作成し、既存のアクション グループに接続します。|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Kusto クエリに基づいてログ警告を複数作成します。|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | ユーザーが管理テナントにサブスクリプションを委任するときに、テナントにアラートをデプロイします。|

## <a name="additional-cross-tenant-scenarios"></a>その他のテナント間のシナリオ

これらのサンプルでは、テナント間の管理シナリオで実行できるさまざまなタスクを示しています。

| **テンプレート** | **説明** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | 2 つの異なるリソース グループにストレージ アカウントをデプロイします。|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Azure 管理サービスを作成し、それらをリンクし、追加のソリューションをデプロイします。 エンドツーエンドでデプロイする場合は、**rgWithAzureMgmt.json** テンプレートを使用します。 |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | 対象となる Azure サブスクリプション内で Azure Security Center を有効にし、構成します。 |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | 委任されたサブスクリプションの既存の Log Analytics ワークスペースで Azure Sentinel をデプロイして有効にします。 |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | これらのテンプレートを使用すると、Windows および Linux の VM に Log Analytics VM 拡張機能をデプロイし、指定した Log Analytics ワークスペースに接続することができます。 |

## <a name="next-steps"></a>次の手順

- [Azure の委任されたリソース管理](../concepts/azure-delegated-resource-management.md)について学習してください。
- [Azure Lighthouse のサンプル リポジトリ](https://github.com/Azure/Azure-Lighthouse-samples/)に関するページを参照してください。
