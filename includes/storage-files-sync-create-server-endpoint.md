---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 6/01/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 9d8d983b5ac7082ff3037d465f43ea9a2ad47fb4
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129300335"
---
# <a name="portal"></a>[ポータル](#tab/azure-portal)
サーバー エンドポイントを追加するには、新しく作成した同期グループに移動し、 **[サーバー エンドポイントの追加]** を選びます。

![[同期グループ] ウィンドウで新しいサーバー エンドポイントを追加する](media/storage-files-sync-create-server-endpoint/add-server-endpoint.png)

**[サーバー エンドポイントの追加]** ブレードが開き、次の情報を入力してサーバー エンドポイントを作成します。

- **登録済みサーバー**: サーバー エンドポイントを作成するサーバーまたはクラスターの名前。
- **パス**:同期グループの一部として同期される Windows Server のパス。
- **クラウドの階層化**: クラウドの階層化を有効または無効にするスイッチ。 クラウドの階層化によって、使用頻度やアクセス頻度が低いファイルを Azure Files に階層化できます。
- **ボリュームの空き領域**: サーバー エンドポイントが配置されているボリュームに確保する空き領域のサイズ。 たとえば、1 つだけのサーバー エンドポイントがあるボリュームでボリュームの空き領域を 50% に設定すると、データの約半量が Azure Files に階層化されます。 クラウドの階層化が有効かどうかにかかわらず、Azure ファイル共有は、データの完全なコピーを常に同期グループ内に保持します。
- **初期ダウンロード モード**: Azure ファイル共有にはファイルがあるが、サーバー上にはない場合に役立つ可能性がある省略可能な選択。 このような状況は、たとえば、別のブランチ オフィス サーバーを同期グループに追加するためにサーバー エンドポイントを作成する場合や、障害が発生したサーバーをディザスター リカバリーする場合などに起こりうる可能性があります。 クラウドを使った階層化が有効になっている場合、既定では、最初にファイルのコンテンツではなく、名前空間のみが呼び戻されます。 これは、ユーザーのアクセス要求で、サーバーにどのファイルのコンテンツを呼び戻すかを決めるべきだと考えている場合に便利です。 クラウドを使った階層化が無効になっている場合、既定では、名前空間が最初にダウンロードされた後、ローカル容量に到達するまで、最終更新タイムスタンプに基づいてファイルが呼び戻されます。 ただし、初期ダウンロード モードは名前空間のみに変更できます。 3 番目のモードは、このサーバー エンドポイントでクラウドを使った階層化が無効になっている場合にのみ使用できます。 このモードでは、最初に名前空間が呼び戻されることを回避します。 ファイルは、完全にダウンロードされた場合にのみ、ローカル サーバーに表示されます。 このモードは、たとえばアプリケーションでは完全なファイルが必要とされ、名前空間に階層化されたファイルがあることが許容されない場合に便利です。

サーバー エンドポイントを追加するには、 **[作成]** を選びます。 Azure ファイル共有と Windows Server でファイルの同期が維持されます。 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
サーバー エンドポイントを作成するには、次の PowerShell コマンドを実行し、`<your-server-endpoint-path>` と `<your-volume-free-space>` を目的の値に置き換え、オプションの[初期ダウンロード](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-download-section)および[初期アップロード](../articles/storage/file-sync/file-sync-server-endpoint-create.md#initial-sync-section) ポリシーの設定を確認してください。

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = "NamespaceOnly"
$initialUploadPolicy = "Merge"
# Optional property. Choose from: [Merge] default for all new server endpoints. Content from the server and the cloud merge. This is the right choice if one location is empty or other server endpoints already exist in the sync group. [ServerAuthoritative] This is the right choice when you seeded the Azure file share (e.g. with Data Box) AND you are connecting the server location you seeded from. This enables you to catch up the Azure file share with the changes that happened on the local server since the seeding.

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy `
        -InitialUploadPolicy $initialUploadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

新しいサーバー エンドポイントを作成するには、[`az storagesync sync-group server-endpoint`](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) コマンドを使用します。

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --initial-upload-policy Merge [OR] ServerAuthoritative

```

---
