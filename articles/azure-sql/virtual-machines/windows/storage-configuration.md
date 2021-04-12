---
title: SQL Server VM のストレージを構成する | Microsoft Docs
description: このトピックでは、プロビジョニング中に SQL Server VM のストレージが Azure によってどのように構成されるかを説明します (Azure Resource Manager デプロイ モデル)。 また、既存の SQL Server VM のストレージを構成する方法についても説明します。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/26/2019
ms.author: mathoma
ms.openlocfilehash: 982bd9239c5e95c9b7af09b5f54c5a09067ca7c6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105565428"
---
# <a name="configure-storage-for-sql-server-vms"></a>SQL Server VM のストレージを構成する
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure Virtual Machines (VM) で SQL Server 用にストレージを構成する方法について説明します。

マーケットプレース イメージを使用してデプロイされた SQL Server VM は、デプロイ時に変更できる既定の[ストレージのベスト プラクティス](performance-guidelines-best-practices-storage.md)に自動的に従います。 これらの構成設定の一部は、デプロイ後に変更できます。 


## <a name="prerequisites"></a>前提条件

自動化されたストレージ構成設定を使用するには、仮想マシンには次の特性が必要です。

* [SQL Server のギャラリー イメージ](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo)を使用してプロビジョニングされているか、[SQL IaaS 拡張機能]()を使用して登録されている。
* [Resource Manager デプロイ モデル](../../../azure-resource-manager/management/deployment-models.md)を使用している。
* [premium SSD](../../../virtual-machines/disks-types.md) を使用している。

## <a name="new-vms"></a>新しい VM

次のセクションでは、新しい SQL Server 仮想マシンのストレージを構成する方法について説明します。

### <a name="azure-portal"></a>Azure portal

SQL Server のギャラリー イメージを使用して Azure VM をプロビジョニングするときは、 **[SQL Server の設定]** タブの **[構成の変更]** を選択して、パフォーマンス最適化ストレージの構成ページを開きます。 既定値のままにすることも、ワークロードに基づいてご自分のニーズに最適なディスク構成の種類に変更することもできます。 

![[SQL Server の設定] タブと [構成の変更] オプションが強調表示されているスクリーンショット。](./media/storage-configuration/sql-vm-storage-configuration-provisioning.png)

**[ストレージの最適化]** では、SQL Server をデプロイする対象のワークロードの種類を選択します。 **[全般]** 最適化オプションの既定では、最大 5,000 IOPS のデータ ディスクが 1 つ使用され、この同じドライブをデータ、トランザクション ログ、TempDB ストレージに使用します。 

**[トランザクション処理]** (OLTP) または **[データ ウェアハウス]** を選択すると、データ用とトランザクション ログ用にそれぞれ個別のディスクが作成され、TempDB 用にはローカル SSD が使用されます。 **[トランザクション処理]** と **[データ ウェアハウス]** でストレージに違いはありませんが、[ストライプの構成とトレース フラグ](#workload-optimization-settings)が変更されます。 Premium Storage を選択すると、[SQL Server VM のパフォーマンスのベスト プラクティス](performance-guidelines-best-practices.md)に関する記事に従って、キャッシュがデータ ドライブについては "*読み取り専用*" に、ログ ドライブについては "*なし*" に設定されます。 

![プロビジョニング中の SQL Server VM ストレージの構成](./media/storage-configuration/sql-vm-storage-configuration.png)

ディスク構成は完全にカスタマイズ可能であるため、SQL Server VM のワークロードに必要なストレージ トポロジ、ディスクの種類、IOPS を構成できます。 また、SQL Server VM がサポートされているリージョン (米国東部 2、東南アジア、北ヨーロッパ) にあり、[サブスクリプションで Ultra Disks](../../../virtual-machines/disks-enable-ultra-ssd.md) を有効にしている場合は、 **[ディスクの種類]** のオプションとして UltraSSD (プレビュー) を使用することもできます。  

さらに、ディスクのキャッシュを設定することもできます。 [Premium ディスク](../../../virtual-machines/disks-types.md#premium-ssd)で使用する場合、Azure VM には [BLOB キャッシュ](../../../virtual-machines/premium-storage-performance.md#disk-caching)と呼ばれる多層キャッシュ テクノロジがあります。 BLOB キャッシュでは、仮想マシンの RAM とローカル SSD の組み合わせがキャッシュに使用されます。 

Premium SSD のディスク キャッシュは、"*読み取り専用*"、"*読み取り書き込み*"、または "*なし*" にすることができます。 

- "*読み取り専用*" キャッシュは、Premium Storage に格納されている SQL Server データ ファイルの場合に非常にメリットがあります。 "*読み取り専用*" キャッシュを使用すると、読み取りは VM メモリおよびローカル SSD 内にあるキャッシュから実行されるので、読み取り待機時間は短く、読み取り IOPS は高く、スループットは高くなります。 これらの読み取りは、Azure Blob Storage に存在するデータ ディスクからの読み取りよりはるかに高速です。 Premium Storage では、ディスクの IOPS とスループットのために、キャッシュからの読み取りはカウントされません。 そのため、アプリケーションの総 IOPS と総スループットを向上させることができます。 
- ログ ファイルはシーケンシャルに書き込まれ、"*読み取り専用*" キャッシュによるメリットがないため、SQL Server ログ ファイルがホストされるディスクには "*なし*" キャッシュ構成を使用する必要があります。 
- SQL Server では "*読み取り書き込み*" キャッシュでのデータ整合性がサポートされていないため、"*読み取り書き込み*" キャッシュを使用して SQL Server ファイルをホストしないでください。 書き込みが "*読み取り専用*" BLOB キャッシュ層を通過すると、書き込みによって "*読み取り専用*" BLOB キャッシュの容量が無駄になり、待機時間が若干増加します。 


   > [!TIP]
   > ストレージ構成が、選択した VM サイズによって課される制限と一致していることを確認してください。 VM サイズのパフォーマンス上限を超えるストレージ パラメーターを選択すると、次の警告が発生します: `The desired performance might not be reached due to the maximum virtual machine disk performance cap`。 ディスクの種類を変更して IOPS を減らすか、VM サイズを増やしてパフォーマンスの上限を上げてください。 これにより、プロビジョニングは停止しません。 


VM が作成されたら、ここで選択した内容に基づいて、次のストレージ構成タスクが実行されます。

* Premium SSD を作成し、仮想マシンに接続する。
* SQL Server にアクセスできるようにデータ ディスクを構成する。
* 指定したサイズとパフォーマンス (IOPS とスループット) 要件に基づいて、データ ディスクを記憶域プールにまとめる。
* 仮想マシンで記憶域プールを新しいドライブに関連付ける。
* 指定したワークロードの種類 (データ ウェアハウス、トランザクション処理、または一般) に基づいて、新しいドライブを最適化する。

Azure portal で SQL Server VM を作成する詳しい手順については、[プロビジョニング チュートリアル](../../../azure-sql/virtual-machines/windows/create-sql-vm-portal.md)をご覧ください。

### <a name="resource-manager-templates"></a>Resource Manager テンプレート

次の Resource Manager テンプレートを使用する場合、既定では、2 つ Premium データ ディスクが記憶域プール構成なしで接続されます。 ただし、仮想マシンに接続される Premium データ ディスクの数は、次のテンプレートをカスタマイズすることで変更できます。

* [自動バックアップを備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [自動修正を備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [AKV 統合を備えた VM の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

### <a name="quickstart-template"></a>クイック スタート テンプレート

次のクイック スタート テンプレートを使用すると、ストレージ最適化を使用して SQL Server VM をデプロイできます。 

* [ストレージ最適化を使用して VM を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage/)
* [UltraSSD を使用して VM を作成する](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-vm-new-storage-ultrassd)

## <a name="existing-vms"></a>既存の VM

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

既存の SQL Server VM については、Azure ポータルで一部のストレージ設定を変更できます。 [SQL 仮想マシン リソース](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)を開き、 **[概要]** を選択します。 SQL Server の概要ページに、VM の現在のストレージ使用量が表示されます。 このグラフには、VM 上のすべてのドライブが示されており、 ドライブごとに、次の 4 つのセクションの記憶域スペースが表示されます。

* SQL data
* SQL ログ
* その他 (非 SQL ストレージ)
* 利用可能

ストレージ設定を変更するには、 **[設定]** の下で **[構成]** を選択します。 

![[構成] オプションと [ストレージ使用量] セクションが強調表示されているスクリーンショット。](./media/storage-configuration/sql-vm-storage-configuration-existing.png)

SQL Server VM の作成プロセス中に構成されたドライブのディスク設定を変更できます。 **[ドライブの拡張]** を選択するとドライブ変更ページが開き、ディスクの種類を変更したり、ディスクを追加したりすることができます。 

![既存の SQL Server VM 用のストレージを構成する](./media/storage-configuration/sql-vm-storage-extend-drive.png)


## <a name="automated-changes"></a>自動変更

このセクションでは、Azure portal での SQL Server VM のプロビジョニングまたは構成時に自動的に行われる、ストレージ構成の変更に関するリファレンス情報を提供します。

* Azure では、VM から選択されたストレージから記憶域プールが構成されます。 このトピックの次のセクションでは、記憶域プールの構成について詳しく説明します。
* 自動ストレージ構成では、必ず [Premium SSD](../../../virtual-machines/disks-types.md) P30 データ ディスクが使用されます。 結果として、選択したテラバイト数と、VM に接続されているデータ ディスク数は 1 対 1 で対応しています。

料金情報については、 [Storage 料金](https://azure.microsoft.com/pricing/details/storage) ページの「 **Disk Storage** 」タブを参照してください。

### <a name="creation-of-the-storage-pool"></a>記憶域プールの作成

Azure では、次の設定を使用して、SQL Server VM で記憶域プールを作成します。

| 設定 | 値 |
| --- | --- |
| ストライプ サイズ |256 KB (データ ウェアハウス)、64 KB (トランザクション) |
| ディスク サイズ |各 1 TB |
| キャッシュ |Read |
| アロケーション サイズ |64 KB NTFS アロケーション ユニット サイズ |
| Recovery | シンプルな復元 (回復性なし) |
| 列の数 |データ ディスクの数 (最大 8)<sup>1</sup> |


<sup>1</sup> 記憶域プールの作成後、その記憶域プールの列数を変更することはできません。


### <a name="workload-optimization-settings"></a>ワークロード最適化の設定

次の表では、使用可能な 3 つのワークロードの種類のオプションと、対応する最適化について説明します。

| ワークロードの種類 | 説明 | 最適化 |
| --- | --- | --- |
| **全般** |ほとんどのワークロードをサポートする既定の設定 |なし |
| **トランザクション処理** |従来のデータベース OLTP ワークロード用にストレージを最適化します |トレース フラグ 1117<br/>トレース フラグ 1118 |
| **データ ウェアハウス** |分析とレポートのワークロード用にストレージを最適化します |トレース フラグ 610<br/>トレース フラグ 1117 |

> [!NOTE]
> ワークロードの種類の指定は、SQL Server 仮想マシンをプロビジョニングするときに、ストレージ構成手順の中でそれを選択することでのみ実行できます。

## <a name="enable-caching"></a>キャッシュの有効化 

ディスク レベルでキャッシュ ポリシーを変更します。 そのためには、Azure portal、[PowerShell](/powershell/module/az.compute/set-azvmdatadisk)、または [Azure CLI](/cli/azure/vm/disk) を使用できます。 

Azure portal でキャッシュ ポリシーを変更するには、次の手順に従います。

1. SQL Server サービスを停止します。 
1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. 仮想マシンに移動し、 **[設定]** で **[ディスク]** を選択します。 
   
   ![Azure portal の VM ディスク構成ブレードを示すスクリーンショット。](./media/storage-configuration/disk-in-portal.png)

1. ドロップダウンから、ディスクの適切なキャッシュ ポリシーを選択します。 

   ![Azure portal のディスク キャッシュ ポリシー構成を示すスクリーンショット。](./media/storage-configuration/azure-disk-config.png)

1. 変更が有効になったら、SQL Server VM を再起動し、SQL Server サービスを開始します。 


## <a name="enable-write-accelerator"></a>書き込みアクセラレータを有効にする

書き込みアクセラレーションは、M シリーズの Virtual Machines (VM) でのみ使用できるディスク機能です。 書き込みアクセラレーションの目的は、大量のミッション クリティカルな OLTP ワークロードまたはデータウェアハウス環境によって、一桁の I/O 待機時間が必要な場合に、Azure Premium Storage に対する書き込みの I/O 待機時間を向上させることです。 

書き込みアクセラレーション ポリシーを変更する前に、すべての SQL Server アクティビティを停止し、SQL Server サービスをシャットダウンしてください。 

ディスクがストライピングされている場合は、各ディスクの書き込みアクセラレーションを個別に有効にします。変更を行う前に、Azure VM をシャットダウンする必要があります。 

Azure portal を使用して書き込みアクセラレーションを有効にするには、次の手順を実行します。

1. SQL Server サービスを停止します。 ディスクがストライピングされている場合は、仮想マシンをシャットダウンします。 
1. [Azure Portal](https://portal.azure.com) にサインインします。 
1. 仮想マシンに移動し、 **[設定]** で **[ディスク]** を選択します。 
   
   ![Azure portal の VM ディスク構成ブレードを示すスクリーンショット。](./media/storage-configuration/disk-in-portal.png)

1. ディスクの **書き込みアクセラレータ** 付きのキャッシュ オプションをドロップダウンから選択します。 

   ![書き込みアクセラレータ キャッシュ ポリシーを示すスクリーンショット。](./media/storage-configuration/write-accelerator.png)

1. 変更が有効になったら、仮想マシンと SQL Server サービスを開始します。 

## <a name="disk-striping"></a>ディスク ストライピング

スループットを向上させるために、データ ディスクをさらに追加し、ディスク ストライピングを使用できます。 データ ディスクの数を特定するには、ログと tempdb を含む、SQL Server データ ファイルに必要なスループットと帯域幅を分析します。 スループットと帯域幅の制限は、VM のサイズによって異なります。 詳細については、[VM のサイズ](../../../virtual-machines/sizes.md)に関する記事を参照してください。


* Windows 8/Windows Server 2012 以降の場合は､次のガイドラインに従った[記憶域スペース](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831739(v=ws.11))を使用します｡

  1. パーティションの不整合によるパフォーマンスへの影響を回避するために、インタリーブ (ストライプ サイズ) を 64 KB (65,536 バイト) に設定します。 これは､PowerShell を使って設定する必要があります｡

  2. カラム数 = 物理ディスク数を設定します｡ 8 つを超えるディスクを構成する場合は PowerShell を使用します (Server Manager の UI ではない)｡

たとえば、次の PowerShell では記憶域プールが新規作成され、インターリーブ サイズは 64 KB、カラム数はこの記憶域プール内の物理ディスクの容量と等しくなります。

  ```powershell
  $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}
  
  New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" `
      -PhysicalDisks $PhysicalDisks | New- VirtualDisk -FriendlyName "DataFiles" `
      -Interleave 65536 -NumberOfColumns $PhysicalDisks .Count -ResiliencySettingName simple `
      –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter `
      -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" `
      -AllocationUnitSize 65536 -Confirm:$false 
  ```

  * Windows 2008 R2 以前では、ダイナミック ディスク (OS ストライプ ボリューム) を使用できます。ストライプ サイズは常に 64 KB です。 このオプションは、Windows 8/Windows Server 2012 の時点で非推奨となっています。 詳細については、[Windows Storage Management API に移行しつつある仮想ディスク サービス](https://docs.microsoft.com/windows/win32/w8cookbook/vds-is-transitioning-to-wmiv2-based-windows-storage-management-api)に関するページでサポートに関する声明をご覧ください。
 
  * [記憶域スペース ダイレクト (S2D)](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) を [SQL Server フェールオーバー クラスター インスタンス](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure)で使用している場合は、単一プールを構成する必要があります。 その単一プール上にはさまざまなボリュームを作成できますが、それらはすべて同じ特性 (たとえば、同じキャッシュ ポリシー) を共有します。
 
  * 負荷予測に基づいて、ご使用の記憶域プールに関連付けるディスク数を決定します。 接続できるデータ ディスクの数は VM サイズによって異なることに注意してください。 詳細については、 [仮想マシンのサイズ](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)に関するページをご覧ください。


## <a name="next-steps"></a>次のステップ

Azure VM での SQL Server の実行に関するその他のトピックについては、「 [Azure Virtual Machines における SQL Server](sql-server-on-azure-vm-iaas-what-is-overview.md)」を参照してください。
