---
title: Azure CLI を使用して Azure ファイル共有をバックアップする
description: Recovery Services コンテナー内のバックアップされた Azure ファイル共有を、Azure CLI を使用してバックアップする方法について説明します
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844043"
---
# <a name="back-up-azure-file-shares-with-cli"></a>CLI を使用して Azure ファイル共有をバックアップする

Azure コマンド ライン インターフェイス (CLI) では、Azure リソースを管理するためのコマンド ライン エクスペリエンスが提供されます。 これは、Azure リソースを使用するためのカスタム オートメーションを構築するための優れたツールです。 この記事では、Azure CLI を使用した Azure ファイル共有のバックアップ方法について詳しく説明します。 これらの手順は、[Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) または [Azure Portal](backup-afs.md) を介して実行することもできます。

このチュートリアルを最後まで読むと、Azure CLI を使用して以下の操作を実行する方法がわかります。

* Recovery Services コンテナーの作成
* Azure ファイル共有のバックアップを有効にする
* ファイル共有のオンデマンド バックアップをトリガーする

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合は、Azure CLI バージョン 2.0.18 以降を使用する必要があります。 CLI のバージョンを調べるには、`run az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照してください。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Service コンテナーは、すべてのバックアップ項目にわたって統合されたビューと管理機能を提供するエンティティです。 保護されたリソースのバックアップ ジョブを実行すると、Recovery Services コンテナー内に復元ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

Recovery Services コンテナーを作成するには、次の手順に従います。

1. コンテナーはリソース グループに配置されます。 既存のリソース グループがない場合は、[az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) を使用して新しいリソース グループを作成します。 このチュートリアルでは、米国東部リージョンで新しいリソース グループ、*azurefiles* を作成します。

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. [az backup vault create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) コマンドレットを使用して、コンテナーを作成します。 リソース グループに使用したのと同じコンテナーの場所を指定します。

    次の例では、米国東部リージョンに *azurefilesvault* という名前の Recovery Services コンテナーを作成します。

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. コンテナー ストレージに使用する冗長性の種類を指定します。 [ローカル冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs)または [geo 冗長ストレージ](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)を使用できます。

    次の例では、[az backup vault backup-properties set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) コマンドレットを使用して *azurefilesvault* のストレージ冗長オプションを **Georedundant** に設定します。

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    コンテナーが正常に作成されたかどうかを確認するには、[az backup vault show](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) コマンドレットを使用して、コンテナーの詳細を取得します。 次の例では、前の手順で作成した *azurefilesvault* の詳細を表示します。

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    出力は次の応答のようになります。

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Azure ファイル共有のバックアップを有効にする

このセクションでは、バックアップを構成する Azure ファイル共有が既にあることを前提としています。 お持ちでない場合は、[az storage share create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) コマンドを使用して Azure ファイル共有を作成します。

ファイル共有のバックアップを有効にするには、バックアップ ジョブの実行時間と回復ポイントの保存期間を定義する保護ポリシーを作成する必要があります。 [az backup policy create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) コマンドレットを使用してバックアップ ポリシーを作成できます。

次の例では、[az backup protection enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) コマンドレットを使用して、*schedule 1* バックアップ ポリシーを使用して *afsaccount* ストレージ アカウントの *azurefileshare* ファイル共有のバックアップを有効にします。

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

出力の **Name** 属性は、**バックアップの有効化**操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="trigger-an-on-demand-backup-for-file-share"></a>ファイル共有のオンデマンド バックアップをトリガーする

スケジュールされた時刻にバックアップ ポリシーがジョブを実行するのを待たずに、ファイル共有のオンデマンド バックアップをトリガーする場合は、[az backup protection backup-now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) コマンドレットを使用します。

オンデマンド バックアップをトリガーするには、次のパラメーターを定義する必要があります。

* **--container-name** は、ファイル共有がホストされているストレージ アカウントの名前です。 コンテナーの**名前**または**フレンドリ名**を取得するには、[az backup container list](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) コマンドを使用します。
* **--item-name** は、オンデマンド バックアップをトリガーするファイル共有の名前をです。 バックアップ項目の**名前**または**フレンドリ名**を取得するには、[az backup item list](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) コマンドを使用します。
* **--retain-until** は、回復ポイントを保持する日付を指定します。 値は UTC 時刻形式 (dd-mm-yyyy) で設定する必要があります。

次の例では、*afsaccount* ストレージ アカウントの *azuresfiles* ファイル共有のオンデマンド バックアップを、*20-01-2020* まで保持した状態でトリガーします。

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

出力の **Name** 属性は、"オンデマンド バックアップ" 操作のためにバックアップ サービスによって作成されたジョブの名前に対応しています。 ジョブの状態を追跡するには、[az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) コマンドレットを使用します。

## <a name="next-steps"></a>次のステップ

* [CLI を使用して Azure ファイル共有を復元する](restore-afs-cli.md)方法について確認する
* [CLI を使用して Azure ファイル共有のバックアップを管理する](manage-afs-backup-cli.md)方法について確認する
