---
title: ファイルと ACL 用の Azure Data Lake Storage Gen2 (プレビュー)
description: PowerShell のコマンドレットを使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、ディレクトリのアクセス制御リスト (ACL) を管理します。
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 983ae646db5f51f7efaa2ff2569133e20e2d1dbd
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/10/2020
ms.locfileid: "75834962"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>PowerShell を使用して Azure Data Lake Storage Gen2 のディレクトリ、ファイル、ACL を管理する (プレビュー)

この記事では、PowerShell を使用して、階層型名前空間 (HNS) が有効なストレージ アカウントでディレクトリ、ファイル、アクセス許可を作成および管理する方法について説明します。 

> [!IMPORTANT]
> この記事で紹介されている PowerShell モジュールは現在パブリック プレビュー中です。

[Gen1 から Gen2 へのマッピング](#gen1-gen2-map) | [フィードバックの送信](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>前提条件

> [!div class="checklist"]
> * Azure サブスクリプション。 [Azure 無料試用版の取得](https://azure.microsoft.com/pricing/free-trial/)に関するページを参照してください。
> * 階層型名前空間 (HNS) が有効になっているストレージ アカウント。 作成するには、[こちら](data-lake-storage-quickstart-create-account.md)の手順に従います。
> * .NET Framework は 4.7.2 以降がインストールされていること。 「[.NET Framework のダウンロード](https://dotnet.microsoft.com/download/dotnet-framework)」を参照してください。
> * PowerShell バージョン `5.1` 以降。

## <a name="install-powershell-modules"></a>PowerShell モジュールをインストールする

1. 次のコマンドを使用して、インストールされている PowerShell のバージョンが `5.1` 以降であることを確認します。 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    お使いの PowerShell のバージョンをアップグレードするには、「[既存の Windows PowerShell をアップグレードする](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)」を参照してください。
    
2. 最新の **PowershellGet** モジュールをインストールします。 次に、PowerShell コンソールを閉じてから再度開きます。

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  **Az.Storage** プレビュー モジュールをインストールします。

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    PowerShell モジュールのインストール方法の詳細については、「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)」を参照してください。

## <a name="connect-to-the-account"></a>アカウントに接続する

Windows PowerShell コマンド ウィンドウを開き、`Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従います。

```powershell
Connect-AzAccount
```

自分の ID が複数のサブスクリプションに関連付けられている場合は、アクティブなサブスクリプションを、ディレクトリを作成して管理するストレージ アカウントのサブスクリプションに設定します。 この例では、`<subscription-id>` プレースホルダーの値をサブスクリプションの ID に置き換えます。

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

次に、コマンドでストレージ アカウントに対する承認を取得する方法を選択します。 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>オプション 1: Azure Active Directory (AD) を使用して承認を取得する

この方法では、システムによって、ユーザー アカウントに適切なロールベースのアクセス制御 (RBAC) の割り当てと ACL のアクセス許可が付与されます。 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>オプション 2:ストレージ アカウント キーを使用して承認を取得する

この方法では、リソースの RBAC アクセス許可または ACL アクセス許可はシステムによってチェックされません。

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>ファイル システムを作成する

ファイル システムは、ファイルのコンテナーとして機能します。 `New-AzDatalakeGen2FileSystem` コマンドレットを使用して、ファイル システムを作成できます。 

この例では、`my-file-system` という名前のファイル システムを作成します。

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>ディレクトリを作成する

`New-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリ参照を作成します。 

この例では、`my-directory` という名前のディレクトリをファイル システムに追加します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

この例では、同じディレクトリを追加しますが、アクセス許可、umask、プロパティ値、メタデータ値も設定します。 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>ディレクトリのプロパティを表示する

この例では、`Get-AzDataLakeGen2Item` コマンドレットを使用してディレクトリを取得し、次にプロパティ値をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>ディレクトリの名前変更または移動

`Move-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリの名前を変更するか、ディレクトリを移動します。

この例では、ディレクトリの名前を `my-directory` から `my-new-directory` に変更します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

この例では、`my-directory` という名前のディレクトリを `my-directory-2` の (`my-subdirectory` という名前の) サブディレクトリに移動します。 この例では、そのサブディレクトリに umask も適用しています。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>ディレクトリを削除する

`Remove-AzDataLakeGen2Item` コマンドレットを使用してディレクトリを削除します。

この例では、`my-directory` という名前のディレクトリを削除します。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

`-Force` パラメーターを使用すると、プロンプトを表示せずにファイルを削除できます。

## <a name="download-from-a-directory"></a>ディレクトリからダウンロードする

`Get-AzDataLakeGen2ItemContent` コマンドレットを使用して、ディレクトリからファイルをダウンロードします。

この例では、`my-directory` という名前のディレクトリから `upload.txt` という名前のファイルをダウンロードします。 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>ディレクトリの内容を一覧表示する

`Get-AzDataLakeGen2ChildItem` コマンドレットを使用して、ディレクトリの内容を一覧表示します。

この例では、`my-directory` という名前のディレクトリの内容を一覧表示します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

この例では、`ACL`、`Permissions`、`Group`、および `Owner` の各プロパティの値は返されません。 これらの値を取得するには、`-FetchPermission` パラメーターを使用します。 

次の例では、同じディレクトリの内容を一覧表示しますが、`-FetchPermission` パラメーターを使用して、`ACL`、`Permissions`、`Group`、および `Owner` の各プロパティの値を返します。 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

ファイル システムの内容を一覧表示するには、コマンドの `-Path` パラメーターを省略します。

## <a name="upload-a-file-to-a-directory"></a>ファイルをディレクトリにアップロードする

`New-AzDataLakeGen2Item` コマンドレットを使用して、ファイルをディレクトリにアップロードします。

この例では、`upload.txt` という名前のファイルを `my-directory` という名前のディレクトリにアップロードします。 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

この例では、同じファイルをアップロードしますが、その後に対象ファイルのアクセス許可、umask、プロパティ値、メタデータ値を設定します。 また、この例では、これらの値をコンソールに出力します。

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>ファイルのプロパティを表示する

この例では、`Get-AzDataLakeGen2Item` コマンドレットを使用してファイルを取得し、次にプロパティ値をコンソールに出力します。

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>ファイルを削除する

`Remove-AzDataLakeGen2Item` コマンドレットを使用してファイルを削除します。

この例では、`upload.txt` という名前のファイルを削除します。 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

`-Force` パラメーターを使用すると、プロンプトを表示せずにファイルを削除できます。

## <a name="manage-access-permissions"></a>アクセス許可を管理する

ディレクトリとファイルのアクセス許可を取得、設定、更新できます。

> [!NOTE]
> Azure Active Directory (Azure AD) を使用してコマンドを承認している場合は、セキュリティ プリンシパルに [Storage BLOB データ所有者ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)が割り当てられていることを確認してください。 ACL アクセス許可の適用方法とその変更による影響の詳細については、「[Azure Data Lake Store Gen2 のアクセス制御](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)」を参照してください。

### <a name="get-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を取得する

`Get-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリまたはファイルの ACL を取得します。

この例では、**ディレクトリ**の ACL を取得し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

この例では、**ファイル**の ACL を取得し、その ACL をコンソールに出力します。

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

次の画像は、ディレクトリの ACL を取得した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

この例では、所有ユーザーには読み取り、書き込み、実行のアクセス許可があります。 所有グループには、読み取りと実行のアクセス許可のみがあります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="set-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を設定する

`New-AzDataLakeGen2ItemAclObject` コマンドレットを使用して、所有ユーザー、所有グループ、またはその他のユーザーの ACL を作成します。 その後、`Update-AzDataLakeGen2Item` コマンドレットを使用して ACL をコミットします。

この例では、所有ユーザー、所有グループ、または他のユーザーの**ディレクトリ**に ACL を設定し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
この例では、所有ユーザー、所有グループ、または他のユーザーの**ファイル**に ACL を設定し、その ACL をコンソールに出力します。

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

次の画像は、ファイルの ACL を設定した後の出力を示しています。

![ACL 出力を取得する](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

この例では、所有ユーザーと所有グループには、読み取りと書き込みのアクセス許可のみがあります。 他のすべてのユーザーには、書き込みと実行のアクセス許可があります。 アクセス制御リストの詳細については、「[Azure Data Lake Storage Gen2 のアクセス制御](data-lake-storage-access-control.md)」を参照してください。

### <a name="update-directory-and-file-permissions"></a>ディレクトリとファイルのアクセス許可を更新する

`Get-AzDataLakeGen2Item` コマンドレットを使用して、ディレクトリまたはファイルの ACL を取得します。 次に、`New-AzDataLakeGen2ItemAclObject` コマンドレットを使用して、新しい ACL エントリを作成します。 新しい ACL を適用するには、`Update-AzDataLakeGen2Item` コマンドレットを使用します。

この例では、ディレクトリに対する書き込み権限と実行権限をユーザーに与えます。

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the directory ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew  

```

この例では、ファイルに対する書き込み権限と実行権限をユーザーに与えます。

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the file ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $aclnew 

```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>ファイル システム内のすべての項目に対するアクセス許可を設定する

`Get-AzDataLakeGen2Item` パラメーターと `-Recurse` パラメーターを `Update-AzDataLakeGen2Item` コマンドレットと共に使用すると、ファイル システム内のすべてのディレクトリとファイルの ACL を再帰的に設定できます。 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Gen1 から Gen2 へのマッピング

次の表は、Data Lake Storage Gen1 で使用するコマンドレットが、Data Lake Storage Gen2 のコマンドレットにどのように対応するかを示しています。

|Data Lake Storage Gen1 のコマンドレット| Data Lake Storage Gen2 のコマンドレット| メモ |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|既定では、Get-AzDataLakeGen2ChildItem コマンドレットは、最初のレベルの子項目のみを一覧表示します。 -Recurse パラメーターは、子項目を再帰的に一覧表示します。 |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item コマンドレットの出力項目には、次のプロパティが含まれます。Acl、Owner、Group、Permission です。|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent コマンドレットは、ファイルのコンテンツをローカル ファイルにダウンロードします。|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|このコマンドレットは、ローカル ファイルから新しいファイルのコンテンツをアップロードします。|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item コマンドレットは、再帰的ではなく、1 つの項目のみを更新します。 再帰的に更新する場合は、Get-AzDataLakeStoreChildItem コマンドレットを使用して項目を一覧表示して、Update-AzDataLakeGen2Item コマンドレットに渡します。|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|項目が存在しない場合、Get-AzDataLakeGen2Item コマンドレットではエラーを報告します。|



## <a name="see-also"></a>参照

* [既知の問題](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Azure Storage における Azure PowerShell の使用](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。
* [Storage PowerShell コマンドレット](/powershell/module/az.storage)。

