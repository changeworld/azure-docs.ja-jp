---
title: "Azure クォータ エラー | Microsoft Docs"
description: "リソース クォータ エラーを解決する方法について説明します。"
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 09/13/2017
ms.author: tomfitz
ms.openlocfilehash: 6a8024a12c4a79e92e37df0a56b6e6bd0cb6a8d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-errors-for-resource-quotas"></a>リソース クォータのエラーを解決する

この記事では、リソースをデプロイするときに発生する可能性のあるクォータ エラーについて説明します。

## <a name="symptom"></a>症状

Azure クォータを超えるリソースを作成するテンプレートをデプロイすると、次のようなデプロイ エラーが発生します。

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

または、次のエラーが発生することがあります。

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>原因

クォータは、リソース グループ、サブスクリプション、アカウント、および他のスコープごとに適用されます。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 上限を超えたコア数の仮想マシンをデプロイしようとすると、クォータを超過したというエラーが発生します。
詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

Azure CLI で、`az vm list-usage` コマンドを使用して仮想マシンのクォータを検索します。

```azurecli
az vm list-usage --location "South Central US"
```

次のような結果が返されます。

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="solution-2"></a>解決策 2

PowerShell で、**Get-AzureRmVMUsage** コマンドを使用して仮想マシンのクォータを検索します。

```powershell
Get-AzureRmVMUsage -Location "South Central US"
```

次のような結果が返されます。

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

### <a name="solution-3"></a>解決策 3

クォータ制限を増やす必要がある場合は、ポータルに移動し、ファイルをデプロイするリージョンのクォータを増加させるように、サポートに問題を報告してください。

> [!NOTE]
> リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。 米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。 アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 の Resource Manager コアを要求する必要があります。
>
>