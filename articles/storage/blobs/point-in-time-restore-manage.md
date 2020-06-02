---
title: ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)
titleSuffix: Azure Storage
description: ポイントインタイム リストア (プレビュー) を使用して、ブロック BLOB を以前の時点の状態に復元する方法について説明します。
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118212"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>ブロック BLOB でポイントインタイム リストアを有効にして管理する (プレビュー)

ポイントインタイム リストア (プレビュー) を使用して、ブロック BLOB を以前の時点の状態に復元することができます。 この記事では、PowerShell を使用してストレージ アカウントでポイントインタイム リストアを有効にする方法について説明します。 また、PowerShell を使用して復元操作を実行する方法についても説明します。

このプレビューの詳細情報および登録方法については、「[ブロック BLOB のポイントインタイム リストア (プレビュー)](point-in-time-restore-overview.md)」を参照してください。

> [!CAUTION]
> ポイントインタイム リストアでは、ブロック BLOB に対する復元操作のみがサポートされます。 コンテナーに対する操作は復元できません。 ポイントインタイム リストアのプレビュー中に [Delete Container](/rest/api/storageservices/delete-container) 操作を呼び出してストレージ アカウントからコンテナーを削除した場合、そのコンテナーは復元操作を使用して復元できません。 プレビュー中は、コンテナーを削除するのではなく、復元する可能性がある場合は個々の BLOB を削除してください。

> [!IMPORTANT]
> ポイントインタイム リストアのプレビューは、非運用環境での使用のみを意図しています。 運用環境のサービス レベル契約(SLA) は現在使用できません。

## <a name="install-the-preview-module"></a>プレビュー モジュールをインストールする

PowerShell を使用して Azure のポイントインタイム リストアを構成するには、まず、Az.Storage PowerShell モジュールのバージョン [1.14.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) をインストールします。 次の手順に従って、プレビュー モジュールをインストールします。

1. **[設定]** の **[アプリと機能]** 設定を使用して、Windows から Azure PowerShell の以前のインストールをアンインストールします。

1. 最新バージョンの PowerShellGet がインストールされていることを確認します。 Windows PowerShell ウィンドウを開き、次のコマンドを実行して最新バージョンをインストールします。

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. PowerShellGet のインストール後、PowerShell ウィンドウを閉じて再び開きます。

1. 最新バージョンの Azure PowerShell をインストールします。

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Az.Storage プレビュー モジュールをインストールします。

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Azure PowerShell のインストールの詳細については、[PowerShellGet を使用した Azure PowerShell のインストール](/powershell/azure/install-az-ps)に関するページを参照してください。

## <a name="enable-and-configure-point-in-time-restore"></a>ポイントインタイム リストアを有効にして構成する

ポイントインタイム リストアを有効にして構成する前に、その前提条件 (論理的な削除、変更フィード、および BLOB のバージョン管理) を有効にします。 これらの各機能を有効にする方法の詳細については、次の記事を参照してください。

- [BLOB の論理的な削除を有効にする](soft-delete-enable.md)
- [変更フィードを有効または無効にする](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [BLOB のバージョン管理を有効にして管理する](versioning-enable.md)

PowerShell で Azure のポイントインタイム リストアを構成するには、Enable-AzStorageBlobRestorePolicy コマンドを呼び出します。 次の例では、論理的な削除を有効にし、論理的な削除の保持期間を設定し、変更フィードを有効にした後、ポイントインタイム リストアを有効にします。 この例を実行する前に、Azure portal または Azure Resource Manager テンプレートを使用して、BLOB のバージョン管理も有効にします。

例を実行するときは、必ず山かっこ内の値を実際の値に置き換えてください。

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>復元操作を実行する

復元操作を開始するには、UTC **DateTime** 値として復元ポイントを指定して、Restore-AzStorageBlobRange コマンドを呼び出します。 復元する BLOB の 1 つ以上の辞書式範囲を指定することも、範囲を省略して、ストレージ アカウントにあるすべてのコンテナー内のすべての BLOB を復元することもできます。 復元操作が完了するまでに数分かかる場合があります。

復元する BLOB の範囲を指定するときは、次の規則に注意してください。

- 開始範囲と終了範囲に指定するコンテナー パターンには、3 文字以上を含める必要があります。 コンテナー名と BLOB 名を区切るために使用されるスラッシュ (/) は、この最小数にはカウントされません。
- 1 回の復元操作につき指定できる範囲は 1 つだけです。
- ワイルドカード文字はサポートされていません。 それらは、標準文字として扱われます。
- 復元操作に渡される範囲に明示的に指定することで、`$root` および `$web` コンテナー内の BLOB を復元できます。 `$root` および `$web` コンテナーは、明示的に指定されている場合にのみ復元されます。 他のシステム コンテナーは復元できません。

### <a name="restore-all-containers-in-the-account"></a>アカウント内のすべてのコンテナーを復元する

ストレージ アカウント内のすべてのコンテナーと BLOB を復元するには、`-BlobRestoreRange` パラメーターを省略して、Restore-AzStorageBlobRange コマンドを呼び出します。 次の例は、ストレージ アカウント内のコンテナーを、現時点から 12 時間前の状態に復元します。

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>1 つの範囲のブロック BLOB を復元する

1 つの範囲の BLOB を復元するには、Restore-AzStorageBlobRange コマンドを呼び出し、`-BlobRestoreRange` パラメーターに対してコンテナーの辞書式範囲と BLOB 名を指定します。 範囲の開始は含まれ、範囲の終了は含まれません。

たとえば、*sample-container* という名前の 1 つのコンテナー内の BLOB を復元するには、*sample-container* で始まり、*sample-container1* で終わる範囲を指定できます。 開始と終了の範囲に指定されたコンテナーが存在している必要はありません。 範囲の終了は除外されるため、ストレージ アカウントに *sample-container1* という名前のコンテナーが含まれていたとしても、*sample-container* という名前のコンテナーだけが復元されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

復元するコンテナー内の BLOB のサブセットを指定するには、スラッシュ (/) を使用して、コンテナー名を BLOB パターンから分離します。 たとえば、次の範囲では、名前が *d* から *f* までの文字で始まる 1 つのコンテナー内の BLOB が選択されます。

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

次に、Restore-AzStorageBlobRange コマンドに範囲を指定します。 `-TimeToRestore` パラメーターに UTC **DateTime** 値を指定して、復元ポイントを指定します。 次の例は、指定された範囲の BLOB を、現時点から 3 日前の状態に復元します。

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>複数の範囲のブロック BLOB を復元する

複数の範囲のブロック BLOB を復元するには、`-BlobRestoreRange` パラメーターに範囲の配列を指定します。 次の例は、*container1* と *container4* の完全な内容を復元します。

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>次のステップ

- [ブロック BLOB のポイントインタイム リストア (プレビュー)](point-in-time-restore-overview.md)
- [論理的な削除](soft-delete-overview.md)
- [変更フィード (プレビュー)](storage-blob-change-feed.md)
- [BLOB のバージョン管理 (プレビュー)](versioning-overview.md)
