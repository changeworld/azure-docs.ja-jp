---
title: Azure VMware Solution リソース プロバイダーの登録
description: Azure VMware Solution リソース プロバイダーを登録する手順。
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 96d15546c5102a69a0b19f92de33d35d2e9ab6c2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/30/2020
ms.locfileid: "91575743"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

Azure VMware Solution を使用するには、最初にリソース プロバイダーをサブスクリプションに登録する必要があります。

```azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

>[!TIP]
>または、GUI を使用して **Microsoft.AVS** リソース プロバイダーを登録することもできます。  詳細については、[リソース プロバイダーの登録と種類](../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider)に関する記事をご覧ください。  
