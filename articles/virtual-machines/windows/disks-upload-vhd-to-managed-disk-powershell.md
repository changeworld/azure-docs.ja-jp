---
title: Azure PowerShell を使用して Azure に vhd をアップロードする
description: Azure PowerShell を使用して Azure マネージド ディスクに vhd をアップロードする方法について説明します。
author: roygara
ms.author: rogarana
ms.date: 05/06/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 98c0316a3fa513f98031b79eefcedea5a1111539
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266496"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Azure PowerShell を使用して Azure に vhd をアップロードする

この記事では、ご利用のローカル コンピューターから Azure マネージド ディスクに vhd をアップロードする方法について説明します。 以前は、データをストレージ アカウントにステージングし、そのストレージ アカウントを管理するなど、より複雑なプロセスを実行する必要がありました。 現在では、vhd をアップロードするためにストレージ アカウントを管理したり、データをステージングしたりする必要はなくなりました。 代わりに、空のマネージド ディスクを作成し、それに vhd を直接アップロードします。 これにより、オンプレミスの VM を Azure にアップロードする操作が簡単になり、最大 32 TiB の vhd を大きなマネージド ディスクに直接アップロードできます。

Azure の IaaS VM 向けにバックアップ ソリューションを提供している場合は、直接アップロードを使用して顧客のバックアップをマネージド ディスクに復元することをお勧めします。 Azure の外部のマシンから VHD をアップロードする場合、速度はご利用のローカル帯域幅によって異なります。 Azure VM を使用している場合、ご利用の帯域幅は標準 HDD と同じになります。

現在、直接アップロードは、Standard HDD、Standard SSD、および Premium SSD マネージド ディスクでサポートされています。 Ultra SSD ではまだサポートされていません。

## <a name="prerequisites"></a>前提条件

- 最新[バージョンの AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy) をダウンロードします。
- [Azure PowerShell モジュールをインストールします](/powershell/azure/install-Az-ps)。
- ローカルに保存されている vhd ファイル。

## <a name="create-an-empty-managed-disk"></a>空のマネージド ディスクを作成する

ご利用の vhd を Azure にアップロードするには、このアップロード プロセス用に特別に構成された空のマネージド ディスクを作成する必要があります。 作成する前に、それらのディスクについて把握しておく必要がある追加情報があります。

この種類のマネージド ディスクには、2 つの固有の状態があります。

- ReadToUpload。ディスクはアップロードを受け取る準備ができていますが、[Secure Access Signature](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) が生成されていないことを意味します。
- ActiveUpload。ディスクはアップロードを受け取る準備ができており、SAS が生成済みであることを意味します。

これらのいずれかの状態にある間は、実際のディスクの種類に関係なく、マネージド ディスクには [Standard HDD 料金](https://azure.microsoft.com/pricing/details/managed-disks/)が課金されます。 たとえば、P10 は S10 として課金されます。 これは、`revoke-access` がマネージド ディスク上で呼び出される (ディスクを VM にアタッチするために必要な処理) まで当てはまります。

次に、ご利用のローカル シェル上に、アップロード用の空の標準 HDD を作成するには、[New-AzDiskConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) コマンドレットにおいて、 **-CreateOption** パラメーターに **Upload** 設定を指定すると共に、 **-UploadSizeInBytes** パラメーターを指定します。 次に、[New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) を呼び出してディスクを作成します。

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Premium SSD または Standard SSD をアップロードする場合は、**Standard_LRS** を **Premium_LRS** または **StandardSSD_LRS** のいずれかに置き換えます。 Ultra SSD はまだサポートされていません。

これで、アップロード プロセス用に構成された空のマネージド ディスクが作成されました。 vhd をそのディスクにアップロードするには、書き込み可能な SAS をアップロード先とし参照できるように、用意する必要があります。

空のマネージド ディスクの書き込み可能な SAS を生成するには、次のコマンドを使用します。

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>vhd をアップロードする

空のマネージド ディスク用の SAS が用意できたので、それを使用して、マネージド ディスクをアップロード コマンドのアップロード先として設定できます。

AzCopy v10 を使用して、生成した SAS URI を指定してローカルの VHD ファイルをマネージド ディスクにアップロードします。

このアップロードのスループットは、同等の [Standard HDD](disks-types.md#standard-hdd) と同じです。 たとえば、S4 と同等のサイズの場合、最大 60 MiB/秒のスループットが得られます。 ただし、S70 と同等のサイズの場合、最大 500 MiB/秒のスループットが得られます。

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas --blob-type PageBlob
```

アップロード中に SAS の有効期限が切れ、まだ `revoke-access` を呼び出していない場合は、再度 `grant-access` を使用して新しい SAS を取得し、アップロードを続行できます。

アップロードが完了し、ディスクにこれ以上データを書き込む必要がなくなったら、SAS を無効にします。 SAS を無効にすると、マネージド ディスクの状態が変わり、そのディスクを VM にアタッチできるようになります。

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="next-steps"></a>次の手順

vhd をマネージド ディスクに正常にアップロードしたので、ディスクを VM にアタッチして使用を開始できます。

ディスクを VM にアタッチする方法については、この主題に関する記事を参照してください。[PowerShell を使用してデータ ディスクを Windows VM にアタッチする](attach-disk-ps.md)。
