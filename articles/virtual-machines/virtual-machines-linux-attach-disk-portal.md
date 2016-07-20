<properties
	pageTitle="データ ディスクを Linux VM に接続する | Microsoft Azure"
	description="Azure ポータルでリソース マネージャー デプロイ モデルを使用して、新規または既存のデータ ディスクを Linux VM に接続する方法。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="cynthn"/>

# Azure ポータルで Linux VM にデータ ディスクを接続する方法

この記事では、Azure ポータルを使用して新しいディスクと既存のディスクの両方を Linux 仮想マシンに接続する方法について示します。[Azure ポータルで Windows VM にデータ ディスクを接続する](virtual-machines-windows-attach-disk-portal.md)こともできます。接続する前に、次のヒントを確認してください。

- 仮想マシンのサイズによって、接続できるデータ ディスク数は変わります。詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」を参照してください。
- Premium Storage を使用するには、DS シリーズまたは GS シリーズの仮想マシンが必要です。これらの仮想マシンでは、Premium および Standard のストレージ アカウントのディスクを使用できます。Premium Storage は特定のリージョンで使用できます。詳細については、「[Premium Storage: Azure 仮想マシン ワークロード向けの高パフォーマンス ストレージ](../storage/storage-premium-storage.md)」を参照してください。
- 仮想マシンに接続されているディスクは、実際には Azure ストレージ アカウントの .vhd ファイルです。詳細については、[仮想マシン用のディスクと VHD](virtual-machines-linux-about-disks-vhds.md) に関するページを参照してください。
- 新しいディスクの場合、接続時に Azure で自動的に作成されるので、最初に作成する必要はありません。
- 既存のディスクの場合、Azure ストレージ アカウントで .vhd ファイルを使用できる必要があります。別の仮想マシンに接続されていない場合は既存の .vhd を使用できます。そうでなければ、独自の .vhd ファイルをストレージ アカウントにアップロードできます。


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## 次のステップ

ディスクを追加した後、使用できるように準備する必要があります。この[記事](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux)の仮想マシンのオペレーティング システムの「方法: Linux での新しいデータ ディスクの初期化」を参照してください。

<!---HONumber=AcomDC_0706_2016-->