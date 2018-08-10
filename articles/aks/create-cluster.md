---
title: Azure Kubernetes Service (AKS) クラスターの作成
description: CLI または Azure Portal を使用して AKS クラスターを作成します。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 06/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 5c133c61c989bf19be3e84287cb76a7d110dccc8
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440475"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Azure Kubernetes Service (AKS) クラスターの作成

Azure Kubernetes Service (AKS) クラスターは、Azure CLI または Azure Portal を使用して作成することができます。

## <a name="azure-cli"></a>Azure CLI

AKS クラスターを作成するには、[az aks create][az-aks-create] コマンドを使用します。

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

`az aks create` コマンドでは以下のオプションを使用できます。 各引数の詳細については、AKS の [Azure CLI 参照][az-aks-create]を参照してください。

| 引数 | 説明 | 必須 |
|---|---|:---:|
| `--name` `-n` | 管理対象クラスターのリソース名。 | はい |
| `--resource-group` `-g` | Azure Kubernetes Service リソース グループの名前。 | はい |
| `--admin-username` `-u` | Linux Virtual Machines のユーザー名。  既定値: azureuser。 | × |
| `--aad-client-app-id` | (プレビュー) 種類が "Native" の Azure Active Directory クライアント アプリケーションの ID。 | × |
| `--aad-server-app-id` | (プレビュー) 種類が "Web app/API" の Azure Active Directory サーバー アプリケーションの ID。 | × |
| `--aad-server-app-secret` | (プレビュー) Azure Active Directory サーバー アプリケーションのシークレット。 | × |
| `--aad-tenant-id` | (プレビュー) Azure Active Directory テナントの ID。 | × |
| `--admin-username` `-u` | SSH アクセス用のノード VM を作成するユーザー アカウント。  既定値: azureuser。 | × |
| ` --client-secret` | サービス プリンシパルに関連付けられているシークレット。 | × |
| `--dns-name-prefix` `-p` | クラスター パブリック IP アドレスの DNS プレフィックス。 | × |
| `--dns-service-ip` | Kubernetes DNS サービスに割り当てられた IP アドレス。 | × |
| `--docker-bridge-address` | Docker bridge に割り当てられた IP アドレスとネットマスク。 | × |
| `--enable-addons` `-a` | カンマ区切りのリストで Kubernetes アドオンを有効にします。 | × |
| `--enable-rbac` `-r` | Kubernetes のロールベースのアクセス制御を有効にします。 | × |
| `--generate-ssh-keys` | SSH 公開キー ファイルおよび SSH 秘密キー ファイルがない場合は生成します。 | × |
| `--kubernetes-version` `-k` | クラスターの作成に使用する Kubernetes のバージョン ("1.7.9" や "1.9.6" など)。 | × |
| `--locaton` `-l` | 自動的に作成されたリソース グループの場所。 | × |
| `--max-pods` `-m` | ノードに展開できるポッドの最大数。 | × |
| `--network-plugin` | 使用する Kubernetes ネットワーク プラグイン。 | × |
| `--no-ssh-key` `-x` | ローカル SSH キーを使用または作成しないでください。 | × |
| `--no-wait` | 実行時間の長い操作の終了を待機しません。 | × |
| `--node-count` `-c` | ノード プールの既定のノード数。  既定値: 3。 | × |
| `--node-osdisk-size` | ノード プール仮想マシンの osDisk サイズ (GB 単位)。 | × |
| `--node-vm-size` `-s` | 仮想マシンのサイズ。  既定値: Standard_D1_v2。 | × |
| `--pod-cidr` | Kubenet を使用している場合、ポッド IP を割り当てる CIDR 表記の IP 範囲。 | × |
| `--service-cidr` | Kubenet を使用している場合、サービス クラスター IP を割り当てる CIDR 表記の IP 範囲。 | × |
| `--service-principal` | クラスター認証に使用されるサービス プリンシパル。 | × |
| `--ssh-key-value` | SSH キー ファイルの値またはキー ファイルのパス。  既定値: ~/.ssh/id_rsa.pub。 | × |
| `--tags` | "key[=value]" 形式のスペース区切りのタグ。 既存のタグをクリアするには '' を使用します。 | × |
| `--vnet-subnet-id` | クラスターを展開する既存の VNet 内のサブネット ID。 | × |
| `--workspace-resource-id` | モニタリング データを保存するために使用する既存の Log Analytics Workspace のリソース ID。 | × |

## <a name="azure-portal"></a>Azure ポータル

Azure Portal を使用した AKS クラスターのデプロイ手順については、Azure Kubernetes Service (AKS) の [Azure Portal クイックスタート][aks-portal-quickstart]に関するページをご覧ください。

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
