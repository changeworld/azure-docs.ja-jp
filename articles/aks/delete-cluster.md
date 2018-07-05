---
title: Azure Kubernetes Service (AKS) クラスターの削除
description: CLI または Azure Portal を使用して AKS クラスターを削除します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 66dcebb702695a6601f6ed17b85a04d5bb4e01f6
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100045"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターの削除

Azure Kubernetes Service クラスターを削除すると、クラスターが配置されたリソース グループは存続しますが、AKS 関連のリソースはすべて削除されます。 このドキュメントでは、Azure CLI と Azure Portal を使用して AKS クラスターを削除する方法を示します。

クラスターの削除に加えて、クラスターが配置されたリソース グループを削除できます。これにより AKS クラスターも削除されます。

## <a name="azure-cli"></a>Azure CLI

AKS クラスターを削除するには、[az aks delete][az-aks-delete] コマンドを使用します。

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

`az aks delete` コマンドでは以下のオプションを使用できます。

| 引数 | 説明 | 必須 |
|---|---|:---:|
| `--name` `-n` | 管理対象クラスターのリソース名。 | はい |
| `--resource-group` `-g` | Azure Kubernetes Service リソース グループの名前。 | はい |
| `--no-wait` | 実行時間の長い操作の終了を待機しません。 | × |
| `--yes` `-y` | 確認のダイアログを表示しません。 | × |

## <a name="azure-portal"></a>Azure ポータル

Azure Portal で、Azure Kubernetes Service (AKS) をリソース含むリソース グループを参照し、このリソースを選択して、**[削除]** をクリックします。 削除操作の確認を求めるプロンプトが表示されます。

![Portal での AKS クラスターの削除](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete