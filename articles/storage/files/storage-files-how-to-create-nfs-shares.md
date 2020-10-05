---
title: NFS 共有を作成する - Azure Files
description: ネットワーク ファイル システム プロトコルを使用してマウントできる Azure ファイル共有を作成する方法について説明します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 2a848cb77336fc89172d55a6204d66b9e5be5976
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705233"
---
# <a name="how-to-create-an-nfs-share"></a>NFS 共有を作成する方法

Azure ファイル共有は、クラウド内に存在するフル マネージドのファイル共有です。 サーバー メッセージ ブロック プロトコルまたはネットワーク ファイル システム (NFS) プロトコルのどちらかを使用してアクセスできます。 この記事では、NFS プロトコルを使用するファイル共有の作成について説明します。 両方のプロトコルの詳細については、[Azure ファイル共有のプロトコル](storage-files-compare-protocols.md)に関するページを参照してください。

## <a name="limitations"></a>制限事項

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>前提条件

- [FileStorage アカウント](storage-how-to-create-premium-fileshare.md)を作成します。

    > [!IMPORTANT]
    > NFS 共有には、信頼されたネットワークからしかアクセスできません。 NFS 共有への接続は、次のいずれかのソースから行う必要があります。

    - [プライベート エンドポイントを作成する](storage-files-networking-endpoints.md#create-a-private-endpoint) (推奨) か、または[パブリック エンドポイントへのアクセスを制限](storage-files-networking-endpoints.md#restrict-public-endpoint-access)します。
    - [Azure Files で使用する Linux 上のポイント対サイト (P2S) VPN を構成](storage-files-configure-p2s-vpn-linux.md)します。
    - [Azure Files で使用するサイト間 VPN を構成](storage-files-configure-s2s-vpn.md)します。
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) を構成します。
- Azure CLI を使用する場合は、[最新バージョンをインストールしてください](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。

## <a name="register-the-nfs-41-protocol"></a>NFS 4.1 プロトコルを登録する

Azure PowerShell モジュールまたは Azure CLI を使用している場合は、次のコマンドを使用して機能を登録します。

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares
                    --namespace Microsoft.Storage
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-that-the-feature-is-registered"></a>機能が登録されたことを確認する

登録の承認には、最大 1 時間かかります。 登録が完了したことを確認するには、次のコマンドを使用します。

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="create-an-nfs-share"></a>NFS 共有を作成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

これで FileStorage アカウントが作成され、ネットワークが構成されたので、NFS ファイル共有を作成できます。 このプロセスは SMB 共有の作成に似ていますが、共有を作成するときに **[SMB]** ではなく **[NFS]** を選択します。

1. ストレージ アカウントに移動し、 **[ファイル共有]** を選択します。
1. **[+ ファイル共有]** を選択して、新しいファイル共有を作成します。
1. ファイル共有に名前を付け、プロビジョニングされた容量を選択します。
1. **[プロトコル]** で **[NFS (プレビュー)]** を選択します。
1. **[ルート スカッシュ]** で選択を行います。

    - [ルート スカッシュ] (既定値) - リモート スーパーユーザー (ルート) へのアクセスが UID (65534) と GID (65534) にマップされます。
    - [ルート スカッシュなし] - リモート スーパーユーザー (ルート) はルートとしてアクセスを受信します。
    - [すべてスカッシュ] - すべてのユーザー アクセスが UID (65534) と GID (65534) にマップされます。
    
1. **［作成］** を選択します

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="ファイル共有の作成ブレードのスクリーンショット":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. .NET Framework がインストールされていることを確認します。 「[.NET Framework のダウンロード](https://dotnet.microsoft.com/download/dotnet-framework)」を参照してください。
 
1. 次のコマンドを使用して、インストールされている PowerShell のバージョンが `5.1` 以降であることを確認します。    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   お使いの PowerShell のバージョンをアップグレードするには、「[既存の Windows PowerShell をアップグレードする](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)」を参照してください。
    
1. PowerShellGet モジュールの最新バージョンをインストールします。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. PowerShell コンソールを閉じてから、再度開きます。

1. **Az.Storage** プレビュー モジュール バージョン **2.5.2-preview** をインストールします。

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)」を参照してください。
   
1. Azure PowerShell モジュールを使用して Premium ファイル共有を作成するには、[New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) コマンドレットを使用します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI で Premium ファイル共有を作成するには、[az storage share create](/cli/azure/storage/share-rm) コマンドを使用します。

> [!NOTE]
> プロビジョニングされる共有サイズは共有クォータによって指定され、ファイル共有はプロビジョニングされたサイズで課金されます。 詳細については、 [価格に関するページ](https://azure.microsoft.com/pricing/details/storage/files/)を参照してください。

```azurecli-interactive
az storage share-rm create \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --enabled-protocol NFS \
    --root-access RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>次のステップ

これで NFS 共有が作成されたので、それを使用するには Linux クライアントにマウントする必要があります。 詳細については、[NFS 共有をマウントする方法](storage-files-how-to-mount-nfs-shares.md)に関するページを参照してください。

何か問題が発生した場合は、[Azure NFS ファイル共有のトラブルシューティング](storage-troubleshooting-files-nfs.md)に関するページを参照してください。