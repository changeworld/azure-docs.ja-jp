---
title: 仮想ファイル システムをプールにマウントする
description: 仮想ファイル システムを Batch プールにマウントする方法について説明します。
ms.topic: how-to
ms.custom: devx-track-csharp
ms.date: 11/11/2021
ms.openlocfilehash: ed83f5771a451f92c69ba5f80de7bfa7d8388778
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132369419"
---
# <a name="mount-a-virtual-file-system-on-a-batch-pool"></a>仮想ファイル システムを Batch プールにマウントする

Azure Batch は、Batch プール内の Windows または Linux 計算ノードへのクラウド ストレージまたは外部ファイル システムのマウントをサポートします。 計算ノードがプールに参加すると、仮想ファイル システムがマウントされ、そのノードのローカル ドライブとして扱われます。 

次のようなファイル システムをマウントできます。 

- Azure Files
- Azure BLOB ストレージ
- [Avere vFXT キャッシュ](../avere-vfxt/avere-vfxt-overview.md)を含むネットワーク ファイル システム (NFS)
- 共通インターネット ファイル システム (CIFS)

この記事では、[Batch Management Library for .NET](/dotnet/api/overview/azure/batch) を使用して、計算ノードのプールに仮想ファイル システムをマウントする方法について説明します。

> [!NOTE]
> 仮想ファイル システムのマウントは、2019 年 8 月 8 日以降に作成された Batch プールでのみサポートされています。 この日付より前に作成された Batch プールでは、この機能はサポートされません。

## <a name="benefits-of-mounting-on-a-pool"></a>プールにマウントする利点

ファイル システムをプールにマウントすると、タスクが大規模なデータ セットからタスクのデータを取得するのではなく、タスクが必要なデータに簡単かつ効率的にアクセスできるようになります。

ムービーのレンダリングなど、一般的なデータ セットへのアクセスを必要とするタスクが複数あるシナリオについて考えてみます。 各タスクは、シーン ファイルから一度に 1 つ以上のフレームをレンダリングします。 シーン ファイルが格納されているドライブをマウントすることで、計算ノードは共有データに簡単にアクセスできるようになります。

さらに、基になるファイル システムは、データに同時にアクセスする計算ノードに必要なパフォーマンスとスケール (スループットと IOPS) に基づいて、個別に選択し、スケーリングすることができます。 たとえば、[Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) の分散型メモリ内キャッシュを使用すると、オンプレミスのソース データにアクセスして、数千単位の同時レンダリング ノードを使用する大規模な映画規模のレンダリングをサポートできます。 代わりに、クラウドベースの BLOB ストレージに既に存在するデータの場合、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を使用して、このデータをローカル ファイル システムとしてマウントすることもできます。 blobfuse は Linux ノード上でのみ使用できますが、[Azure Files](../storage/files/storage-files-introduction.md) に用意されている同様のワークフローは、Windows と Linux の両方で使用できます。

## <a name="mount-a-virtual-file-system-on-a-pool"></a>仮想ファイル システムをプールにマウントする  

プールに仮想ファイル システムをマウントすると、プール内のすべての計算ノードでファイル システムを使用できるようになります。 ファイル システムの構成は、計算ノードがプールに参加するか、再起動するか、再イメージ化されると発生します。

プールにファイル システムをマウントするには、`MountConfiguration` オブジェクトを作成します。 仮想ファイル システムに適したオブジェクト (`AzureBlobFileSystemConfiguration`、`AzureFileShareConfiguration`、`NfsMountConfiguration`、または `CifsMountConfiguration`) を選択します。

すべてのマウント構成オブジェクトには、次の基本パラメーターが必要です。 一部のマウント構成には、使用するファイル システムに固有のパラメーターがあります。詳細については、コード例を参照してください。

- **アカウント名またはソース**:仮想ファイル共有をマウントするには、ストレージ アカウントまたはそのソースの名前が必要です。
- **相対的なマウント パスまたはソース**: 計算ノードにマウントされるファイル システムの場所。`AZ_BATCH_NODE_MOUNTS_DIR` を介してノード上でアクセスできる標準の `fsmounts` ディレクトリの相対的な場所です。 正確な場所は、ノード上の使用されているオペレーティング システムによって変わります。 たとえば、Ubuntu ノード上の物理的な場所は `mnt\batch\tasks\fsmounts` にマップされます。 CentOS ノードでは、場所は `mnt\resources\batch\tasks\fsmounts` にマップされます。
- **マウント オプションまたは blobfuse オプション**:これらのオプションは、ファイル システムをマウントするための特定のパラメーターを示します。

`MountConfiguration` オブジェクトが作成されたら、プールを作成するときにそのオブジェクトを `MountConfigurationList` プロパティに割り当てます。 ファイル システムのマウントは、ノードがプールに参加するか、再起動するか、再イメージ化されると発生します。

ファイル システムがマウントされると、マウントされたファイル システムとログ ファイル (トラブルシューティングとデバッグに役立ちます) の場所を指す環境変数 `AZ_BATCH_NODE_MOUNTS_DIR` が作成されます。 ログ ファイルの詳細については、「[マウント エラーの診断](#diagnose-mount-errors)」セクションを参照してください。  

> [!IMPORTANT]
> プールにマウントされるファイル システムの最大数は 10 です。 詳細とその他の制限については、「[Batch サービスのクォータと制限](batch-quota-limit.md#other-limits)」を参照してください。

## <a name="mount-azure-file-share-with-powershell"></a>PowerShell を使用して Azure ファイル共有をマウントする

[Azure PowerShell](/powershell/) または [Azure Cloud Shell](../cloud-shell/overview.md) を使用して、Batch プールに Azure ファイル共有をマウントすることができます。

# <a name="windows"></a>[Windows](#tab/windows)

1. Azure サブスクリプションにサインインします。

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Batch アカウントのコンテキストを取得します。
    
    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. 次の設定を使用して Batch プールを作成します。 必要に応じて、サンプル値を独自の情報に置き換えてください。

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "Storage-Account-key")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
    ```

1. ドライブの直接パスを使用してマウント ファイルにアクセスします。 次に例を示します。

    ```powershell
    cmd /c "more S:\folder1\out.txt & timeout /t 90 > NULL"
    ```

1. 出力ファイルが正しいか確認します。

1. リモート デスクトップ プロトコル (RDP) または SSH を使用している場合は、`S` ドライブに直接アクセスするための資格情報を追加します。 この Azure Batch エージェントでは、Windows の Azure Batch タスクへのアクセス権のみが付与されます。 RDP 経由でノードに接続する場合、ユーザー アカウントではマウントしているドライブに自動アクセスできません。 

    資格情報を追加するには、`cmdkey` を使用します。 サンプル値は独自の情報に置き換えてください。

    ```powershell
    cmdkey /add:"<storage-account-name>.file.core.windows.net" /user:"Azure\<storage-account-name>" /pass:"<storage-account-key>"
    ```

# <a name="linux"></a>[Linux](#tab/linux)

1. Azure サブスクリプションにサインインします。

    ```powershell
    Connect-AzAccount -Subscription "<subscription-ID>"
    ```

1. Batch アカウントのコンテキストを取得します。

    ```powershell
    $context = Get-AzBatchAccount -AccountName <batch-account-name>
    ```

1. 次の設定を使用して Batch プールを作成します。 必要に応じて、サンプル値を独自の情報に置き換えてください。

    ```powershell
    $fileShareConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSAzureFileShareConfiguration" -ArgumentList @("<Storage-Account-name>", https://<Storage-Account-name>.file.core.windows.net/batchfileshare1, "S", "<Storage-Account-key>", "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp")
    
    $mountConfig = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSMountConfiguration" -ArgumentList @($fileShareConfig)
    
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -MountConfiguration @($mountConfig) -BatchContext $Context
     
    ```

1. 環境変数 `AZ_BATCH_NODE_MOUNTS_DIR` を使用してマウント ファイルにアクセスします。 次に例を示します。

    ```bash
    /bin/bash -c 'more $AZ_BATCH_NODE_MOUNTS_DIR/S/folder1/out.txt; sleep 20s'
    ```

    必要に応じて、直接パスを使用してマウント ファイルにアクセスすることもできます。

1. 出力ファイルが正しいか確認します。

1. RDP または SSH を使用している場合は、手動で `S` ドライブに直接アクセスできます。 パス `/mnt/batch/tasks/fsmounts/S` を使用します。

---

### <a name="troubleshoot-powershell-mounting"></a>PowerShell のマウントのトラブルシューティング

PowerShell または Cloud Shell を使用して Batch プールに Azure ファイル共有をマウントすると、次のエラーが発生する可能性があります。

```text
Mount Configuration Error | An error was encountered while configuring specified mount(s)
Message: System error (out of memory, cannot fork, no more loop devices)
MountConfigurationPath: S
```

このエラーが発生した場合は、関連するログ ファイルを確認するためにノードに RDP または SSH 接続します。 Batch エージェントは、Windows や Linux と異なる方法でマウントを実装します。 Linux では、Batch によってパッケージ `cifs-utils` がインストールされます。 次に、Batch によってマウント コマンドが発行されます。 Windows では、Batch が `cmdkey` を使用して Batch アカウントの資格情報を追加します。 次に、Batch で `net use` によってマウント コマンドが発行されます。 次に例を示します。

```powershell
net use S: \\<storage-account-name>.file.core.windows.net\<fileshare> /u:AZURE\<storage-account-name> <storage-account-key>
```

# <a name="windows"></a>[Windows](#tab/windows)

1. RDP 経由でノードに接続します。

1. ログ ファイル `fshare-S.log` を開きます。 ファイルのパスが `D:\batch\tasks\fsmounts` です。

1. エラー メッセージを確認します。 次に例を示します。

    ```text
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. [Windows サーバー メッセージ ブロック (SMB) での Azure Files の問題のトラブルシューティング](../storage/files/storage-troubleshoot-windows-file-connection-problems.md)を使用して問題をトラブルシューティングします。

# <a name="linux"></a>[Linux](#tab/linux)

1. SSH 経由でノードに接続します。

1. ログ ファイル `fshare-S.log` を開きます。 ファイルのパスが `/mnt/batch/tasks/fsmounts` です。

1. エラー メッセージを確認します。 たとえば、「 `mount error(13): Permission denied` 」のように入力します。

1. [Linux での Azure Files に関する問題のトラブルシューティング (SMB)](../storage/files/storage-troubleshoot-linux-file-connection-problems.md) を使用して問題をトラブルシューティングします。

---

RDP または SSH を使用してノード上のログ ファイルを確認できない場合は、Batch ログを直接確認します。 この方法は、Windows と Linux の両方のログに使用します。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索バーに **Batch アカウント** を入力して、選択します。

1. **Batch アカウント** のページで、お使いの Batch プールがあるアカウントを選択します。

1. Batch アカウント ページのメニューの **[機能]** の下で、 **[プール]** を選択します。

1. プールの名前を選択します。

1. Batch プール ページのメニューの **[全般]** の下で、 **[ノード]** を選択します。

1. ノードの名前を選択します。

1. ノードの **[概要]** ページで、 **[Batch ログのアップロード]** を選択します。

1. **[Batch ログのアップロード]** ペインで、Azure Storage コンテナーを選択します。 その後、 **[ストレージ コンテナーを選択します]** を選択します。

1. ストレージ コンテナーからログ ファイルを選択してダウンロードします。 

1. `agent-debug.log`を開きます。

1. エラー メッセージを確認します。 次に例を示します。 

    ```text
    ..20210322T113107.448Z.00000000-0000-0000-0000-000000000000.ERROR.agent.mount.filesystems.basefilesystem.basefilesystem.py.run_cmd_persist_output_async.59.2912.MainThread.3580.Mount command failed with exit code: 2, output:
    
    CMDKEY: Credential added successfully.
    
    System error 86 has occurred.
    
    The specified network password is not correct.
    ```

1. 「[Windows での Azure Files に関する問題のトラブルシューティング (SMB)](../storage/files/storage-troubleshoot-windows-file-connection-problems.md)」または「[Linux での Azure Files に関する問題のトラブルシューティング](../storage/files/storage-troubleshoot-linux-file-connection-problems.md)」を使用して問題をトラブルシューティングします。

それでも失敗の原因が見つからない場合は、代わりに [PowerShell を使用してファイル共有を手動でマウントする](#manually-mount-file-share-with-powershell)ことができます。

### <a name="manually-mount-file-share-with-powershell"></a>PowerShell を使用してファイル共有を手動でマウントする

PowerShell でマウント エラーを診断または修正できない場合は、ファイル共有を手動でマウントできます。

# <a name="windows"></a>[Windows](#tab/windows)

1. マウント構成を使用せずにプールを作成します。 次に例を示します。

    ```powershell
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("WindowsServer", "MicrosoftWindowsServer", "2016-Datacenter", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.windows amd64")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1  -BatchContext $Context
    ```

1. ノードが **アイドル** 状態になるまで待ちます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索バーに **ストレージ アカウント** を入力して、選択します。

1. お使いのファイル共有を含むストレージ アカウントの名前を選択します。

1. ストレージ アカウント ページのメニューの **[データ ストレージ]** で **[ファイル共有]** を選びます。

1. **[ファイル共有]** ページで、ファイル共有の名前を選択します。

1. ファイル共有の **[概要]** ページで **[接続]** を選択します。

1. **[接続]** ペインで、 **[Windows]** タブを選択します。

1. **[ドライブ文字]** に、使用するドライブを入力します。 既定値は、`Z` です。

1. **[認証方法]** で、ファイル共有への接続方法を選択します。

1. ファイル共有をマウントするための PowerShell コマンドをコピーします。

1. RDP 経由でノードに接続します。

1. コピーしたコマンドを実行して、ファイル共有をマウントします。

1. 出力でエラー メッセージを確認します。 この情報を使用して、ネットワーク関連の問題のトラブルシューティングを行います。

# <a name="linux"></a>[Linux](#tab/linux)


1. マウント構成を使用せずにプールを作成します。 次に例を示します。

    ```bash
    $imageReference = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("ubuntuserver", "canonical", "20.04-lts", "latest")
    
    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageReference, "batch.node.ubuntu 20.04")
    
    New-AzBatchPool -Id "<Pool-Name>" -VirtualMachineSize "STANDARD_D2_V2" -VirtualMachineConfiguration $configuration -TargetDedicatedComputeNodes 1 -BatchContext $Context
    ```

1. ノードが **アイドル** 状態になるまで待ちます。

1. [Azure portal](https://portal.azure.com) にサインインします。

1. 検索バーに **ストレージ アカウント** を入力して、選択します。

1. お使いのファイル共有を含むストレージ アカウントの名前を選択します。

1. ストレージ アカウント ページのメニューの **[データ ストレージ]** で **[ファイル共有]** を選びます。

1. **[ファイル共有]** ページで、ファイル共有の名前を選択します。

1. ファイル共有の **[概要]** ページで **[接続]** を選択します。

1. **[接続]** ペインで、 **[Linux]** タブを選択します。

1. 使用する **マウント ポイント** を入力します。

1. ファイル共有をマウントするための Linux コマンドをコピーします。

1. SSH 経由でノードに接続します。

1. コピーしたコマンドを実行して、ファイル共有をマウントします。

1. 出力でエラー メッセージを確認します。 この情報を使用して、ネットワーク関連の問題のトラブルシューティングを行います。


---

## <a name="examples"></a>例

次のコード例は、さまざまなファイル共有を計算ノードのプールにマウントする方法を示しています。

### <a name="azure-files-share"></a>Azure Files 共有

Azure Files は、標準の Azure クラウド ファイル システム オファリングです。 コード サンプルのパラメーターに関する詳細については、[Azure Files 共有の使用 (SMB)](../storage/files/storage-how-to-use-files-windows.md) または [Azure Files 共有の使用 (NFS)](../storage/files/storage-files-how-to-create-nfs-shares.md) に関するページを参照してください。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureFileShareConfiguration = new AzureFileShareConfiguration
            {
                AccountName = "{storage-account-name}",
                AzureFileUrl = "https://{storage-account-name}.file.core.windows.net/{file-share-name}",
                AccountKey = "{storage-account-key}",
                RelativeMountPath = "S",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,sec=ntlmssp"
            },
        }
    }
}
```

### <a name="azure-blob-container"></a>Azure BLOB コンテナー

もう 1 つの選択肢は、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を介して Azure Blob ストレージを使用することです。 BLOB ファイル システムをマウントするには、お使いのストレージ アカウントにアクセスできる、`AccountKey`、`SasKey` または `Managed Identity` が必要です。

<<<<<<< HEAD
もう 1 つの選択肢は、[blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md) を介して Azure Blob Storage を使用することです。 BLOB ファイル システムをマウントするには、ストレージ アカウントに `AccountKey` または `SasKey` が必要です。 これらのキーの取得については、「[ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)」または「[Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)」を参照してください。 blobfuse の使用に関する詳細とヒントについては、「blobfuse」を参照してください。
=======
これらのキーを取得する方法の詳細については、以下を参照してください。 

- [ストレージ アカウント アクセス キーを管理する](../storage/common/storage-account-keys-manage.md)
- [共有アクセス署名 (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する](../storage/common/storage-sas-overview.md)
- [Batch プールでマネージド ID を構成する](managed-identity-pools.md) 

blobfuse の使用に関する詳細とヒントについては、[blobfuse プロジェクト](https://github.com/Azure/azure-storage-fuse)に関するページを参照してください。
>>>>>>> repo_sync_working_branch

blobfuse でマウントされたディレクトリへの既定のアクセスを取得するには、**Administrator** としてタスクを実行します。 blobfuse によってディレクトリはユーザー空間にマウントされ、プールの作成時にルートとしてマウントされます。 Linux では、すべての **Administrator** タスクがルートになります。 FUSE モジュールのすべてのオプションについては、[FUSE のリファレンス ページ](https://manpages.ubuntu.com/manpages/xenial/man8/mount.fuse.8.html)で説明されています。

[トラブルシューティングの FAQ](https://github.com/Azure/azure-storage-fuse/wiki/3.-Troubleshoot-FAQ)に関する記事で、blobfuse の使用に関する詳細とヒントについて確認してください。 また、[blobfuse リポジトリの GitHub の問題](https://github.com/Azure/azure-storage-fuse/issues)に関する記事を参照して、現在の blobfuse の問題と解決策を確認することもできます。

> [!NOTE]
> 次の例では、`AccountKey`、`SasKey` および `IdentityReference` が示されていますが、これらは相互に排他的で、1 つしか指定できません。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            AzureBlobFileSystemConfiguration = new AzureBlobFileSystemConfiguration
            {
                AccountName = "StorageAccountName",
                ContainerName = "containerName",
                AccountKey = "StorageAccountKey",
                SasKey = "SasKey",
                IdentityReference = new ComputeNodeIdentityReference("/subscriptions/SUB/resourceGroups/RG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/identity-name"),
                RelativeMountPath = "RelativeMountPath",
                BlobfuseOptions = "-o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120 "
            },
        }
    }
}
```

> [!TIP]
>マネージド ID を使用する場合は、マウントを実行する VM で使用できるように、ID が確実に[プールに割り当てられている](managed-identity-pools.md)ようにします。 ID が正常に機能するには、`Storage Blob Data Contributor` ロールを持っている必要があります。

### <a name="network-file-system"></a>ネットワーク ファイル システム

ネットワーク ファイル システム (NFS) をプール ノードにマウントして、Azure Batch が従来のファイル システムにアクセスできるようにすることができます。 この設定は、クラウドにデプロイされる単一の NFS サーバー、または仮想ネットワーク経由でアクセスされるオンプレミスの NFS サーバーの場合があります。 NFS マウントは [Avere vFXT](../avere-vfxt/avere-vfxt-overview.md) をサポートしています。 Avere vFXT は、データを集中的に使用するハイパフォーマンス コンピューティング (HPC) タスクおよびその他の標準的な NFS 準拠インターフェイス用の分散型メモリ内キャッシュ ソリューションです。 たとえば、[Azure BLOB の NFS](../storage/blobs/network-file-system-protocol-support.md) や、[Azure Files の NFS](../storage/files/storage-files-how-to-mount-nfs-shares.md) などです。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            NfsMountConfiguration = new NFSMountConfiguration
            {
                Source = "source",
                RelativeMountPath = "RelativeMountPath",
                MountOptions = "options ver=3.0"
            },
        }
    }
}
```

### <a name="common-internet-file-system"></a>共通インターネット ファイル システム

プール ノードへの[共通インターネット ファイル システム (CIFS)](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) のマウントは、従来のファイル システムへのアクセスを提供するもう 1 つの方法です。 CIFS は、ネットワーク サーバーのファイルとサービスを要求するためのオープンなクロスプラットフォーム メカニズムを提供するファイル共有プロトコルです。 CIFS は、インターネットおよびイントラネット ファイル共有用の [SMB プロトコル](/windows-server/storage/file-server/file-server-smb-overview)の拡張バージョンに基づいています。

```csharp
new PoolAddParameter
{
    Id = poolId,
    MountConfiguration = new[]
    {
        new MountConfiguration
        {
            CifsMountConfiguration = new CIFSMountConfiguration
            {
                Username = "StorageAccountName",
                RelativeMountPath = "cifsmountpoint",
                Source = "source",
                Password = "StorageAccountKey",
                MountOptions = "-o vers=3.0,dir_mode=0777,file_mode=0777,serverino"
            },
        }
    }
}
```

## <a name="diagnose-mount-errors"></a>マウント エラーの診断

マウント構成が失敗した場合、プール内の計算ノードは失敗し、ノードの状態は `unusable` に設定されます。 マウント構成エラーを診断するには、[`ComputeNodeError`](/rest/api/batchservice/computenode/get#computenodeerror) プロパティでエラーの詳細を調べます。

デバッグのためにログ ファイルを取得するには、[OutputFiles](batch-task-output-files.md) を使用して `*.log` ファイルをアップロードします。 `*.log` ファイルには、`AZ_BATCH_NODE_MOUNTS_DIR` の場所にあるファイル システムのマウントに関する情報が含まれています。 マウント ログ ファイルの形式は、マウントごとに `<type>-<mountDirOrDrive>.log` となります。 たとえば、`test` というマウント ディレクトリの `cifs` マウントでは、`cifs-test.log` という名前のマウント ログ ファイルが作成されます。

## <a name="support-matrix"></a>サポート マトリックス

Azure Batch では、それぞれのパブリッシャーとオファー用に生成されるノード エージェントに対して、次の仮想ファイル システムの種類がサポートされています。

| OS の種類 | Azure Files 共有 | Azure BLOB コンテナー | NFS マウント | CIFS マウント |
|---|---|---|---|---|
| Linux | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Windows | :heavy_check_mark: | :x: | :x: | :x: |

## <a name="networking-requirements"></a>ネットワーク要件

[仮想ネットワーク内の Azure Batch プール](batch-virtual-network.md)で仮想ファイル マウントを使用するときは、次の要件を考慮して、必要なトラフィックがブロックされないようにしてください。

- **Azure ファイル共有**:
  - "storage" サービス タグとの間のトラフィックに TCP ポート 445 を開く必要があります。 詳細については、[Windows での Azure ファイル共有の使用](../storage/files/storage-how-to-use-files-windows.md#prerequisites)に関する記事を参照してください。
- **Azure BLOB コンテナー**:
  - "storage" サービス タグとの間のトラフィックに TCP ポート 443 を開く必要があります。
  - blobfuse および gpg パッケージをダウンロードするために、VM が https://packages.microsoft.com にアクセスできる必要があります。 構成によっては、追加のパッケージをダウンロードするために他の URL へのアクセスも必要な場合があります。
- **Network File System (NFS)** :
  - ポート 2049 (既定値。構成によって他の要件になる場合もあります) へのアクセスが必要です。
  - `nfs-common` (Debian または Ubuntu 用)、または `nfs-utils` (CentOS 用) のパッケージをダウンロードするには、VM が適切なパッケージ マネージャーにアクセスできる必要があります。 この URL は、使用している OS バージョンによって異なる場合があります。 構成によっては、追加のパッケージをダウンロードするために他の URL へのアクセスも必要な場合があります。
  - NFS 経由で Azure BLOB または Azure Files をマウントする場合は、さらにネットワーク要件が増える場合があります。 たとえば、ストレージ アカウントと同じ仮想ネットワークのサブネットを共有する計算ノードが必要な場合があります。
- **共通インターネット ファイル システム (CIFS)** :
  - TCP ポート 445 へのアクセスが必要です。
  - `cifs-utils` パッケージをダウンロードするには、VM が適切なパッケージ マネージャーにアクセスできる必要があります。 この URL は、使用している OS バージョンによって異なる場合があります。

## <a name="next-steps"></a>次のステップ

- [Windows](../storage/files/storage-how-to-use-files-windows.md) または [Linux](../storage/files/storage-how-to-use-files-linux.md) で Azure Files 共有をマウントする方法の詳細について説明します。
- [blobfuse](https://github.com/Azure/azure-storage-fuse) 仮想ファイル システムの使用とマウントについて説明します。
- NFS とそのアプリケーションの詳細については、「[ネットワーク ファイル システムの概要](/windows-server/storage/nfs/nfs-overview)」を参照してください。
- CIFS の詳細については、「[Microsoft SMB プロトコルと CIFS プロトコルの概要](/windows/desktop/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)」を参照してください。