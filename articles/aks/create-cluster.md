---
title: "Azure Container Service (AKS) クラスターの作成"
description: "CLI または Azure Portal を使用して AKS クラスターを作成します。"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 3e884bc16680d74801911547045deb48246afccd
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2018
---
# <a name="create-an-azure-container-service-aks-cluster"></a>Azure Container Service (AKS) クラスターの作成

Azure Container Service (AKS) クラスターは、Azure CLI または Azure Portal を使用して作成することができます。

## <a name="azure-cli"></a>Azure CLI

AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

`az aks create` コマンドでは以下のオプションを使用できます。

| 引数 | [説明] | 必須 |
|---|---|:---:|
| `--name` `-n` | 管理対象クラスターのリソース名。 | はい |
| `--resource-group` `-g` | Azure Container Service リソース グループの名前。 | はい |
| `--admin-username` `-u` | Linux Virtual Machines のユーザー名。  既定値: azureuser。 | × |
| ` --client-secret` | サービス プリンシパルに関連付けられているシークレット。 | × |
| `--dns-name-prefix` `-p` | クラスター パブリック IP アドレスの DNS プレフィックス。 | × |
| `--generate-ssh-keys` | SSH 公開キー ファイルおよび SSH 秘密キー ファイルがない場合は生成します。 | × |
| `--kubernetes-version` `-k` | クラスターの作成に使用する Kubernetes のバージョン ("1.7.9" や "1.8.2" など)。  既定値: 1.7.7。 | × |
| `--no-wait` | 実行時間の長い操作の終了を待機しません。 | × |
| `--node-count` `-c` | ノード プールの既定のノード数。  既定値: 3。 | × |
| `--node-osdisk-size` | ノード プール仮想マシンの osDisk サイズ (GB 単位)。 | × |
| `--node-vm-size` `-s` | 仮想マシンのサイズ。  既定値: Standard_D1_v2。 | × |
| `--service-principal` | クラスター認証に使用されるサービス プリンシパル。 | × |
| `--ssh-key-value` | SSH キー ファイルの値またはキー ファイルのパス。  既定値: ~/.ssh/id_rsa.pub。 | × |
| `--tags` | "key[=value]" 形式のスペース区切りのタグ。 既存のタグをクリアするには '' を使用します。 | × |

## <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用した AKS クラスターのデプロイ手順については、Azure Container Service (AKS) の [Azure Portal クイックスタート][aks-portal-quickstart]に関するページを参照してください。 

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md