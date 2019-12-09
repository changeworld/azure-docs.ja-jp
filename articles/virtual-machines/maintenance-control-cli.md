---
title: Azure 仮想マシンのメンテナンス コントロール
description: メンテナンス コントロールを使用して Azure VM にメンテナンスを適用するタイミングを制御する方法について説明します。
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: f336bd0e208e847dbb24123285066330d8a1ce40
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534935"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>プレビュー:メンテナンス コントロールと Azure CLI を使用して更新を制御する

メンテナンス コントロールを使用して、再起動が不要なプラットフォームの更新を管理します。 Azure は、信頼性、パフォーマンス、セキュリティを改善し、新機能を導入する目的で、インフラストラクチャを頻繁に更新しています。 ほとんどの更新は、ユーザーからは透過的に行われます。 ゲーム、メディア ストリーミング、金融取引などの一部のセンシティブなワークロードでは、数秒間であっても、メンテナンスのために VM がフリーズしたり切断したりすることが許されません。 メンテナンス コントロールが提供するオプションを使用すると、プラットフォームの更新を待機し、35 日間のローリング期間内にそれらの更新を適用できます。 

メンテナンス コントロールを使用すると、分離された VM や Azure Dedicated Host に更新プログラムを適用するタイミングをユーザーが決定できます。

メンテナンス コントロールを使用すると、次のことができます。
- 更新プログラムを 1 つの更新プログラム パッケージにまとめる。
- 最大 35 日間待機して更新プログラムを適用する。 
- Azure Functions を使用して、メンテナンス期間のプラットフォームの更新を自動化する。
- メンテナンス構成が、複数のサブスクリプションやリソース グループ全体で機能するようにする。 

> [!IMPORTANT]
> メンテナンス コントロールは、現在パブリック プレビューの段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 

## <a name="limitations"></a>制限事項

- VM は、[専用ホスト](./linux/dedicated-hosts.md)上にあるか、[分離された VM サイズ](./linux/isolation.md)を使用して作成される必要があります。
- 35 日後に、更新プログラムが自動的に適用されます。
- ユーザーは、**リソース所有者**のアクセス権を持っている必要があります。


## <a name="install-the-maintenance-extension"></a>メンテナンス拡張機能をインストールする

[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) をローカルにインストールする場合は、バージョン 2.0.76 以降が必要です。

`maintenance` プレビュー CLI 拡張機能を、ローカルまたは Cloud Shell にインストールします。 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>メンテナンス構成を作成する

`az maintenance configuration create` を使用して、メンテナンス構成を作成します。 この例では、ホストを対象とした *myConfig* という名前のメンテナンス構成を作成します。 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

後で使用するために、出力から構成 ID をコピーします。

`--maintenanceScope host` を使用すると、ホストに対する更新を制御するために、そのメンテナンス構成が確実に使用されます。

同じ名前の構成を別の場所に作成しようとすると、エラーが発生します。 構成名は、サブスクリプションに対して一意である必要があります。

`az maintenance configuration list` を使用すると、使用可能なメンテナンス構成に対してクエリを実行できます。

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>構成を割り当てる

分離された VM または Azure Dedicated Host に構成を割り当てるには、`az maintenance assignment create` を使用します。

### <a name="isolated-vm"></a>分離された VM

構成の ID を使用して、構成を VM に適用します。 `--resource-type virtualMachines` を指定し、`--resource-name` には VM の名前、`--resource-group` には VM のリソース グループ、`--location` には VM の場所を指定します。 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id '/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig'
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストに構成を適用するには、`--resource-type hosts` と `--resource-parent-name` を含める必要があります。これらには、ホスト グループの名前と `--resource-parent-type hostGroups` を使用します。 

パラメーター `--resource-id` は、ホストの ID です。 [az vm host get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) を使用すると、専用ホストの ID を取得できます。

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>構成を確認する

`az maintenance assignment list` を使用することにより、構成が正しく適用されていることを確認したり、現在適用されている構成を確認したりできます。

### <a name="isolated-vm"></a>分離された VM

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>専用ホスト 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>保留中の更新プログラムを確認する

`az maintenance update list` を使用すると、保留中の更新プログラムがあるかどうかを確認できます。 --subscription が、その VM を含むサブスクリプションの ID になるように更新します。

### <a name="isolated-vm"></a>分離された VM

分離された VM の保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>専用ホスト

専用ホストの保留中の更新プログラムを確認します。 この例では、読みやすくするために出力を表形式で表示しています。 リソースの値は実際の値に置き換えてください。

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>更新プログラムを適用する

`az maintenance apply update` を使用して、保留中の更新プログラムを適用します。

### <a name="isolated-vm"></a>分離された VM

分離された VM に更新プログラムを適用する要求を作成します。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myMaintenanceRG\
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>専用ホスト

専用ホストに更新プログラムを適用します。

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>更新プログラムの適用状態を確認する 

`az maintenance applyupdate get` を使用すると、更新プログラムの進行状況を確認できます。 

### <a name="isolated-vm"></a>分離された VM

`myUpdateName` は、`az maintenance applyupdate create` を実行したときに返された更新プログラムの名前に置き換えます。

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name myUpdateName 
```

### <a name="dedicated-host"></a>専用ホスト

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name default \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>メンテナンス構成を削除する

メンテナンス構成を削除するには、`az maintenance configuration delete` を使用します。 構成を削除すると、関連付けられているリソースからそのメンテナンス コントロールが削除されます。

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>次の手順
詳細については、[メンテナンスと更新プログラム](maintenance-and-updates.md)に関するページを参照してください。