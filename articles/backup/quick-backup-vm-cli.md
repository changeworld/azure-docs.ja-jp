---
title: "Azure クイック スタート - Azure CLI で VM をバックアップする | Microsoft Docs"
description: "Azure CLI を使用して仮想マシンをバックアップする方法"
services: backup, virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines-linux
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: storage-backup-recovery
ms.date: 10/02/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b0820613e5b08895148391837c1d7b28cb207128
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>CLI を使用した Azure での仮想マシンのバックアップ
Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 データは、定期的にバックアップすることで保護することができます。 Azure Backup によって、geo 冗長 Recovery コンテナーに保存できる復元ポイントが作成されます。 この記事では、Azure CLI を使用して Azure で仮想マシン (VM) をバックアップする方法を説明します。 これらの手順は、[Azure PowerShell](quick-backup-vm-powershell.md) または [Azure Portal](quick-backup-vm-portal.md) を介して実行することもできます。

このクイック スタートでは、既存の Azure VM でバックアップを実行できます。 VM を作成する必要がある場合は、[Azure CLI を使用して VM を作成](../virtual-machines/linux/quick-create-cli.md)できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.18 以降を使用する必要があります。 CLI のバージョンを調べるには、༖༗ を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成
Recovery Services コンテナーは、Azure VM などの保護された各リソースのバックアップ データを格納する論理コンテナーです。 保護されたリソースのバックアップ ジョブを実行すると、Recovery Services コンテナー内に復元ポイントが作成されます。 次に、この復旧ポイントのいずれかを使用して、データを特定の時点に復元できます。

[az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault#az_backup_vault_create) を使用して Recovery Services コンテナーを作成します。 保護する VM と同じリソース グループと場所を指定します。 [VM クイック スタート](../virtual-machines/linux/quick-create-cli.md)を使用した場合、次のものが作成されています。

- *myResourceGroup* という名前のリソース グループ
- *myVM* という名前の VM
- *eastus* に置かれた各種リソース

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

既定では、Recovery Services コンテナーが geo 冗長ストレージ用に設定されています。 geo 冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンにバックアップ データがレプリケートされます。


## <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする
バックアップ ジョブをいつ実行し、復旧ポイントをどのくらいの期間格納するかを定義する保護ポリシーを作成します。 既定の保護ポリシーでは、毎日バックアップ ジョブが実行され、復元ポイントは 30 日間保持されます。 ポリシーのこれらの既定値を使用して、VM をすぐに保護できます。 VM のバックアップ保護を有効にするには、[az backup protection enable-for-vm](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_enable_for_vm) を使用します。 保護するリソース グループと VM を指定した後、使用するポリシーを指定します。

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```


## <a name="start-a-backup-job"></a>バックアップ ジョブの開始
スケジュールされた時刻にジョブを実行する既定のポリシーを待機するのではなく、バックアップを今すぐ開始するには、[az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection#az_backup_protection_backup_now) を使用します。 この最初のバックアップ ジョブによって、完全な復旧ポイントが作成されます。 初回のバックアップ後のバックアップ ジョブでは、増分復元ポイントが作成されます。 増分復旧ポイントでは、前回のバックアップ以降に行われた変更のみを転送対象とすることで、高い保存効率と時間効率を実現します。

VM のバックアップには、次のパラメーターが使用されます。

- `--container-name` は VM の名前です
- `--item-name` は VM の名前です
- `--retain-until` 値は、復旧ポイントを利用できるようにする最後の利用可能な日付に UTC 時刻形式 (**dd-mm-yyyy**) で設定します。

次の例では、*myVM* という名前の VM をバックアップし、復旧ポイントの期限を 2017 年 10 月 18 日に設定します。

```azurecli-interactive 
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```


## <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する
バックアップ ジョブの状態を監視するには、[az backup job list](https://docs.microsoft.com/cli/azure/backup/job#az_backup_job_list) を使用します。

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

出力は次の例のようになります。バックアップ ジョブが *InProgress* であることが示されています。

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

バックアップ ジョブの *状態* が *完了* と報告されたら、VM は Recovery Services で保護され、完全な復旧ポイントが格納されています。


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
VM が不要になったら、VM の保護を無効にし、復旧ポイントと Recovery Services コンテナーを削除した後、リソース グループと関連付けられている VM リソースを削除することができます。 既存の VM を使用した場合、リソース グループと VM をそのままの状態にしておくために、最後の [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) コマンドを省略できます。

VM のデータを復元する方法について説明したバックアップ チュートリアルに取り組むには、「[次のステップ](#next-steps)」に進んでください。 

```azurecli-interactive 
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>次のステップ
このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。 Azure Backup と Recovery Services についてさらに学ぶには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [複数の Azure VM をバックアップする](./tutorial-backup-vm-at-scale.md)
