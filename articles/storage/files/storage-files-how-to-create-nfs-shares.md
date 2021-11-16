---
title: NFS 共有を作成する - Azure Files
description: ネットワーク ファイル システム プロトコルを使用してマウントできる Azure ファイル共有を作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 77a8a7d3a210441cea406241ee1f4f776878c826
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519434"
---
# <a name="how-to-create-an-nfs-share"></a>NFS 共有を作成する方法
Azure ファイル共有は、クラウド内に存在するフル マネージドのファイル共有です。 この記事では、NFS プロトコルを使用するファイル共有の作成について説明します。

## <a name="applies-to"></a>適用対象
| ファイル共有の種類 | SMB | NFS |
|-|:-:|:-:|
| Standard ファイル共有 (GPv2)、LRS/ZRS | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Standard ファイル共有 (GPv2)、GRS/GZRS | ![いいえ](../media/icons/no-icon.png) | ![いいえ](../media/icons/no-icon.png) |
| Premium ファイル共有 (FileStorage)、LRS/ZRS | ![いいえ](../media/icons/no-icon.png) | ![はい](../media/icons/yes-icon.png) |

## <a name="limitations"></a>制限事項
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]


### <a name="regional-availability"></a>リージョン別の提供状況
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>前提条件
- NFS 共有は、数値 UID/GID のみを受け入れます。 クライアントが英数字の UID/GID を送信しないようにするには、ID マッピングを無効にします。
- NFS 共有には、信頼されたネットワークからしかアクセスできません。 NFS 共有への接続は、次のいずれかのソースから行う必要があります。
    - [プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint) (推奨) か、または[パブリック エンドポイントへのアクセスを制限](storage-files-networking-endpoints.md#restrict-public-endpoint-access)します。
    - [Azure Files で使用する Linux 上のポイント対サイト (P2S) VPN を構成](storage-files-configure-p2s-vpn-linux.md)します。
    - [Azure Files で使用するサイト間 VPN を構成](storage-files-configure-s2s-vpn.md)します。
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) を構成します。

- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](/cli/azure/install-azure-cli)。

## <a name="create-a-filestorage-storage-account"></a>FileStorage ストレージ アカウントを作成する
現時点では、NFS 4.1 共有は Premium ファイル共有としてのみ使用できます。 NFS 4.1 プロトコル サポートを使用して Premium ファイル共有をデプロイするには、最初に FileStorage ストレージ アカウントを作成する必要があります。 ストレージ アカウントは、Azure の最上位レベルのオブジェクトで、複数の Azure ファイル共有のデプロイに使用できるストレージの共有プールを表します。

# <a name="portal"></a>[ポータル](#tab/azure-portal)
FileStorage ストレージ アカウントを作成するには、Azure portal に移動します。

1. [アカウント] [Azure portal](https://portal.azure.com/)左側の **Storage [アカウント]** を選択します。

    ![Azure portal のメイン ページでストレージ アカウントを選択します。](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 表示された **[ストレージ アカウント]** ウィンドウで **[追加]** を選択します。
1. ストレージ アカウントを作成するサブスクリプションを選択します。
1. ストレージ アカウントを作成するリソース グループを選択します。
1. 次に、ストレージ アカウントの名前を入力します。 選択する名前は Azure 全体で一意である必要があります。 また、名前の長さは 3 から 24 文字とし、数字と小文字のみを使用できます。
1. ストレージ アカウントの場所を選択するか、または既定の場所を使います。
1. **[パフォーマンス]** では **[Premium]** を選択します。

    **[アカウントの種類]** ドロップダウンにオプションとして **[ファイル共有]** を表示するには、 **[Premium]** を選択する必要があります。

1. **[Premium account type]\(Premium アカウントの種類\)** で **[ファイル共有]** を選択します。

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="選択された Premium パフォーマンスのスクリーンショット。":::

1. **[レプリケーション]** の設定は、既定値の **[ローカル冗長ストレージ (LRS)]** のままにします。
1. **[確認および作成]** を選択して、ストレージ アカウントの設定を確認し、アカウントを作成します。
1. **［作成］** を選択します

ストレージ アカウント リソースが作成されたら、そこに移動します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
FileStorage ストレージ アカウントを作成するには、PowerShell プロンプトを開き、次のコマンドを実行します。`<resource-group>` と `<storage-account>` を、実際の環境に合った適切な値に置き換えることを忘れないでください。

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
FileStorage ストレージ アカウントを作成するには、お使いのターミナルを開き、次のコマンドを実行します。`<resource-group>` と `<storage-account>` を、実際の環境に合った適切な値に置き換えることを忘れないでください。

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="disable-secure-transfer"></a>セキュリティで保護された転送を無効にする

安全な転送を無効にしない限り、NFS ファイル共有をマウントできない。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. 作成したストレージ アカウントに移動します。
1. **[構成]** を選択します。
1. **[安全な転送が必須]** には **[無効]** を選択します。
1. **[保存]** を選択します。

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="安全な転送が無効になっているストレージ アカウントの構成画面のスクリーンショット。" lightbox="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $False
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only false
```
---

## <a name="create-an-nfs-share"></a>NFS 共有を作成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

これで FileStorage アカウントが作成され、ネットワークが構成されたので、NFS ファイル共有を作成できます。 このプロセスは SMB 共有の作成に似ていますが、共有を作成するときに **[SMB]** ではなく **[NFS]** を選択します。

1. ストレージ アカウントに移動し、 **[ファイル共有]** を選択します。
1. **[+ ファイル共有]** を選択して、新しいファイル共有を作成します。
1. ファイル共有に名前を付け、プロビジョニングされた容量を選択します。
1. **[プロトコル]** では、 **[SSL]** を選択します。
1. **[ルート スカッシュ]** で選択を行います。

    - [ルート スカッシュ] (既定値) - リモート スーパーユーザー (ルート) へのアクセスが UID (65534) と GID (65534) にマップされます。
    - [ルート スカッシュなし] - リモート スーパーユーザー (ルート) はルートとしてアクセスを受信します。
    - [すべてスカッシュ] - すべてのユーザー アクセスが UID (65534) と GID (65534) にマップされます。
    
1. **［作成］** を選択します

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="ファイル共有の作成ブレードのスクリーンショット。":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Azure PowerShell モジュールを使用して Premium ファイル共有を作成するには、[New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) コマンドレットを使用します。

    > [!NOTE]
    > Premium ファイル共有は、プロビジョニング モデルを使用して課金されます。 共有のプロビジョニングされたサイズは、次の `QuotaGiB` で指定します。 詳細については、[プロビジョニング モデル](understanding-billing.md#provisioned-model)に関するページと [Azure Files の料金ページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI で Premium ファイル共有を作成するには、[az storage share create](/cli/azure/storage/share-rm) コマンドを使用します。

> [!NOTE]
> Premium ファイル共有は、プロビジョニング モデルを使用して課金されます。 共有のプロビジョニングされたサイズは、次の `quota` で指定します。 詳細については、[プロビジョニング モデル](understanding-billing.md#provisioned-model)に関するページと [Azure Files の料金ページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>次のステップ
これで NFS 共有が作成されたので、それを使用するには Linux クライアントにマウントする必要があります。 詳細については、[NFS 共有をマウントする方法](storage-files-how-to-mount-nfs-shares.md)に関するページを参照してください。

何か問題が発生した場合は、[Azure NFS ファイル共有のトラブルシューティング](storage-troubleshooting-files-nfs.md)に関するページを参照してください。
