<properties
	pageTitle="Linux VM に関する FAQ | Microsoft Azure"
	description="Resource Manager モデルで作成された Linux 仮想マシンについてよく寄せられる質問への回答を示します。"
	services="virtual-machines-linux"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="cynthn"/>

# Linux 仮想マシンについてのよく寄せられる質問 


この記事では、Resource Manager デプロイ モデルを使用して Azure で作成された Linux 仮想マシンについてよく寄せられる質問に回答します。このトピックの Windows バージョンについては、「[Frequently asked question about Windows Virtual Machines (Windows 仮想マシンについてのよく寄せられる質問)](virtual-machines-windows-faq.md)」を参照してください。

## Azure VM では何を実行できますか。

すべてのサブスクライバーは、Azure 仮想マシンでサーバー ソフトウェアを実行できます。詳細については、「[Azure での動作保証済み Linux ディストリビューション](virtual-machines-linux-endorsed-distros.md)」を参照してください。


## 仮想マシンではどれくらいのストレージ容量を使用できますか。

各データ ディスクで最大 1 TB (テラバイト) を利用できます。使用できるデータ ディスクの数は、仮想マシンのサイズによって決まります。詳細については、「[仮想マシンのサイズ](virtual-machines-linux-sizes.md)」を参照してください。

Azure のストレージ アカウントでは、オペレーティング システム ディスクと任意のデータ ディスクのストレージを利用できます。各ディスクは、実際には .vhd ファイルであり、ページ BLOB として保存されます。価格の詳細については、「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/storage/)」を参照してください。



## 仮想マシンへのアクセス方法を教えてください。

Secure Shell (SSH) を使用して、仮想マシンにログオンするためのリモート接続を確立する必要があります。[Windows から](virtual-machines-linux-ssh-from-windows.md)接続する手順、または [Linux および Mac から](virtual-machines-linux-mac-create-ssh-keys.md)接続する手順を参照します。SSH では、既定で最大 10 の同時接続が可能です。この接続数は構成ファイルを編集することで増やすことができます。


問題がある場合は、[Secure Shell (SSH) 接続のトラブルシューティング](virtual-machines-linux-troubleshoot-ssh-connection.md)に関する記事を参照してください。

## データの格納時に一時ディスク (/dev/sdb2) を使用できますか。

データの格納に一時ディスク (/dev/sdb1) を使用しないでください。一時ディスクは一時的なストレージでしかなく、データ損失の発生時にデータを復旧できない恐れがあります。

## 既存の Azure VM をコピーまたは複製できますか。

はい。詳細については、「[Resource Manager デプロイ モデルで Linux 仮想マシンのコピーを作成する方法](virtual-machines-linux-copy-vm.md)」を参照してください。

## Azure Resource Manager でカナダ中部およびカナダ東部のリージョンが表示されない理由を教えてください。

カナダ中部およびカナダ東部の 2 つの新しいリージョンは、既存の Azure サブスクリプションで仮想マシンを作成した場合、自動的には登録されません。Azure Resource Manager を使用してこれら以外の任意のリージョンに Azure ポータルから仮想マシンをデプロイすると、この登録は自動的に行われます。その他の任意の Azure リージョンに仮想マシンがデプロイされると、新しいリージョンを後続の仮想マシンで使用できるようになります。

## VM の作成後、NIC を VM に追加できますか。

いいえ。NIC を追加できるのは VM の作成時のみです。

<!---HONumber=AcomDC_0824_2016-->