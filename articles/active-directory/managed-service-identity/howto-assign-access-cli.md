---
title: Azure CLI を使用して、Azure リソースに MSI アクセスを割り当てる方法
description: Azure CLI を使用して、1 つのリソースに MSI を割り当て、別のリソースにアクセスを割り当てる方法について、ステップ バイ ステップで説明します。
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/25/2017
ms.author: daveba
ms.openlocfilehash: a5da06eac7f4680282aad305f57cb9ca1c9d5730
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424434"
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-azure-cli"></a>Azure CLI を使用してリソースに管理対象サービス ID (MSI) アクセスを割り当てる

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

MSI で Azure リソースを構成したら、他のセキュリティ プリンシパルと同じように、別のリソースに MSI アクセスを付与できます。 この例では、Azure CLI を使用して、Azure 仮想マシンまたは仮想マシン スケール セットの MSI アクセスを Azure ストレージ アカウントに付与する方法について説明します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

CLI スクリプトの例を実行するには、次の 3 つのオプションがあります。

- Azure ポータルから [Azure Cloud Shell](../../cloud-shell/overview.md) を使用する (次のセクションを参照してください)。
- 各コード ブロックの右上隅にある「試してみる」ボタンを利用して、埋め込まれた Azure Cloud Shell シェルを使用します。
- ローカル CLI コンソールを使用する場合は、[CLI 2.0 の最新バージョンをインストールする](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 以降) 。 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>RBAC を使用して、別のリソースに MSI アクセスを割り当てる

[Azure 仮想マシン](qs-configure-cli-windows-vm.md)、[Azure 仮想マシン スケール セット](qs-configure-cli-windows-vmss.md)などの Azure リソースで MSI を有効にした後、次のようにします。 

1. ローカルのコンソールで Azure CLI を使用している場合は、最初に [az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。 VM または仮想マシン スケール セットをデプロイする Azure サブスクリプションに関連付けられているアカウントを使用します。

   ```azurecli-interactive
   az login
   ```

2. この例では、ストレージ アカウントに Azure 仮想マシン アクセスを許可します。 まず、[az resource list](/cli/azure/resource/#az-resource-list) を使用して "myVM" という仮想マシンのサービス プリンシパルを取得します。

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure 仮想マシン スケール セットについてもコマンドはほぼ同じですが、ここでは、"DevTestVMSS" という名前の仮想マシン スケール セットのサービス プリンシパルを取得します。
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

3. サービス プリンシパル ID を作成したら、[az role assignment create](/cli/azure/role/assignment#az-role-assignment-create) を使用して、"myStorageAcct" というストレージ アカウントに、仮想マシンまたは仮想マシン スケール セットの "閲覧者" アクセスを付与します。

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="troubleshooting"></a>トラブルシューティング

リソースの MSI が使用可能な ID の一覧に表示されない場合は、MSI が正しく有効になっていることを確認します。 この場合、[Azure Portal](https://portal.azure.com) の Azure 仮想マシンまたは仮想マシン スケール セットに戻り、次の操作を行うことができます。

- [構成] ページを参照し、MSI の有効が [はい] になっていることを確認します。
- [拡張] ページを参照して、MSI 拡張機能が正常に展開されていることを確認します (**[拡張]** ページは、Azure 仮想マシン スケール セットでは使用できません)。

いずれかが正しくない場合は、リソース上で MSI を再デプロイするか、デプロイ エラーのトラブルシューティングを行う必要があります。

## <a name="related-content"></a>関連コンテンツ

- MSI の概要については、[管理対象サービス ID の概要](overview.md)に関する記事をご覧ください。
- Azure 仮想マシンで MSI を有効にするには、「[Configure an Azure VM Managed Service Identity (MSI) using Azure CLI (Azure CLI を使用して Azure VM 管理対象サービス ID (MSI) を構成する)](qs-configure-cli-windows-vm.md)」を参照してください。
- Azure 仮想マシン スケール セットで MSI を有効にするには、「[Configure an Azure Virtual Machine Scale Set Managed Service Identity (MSI) using the Azure portal (Azure Portal を使用して Azure 仮想マシン スケール セット管理対象サービス ID (MSI) を構成する)](qs-configure-portal-windows-vmss.md)」を参照してください

Microsoft のコンテンツ改善のため、次のコメント セクションよりご意見をお寄せください。

