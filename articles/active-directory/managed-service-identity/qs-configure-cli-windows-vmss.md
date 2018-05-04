---
title: Azure CLI を使用して Azure 仮想マシン スケール セットの MSI を構成する
description: Azure CLI を使用して、Azure 仮想マシン スケール セット上の管理対象サービス ID (MSI) を構成する方法をステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/15/2018
ms.author: daveba
ms.openlocfilehash: 9cdf5225f2d87fffa2290e3edd09d4ae829aee21
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
---
# <a name="configure-a-virtual-machine-scale-set-managed-service-identity-msi-using-azure-cli"></a>Azure CLI を使用して、Azure 仮想マシン スケール セットの管理対象サービス ID (MSI) を構成する

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

管理対象サービス ID は、Azure Active Directory で自動管理対象 ID を使用する Azure サービスを提供します。 この ID を使用して、コードに資格情報が含まれていなくても、Azure AD の認証をサポートする任意のサービスに認証することができます。 

この記事では、Azure CLI を使用して Azure 仮想マシン スケール セットの MSI を有効化および削除する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。

- Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
- 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョンをインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 以降) 。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セット の作成中に MSI を有効にする

次のようにして、MSI が有効化された仮想マシン スケール セットを作成します。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。 仮想マシン スケール セットをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az group create](/cli/azure/group/#az_group_create) を使用して、仮想マシン スケール セットとその関連リソースの管理およびデプロイ用に[リソース グループ](../../azure-resource-manager/resource-group-overview.md#terminology)を作成します。 代わりに使用するリソース グループが既にある場合は、この手順をスキップできます。

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. [az vmss create](/cli/azure/vmss/#az_vmss_create) を使用して仮想マシン スケール セットを作成します。 次の例では、`--assign-identity` パラメーターからの要求により、MSI を使用して *myVM* という仮想マシン スケール セットを作成します。 `--admin-username` および `--admin-password` パラメーターは、仮想マシンのサインイン用の管理ユーザー名とパスワードを指定します。 これらの値は、お使いの環境に合わせて更新してください。 

   ```azurecli-interactive 
   az vmss create --resource-group myResourceGroup --name myVMSS --image win2016datacenter --upgrade-policy-mode automatic --custom-data cloud-init.txt --admin-username azureuser --admin-password myPassword12 --assign-identity --generate-ssh-keys
   ```

## <a name="enable-msi-on-an-existing-azure-virtual-machine-scale-set"></a>既存の Azure 仮想マシンをスケール セットで MSI を有効にする

既存の Azure 仮想マシン スケール セットで MSI を有効にする必要がある場合は、次のようにします。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity assign](/cli/azure/vmss/identity/#az_vmss_identity_assign) コマンドを使用して、次のように MSI を既存 VM に追加します。

   ```azurecli-interactive
   az vmss identity assign -g myResourceGroup -n myVMSS
   ```

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Azure 仮想マシン スケール セットから MSI を削除する

MSI が不要になった仮想マシン スケール セットがある場合は、次のようにします。

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az_login) を使用して Azure にサインインします。 目的の仮想マシン スケール セットが含まれる Azure サブスクリプションに関連付けられたアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. [az vmss identity remove](/cli/azure/vmss/identity/#az_vmss_remove_identity) コマンドを使用して、次のように MSI を削除します。

   ```azurecli-interactive
   az vmss identity remove -g myResourceGroup -n myVMSS --identities readerID writerID
   ```

## <a name="next-steps"></a>次の手順

- [管理対象サービス ID の概要](overview.md)
- Azure 仮想マシン スケール セットの全作成に関するクイック スタートについては、次を参照してください。 

  - [CLI を使用して仮想マシン スケール セットを作成する](../../virtual-machines/linux/tutorial-create-vmss.md#create-a-scale-set)

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。
















