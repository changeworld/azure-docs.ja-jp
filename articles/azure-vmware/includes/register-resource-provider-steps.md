---
title: Azure VMware Solution リソース プロバイダーの登録
description: Azure VMware Solution リソース プロバイダーを登録する手順。
ms.topic: include
ms.date: 09/04/2020
ms.openlocfilehash: 08741181b778b44289868a9f3fb1c2368955248c
ms.sourcegitcommit: 8791f69d44150767807d215cafc4076f3ed43f9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/08/2020
ms.locfileid: "89512372"
---
Azure VMware Solution を使用するには、最初にリソース プロバイダーをサブスクリプションに登録する必要があります。

```
azurecli-interactive
az provider register -n Microsoft.AVS --subscription <your subscription ID>
```

リソース プロバイダーを登録するその他の方法については、「[Azure リソース プロバイダーと種類](../../azure-resource-manager/management/resource-providers-and-types.md)」を参照してください。