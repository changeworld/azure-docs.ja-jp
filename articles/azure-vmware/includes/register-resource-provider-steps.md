---
title: Azure VMware Solution リソース プロバイダーの登録
description: Azure VMware Solution リソース プロバイダーを登録する手順。
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: cd4a6f3003945973f0fe5367eb198823595a412e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750064"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

Azure VMware Solution を使用するには、最初にリソース プロバイダーをサブスクリプションに登録する必要があります。 リソース プロバイダーの詳細については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。

### <a name="azure-cli"></a>Azure CLI 

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

### <a name="azure-portal"></a>Azure portal
 
1. [Azure portal](https://portal.azure.com) にサインインします。

1. Azure portal のメニューで、**[すべてのサービス]** を選択します。

1. **[すべてのサービス]** ボックスで **"サブスクリプション (subscription)"** と入力し、**[サブスクリプション]** を選択します。

1. サブスクリプションの一覧から、表示するサブスクリプションを選択します。

1. **[リソース プロバイダー]** を選択し、検索対象として「**Microsoft.AVS**」と入力します。 
 
1. リソースプロバイダーが登録されていない場合は、 **[登録]** を選択します。