---
title: Azure CLI を使用して Azure portal ダッシュボードを作成する
description: 'クイックスタート: Azure CLI を使用して、Azure portal のダッシュボードを作成する方法について説明します。 ダッシュボードでは、お使いのクラウド リソースが集中的に整理して表示されます。'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.date: 12/4/2020
ms.openlocfilehash: d951c692c7d3c282ae68c5f9b53e9cda5407df10
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481023"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>クイックスタート: Azure CLI を使用して Azure portal ダッシュボードを作成する

Azure portal のダッシュボードでは、お使いのクラウド リソースが集中的に整理して表示されます。 この記事では、Azure CLI を使用してダッシュボードを作成するプロセスに焦点を当てます。
このダッシュボードには、仮想マシン (VM) のパフォーマンスに加え、一部の静的情報とリンクが表示されます。


[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 複数の Azure サブスクリプションをお持ちの場合は、リソースが課金の対象となる適切なサブスクリプションを選択してください。
[az account set](/cli/azure/account#az_account_set) コマンドを使用してサブスクリプションを選択します。

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- [az group create](/cli/azure/group#az_group_create) コマンドを使用して [Azure リソース グループ](../azure-resource-manager/management/overview.md)を作成するか、既存のリソース グループを使用します。

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   リソース グループとは、複数の Azure リソースをまとめてデプロイ、管理する際の論理コンテナーです。

## <a name="create-a-virtual-machine"></a>仮想マシンを作成する

[az vm create](/cli/azure/vm#az_vm_create) コマンドを使用して仮想マシンを作成します。

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> パスワードは複雑なものにする必要があります。
> これは新しいユーザー名とパスワードです。
> たとえば、Azure へのサインインに使用するアカウントではありません。
> 詳細については、[ユーザー名の要件](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm)に関するページと[パスワードの要件](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm)に関するページを参照してください。

デプロイが開始されます。通常、完了するまでに数分かかります。
デプロイが完了したら、次のセクションに進みます。

## <a name="download-the-dashboard-template"></a>ダッシュボード テンプレートをダウンロードする

Azure ダッシュボードはリソースであるため、JSON として表すことができます。
詳細については、「[Azure ダッシュボードの構造](./azure-portal-dashboards-structure.md)」を参照してください。

[portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json) ファイルをダウンロードします。

次の値を実際の値に変更して、ダウンロードしたテンプレートをカスタマイズします。

* `<subscriptionID>`:該当するサブスクリプション
* `<rgName>`:リソース グループ (例: `myResourceGroup`)
* `<vmName>`:仮想マシン名 (例: `SimpleWinVM`)
* `<dashboardTitle>`:ダッシュボードのタイトル (例: `Simple VM Dashboard`)
* `<location>`:Azure リージョン (例: `centralus`)

詳細については、[Microsoft portal ダッシュボード テンプレート リファレンス](/azure/templates/microsoft.portal/dashboards)に関するページを参照してください。

## <a name="deploy-the-dashboard-template"></a>ダッシュボード テンプレートをデプロイする

これで、Azure CLI 内からテンプレートをデプロイできるようになりました。

1. [az portal dashboard create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) コマンドを実行して、テンプレートをデプロイします。

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. [az portal dashboard show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) コマンドを実行して、ダッシュボードが正常に作成されたことを確認します。

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

現在のサブスクリプションのすべてのダッシュボードを表示するには、[az portal dashboard list](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list) を使用します。

```azurecli
az portal dashboard list
```

リソース グループのすべてのダッシュボードを表示することもできます。

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

ダッシュボードを更新するには、[az portal dashboard update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update) コマンドを使用します。

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

[!INCLUDE [azure-portal-review-deployed-resources](../../includes/azure-portal-review-deployed-resources.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

仮想マシンおよび関連付けられているダッシュボードを削除するには、それらが含まれているリソース グループを削除します。

> [!CAUTION]
> 次の例では、指定されたリソース グループとそれに含まれるすべてのリソースを削除します。
> 指定したリソース グループにこの記事の範囲外のリソースが含まれている場合、それらも削除されます。

```azurecli
az group delete --name myResourceGroup
```

ダッシュボードだけを削除するには、[az portal dashboard delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete) コマンドを使用します。

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>次のステップ

ダッシュボードの Azure CLI サポートの詳細については、「[az portal dashboard](/cli/azure/ext/portal/portal/dashboard)」を参照してください。
