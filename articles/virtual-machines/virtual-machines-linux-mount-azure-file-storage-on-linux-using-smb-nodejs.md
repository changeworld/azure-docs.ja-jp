---
title: "Azure CLI 1.0 で SMB を使用して Linux VM に Azure File Storage をマウントする | Microsoft Docs"
description: "SMB を使用して Linux VM に Azure File Storage をマウントする方法です。"
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/07/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 4d547f854df5d4801779e37ac5f22fb9551bd6bb
ms.openlocfilehash: 73151dba33e2c05a66176ef6b895339bce167128
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-10"></a>Azure CLI 1.0 で SMB を使用して Linux VM に Azure File Storage をマウントする

この記事では、SMB マウントを使用して Linux VM で Azure File Storage サービスを利用する方法を説明します。  Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。  要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](#quick-commands) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI (本記事)
- [Azure CLI 2.0 (プレビュー)](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - Resource Manager デプロイメント モデル用の次世代 CLI


## <a name="quick-commands"></a>クイック コマンド

タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)。

前提条件: リソース グループ、VNet、SSH 受信を使用した NSG、サブネット、Azure Storage Account、Azure Storage Account キー、Azure Storage Account 共有、Linux VM。 各例を独自の設定に置き換えてください。

ローカル マウント用のディレクトリを作成します。

```bash
mkdir -p /mnt/mymountpoint
```

Azure Storage Account 共有をマウント ポイントにマウントします。

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

再起動後もマウントを維持するには、`/etc/fstab` に行を追加します

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。  また、File Storage の最新のリリースでは、SMB 3.0 をサポートしている OS からファイル共有をマウントできます。  Linux で SMB マウントを使用すると、SLA でサポートされる信頼性の高い、永続的なアーカイブ格納場所に簡単にバックアップできるようになります。  

Azure File Storage でホストされている SMB マウントに VM からファイルを移動する方法では、同じ SMB 共有をお使いの Mac、Linux、Windows ワークステーションにローカルでマウントすることができるため、ログをデバッグする際に便利です。  SMB プロトコルは大量のログ記録を行うように構築されていないため、SMB は Linux やアプリケーションのログをリアル タイムにストリーミングするソリューションには適していません。  Linux やアプリケーションのログ記録の出力を収集する場合は、Fluentd のような統合された専用のログ記録レイヤー ツールの方が、SMB よりも優れています。

この詳細なチュートリアルでは、まず Azure File Storage 共有を作成するために必要な前提条件の作成し、次に SMB 経由で Linux VM にマウントします。

## <a name="create-the-azure-storage-account"></a>Azure ストレージ アカウントの作成

```azurecli
azure storage account create myStorageAccount \
--sku-name lrs \
--kind storage \
-l westus \
-g myResourceGroup
```

## <a name="show-the-storage-account-keys"></a>ストレージ アカウント キーの表示

Azure ストレージ アカウント キーは、ストレージ アカウントの作成時にペアで作成されます。  ストレージ アカウント キーは、サービスを中断させることなく交互に使用できるように、ペアで作成されます。  ペアの&2; つ目のキーを使用したら、新しいキーのペアを作成します。  新しいストレージ アカウント キーは必ずペアで作成されるため、常に未使用のストレージ キーが&1; つ以上用意されていることになり、すぐに切り換えることができます。

```azurecli
azure storage account keys list myStorageAccount \
--resource-group myResourceGroup
```

## <a name="create-the-azure-file-storage-share"></a>Azure File Storage 共有の作成

SMB 共有が含まれる File Storage 共有を作成します。  クォータは常にギガバイト (GB) で表されます。

```azurecli
azure storage share create mystorageshare \
--quota 10 \
--account-name myStorageAccount \
--account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>マウント ポイントのディレクトリを作成します。

SMB 共有のマウント先となるローカルのディレクトリが Linux ファイル システム上に必要です。  ローカル マウント ディレクトリに書き込まれるファイルや読み取られるファイルは、Azure File Storage にホストされる SMB に転送されます。

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>SMB 共有のマウント

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=myStorageAccount,password=myStorageAccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>再起動後の SMB のマウントの維持

Linux VM を再起動すると、マウントされた SMB 共有はシャットダウン中にアンマウントされます。  起動時に SMB 共有を再マウントするは、Linux の `/etc/fstab` に行を追加する必要があります。  Linux は `fstab` ファイルを使用して、起動中にマウントする必要があるファイル システムを認識します。  SMB 共有を追加することによって、Azure File Storage 共有は Linux VM に永続的にマウントされるファイル システムとして設定されます。  Azure File Storage SMB 共有を新しい VM に追加するには、`cloud-init` を使用します。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>次のステップ

- [cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

