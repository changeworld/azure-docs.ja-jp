---
title: "SMB を使用して Linux VM に Azure File Storage をマウントする |Microsoft Docs"
description: "Azure CLI 2.0 (プレビュー) で SMB を使用して Linux VM に Azure File Storage をマウントする方法"
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
ms.date: 02/13/2017
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: dbdebe120bd6166854f3494169d9c0d5c8f7bf69
ms.openlocfilehash: 6851faf301175ed851e53088862e6f8b50ad8b3e
ms.lasthandoff: 02/15/2017


---

# <a name="mount-azure-file-storage-on-linux-vms-using-smb-using-the-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) で SMB を使用して Linux VM に Azure File Storage をマウントする

この記事では、SMB マウントを使用して Linux VM で Azure File Storage サービスを利用する方法を説明します。 Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。  要件は次のとおりです。

- [Azure アカウント](https://azure.microsoft.com/pricing/free-trial/)

- [SSH パブリック キー ファイルおよびプライベート キー ファイル](virtual-machines-linux-mac-create-ssh-keys.md)


## <a name="cli-versions-to-complete-the-task"></a>タスクを完了するための CLI バージョン
次のいずれかの CLI バージョンを使用してタスクを完了できます。

- [Azure CLI 1.0](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) - クラシック デプロイメント モデルと Resource Manager デプロイメント モデル用の CLI
- [Azure CLI 2.0 (プレビュー)](#quick-commands) - Resource Manager デプロイメント モデル用の次世代 CLI (この記事)


## <a name="quick-commands"></a>クイック コマンド

タスクをすばやく実行する必要がある場合のために、次のセクションでは、必要なコマンドの詳細について説明します。 詳細な情報と各手順のコンテキストが、ドキュメントの残りの部分に記載されています。[ここからお読みください](virtual-machines-linux-mount-azure-file-storage-on-linux-using-smb.md#detailed-walkthrough)。

前提条件: リソース グループ、VNet、SSH 受信を使用した NSG、サブネット、Azure Storage Account、Azure Storage Account キー、Azure Storage Account 共有、Linux VM。 各例を実際の値に置き換えてください。

次のようにローカル マウント用のディレクトリを作成します。

```bash
mkdir -p /mnt/mymountpoint
```

次のように Azure File Storage SMB 共有をマウント ポイントにマウントします。

```bash
sudo mount -t cifs //myaccountname.file.core.windows.net/mysharename /mymountpoint -o vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

再起動後もマウントを維持するには、次のように `/etc/fstab` に行を追加します。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="detailed-walkthrough"></a>詳細なチュートリアル

Azure File Storage は、標準の SMB プロトコルを使用したクラウドでのファイル共有を提供します。 また、File Storage の最新のリリースでは、SMB 3.0 をサポートしている OS からファイル共有をマウントできます。 Linux で SMB マウントを使用すると、SLA でサポートされる信頼性の高い、永続的なアーカイブ格納場所に簡単にバックアップできるようになります。  

Azure File Storage でホストされている SMB マウントに VM からファイルを移動する方法では、同じ SMB 共有をお使いの Mac、Linux、Windows ワークステーションにローカルでマウントすることができるため、ログをデバッグする際に便利です。 SMB プロトコルは大量のログ記録を行うように構築されていないため、SMB は Linux やアプリケーションのログをリアル タイムにストリーミングするソリューションには適していません。 Linux やアプリケーションのログ記録の出力を収集する場合は、Fluentd のような統合された専用のログ記録レイヤー ツールの方が、SMB よりも優れています。

この詳細なチュートリアルでは、まず Azure File Storage 共有を作成するために必要な前提条件の作成し、次に SMB 経由で Linux VM にマウントします。

最初に、[az group create](/cli/azure/group#create) を実行して、ファイル共有を保持するリソース グループを作成します。 次の例では、`myResourceGroup` という名前のリソース グループを `West US` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-the-azure-storage-account"></a>Azure ストレージ アカウントの作成
次に、[az storage account create](/cli/azure/storage/account#create) を実行して、実際のファイルを格納するストレージ アカウントを作成します。 次の例では、`Standard_LRS` ストレージ SKU を使用して `mystorageaccount` という名前のストレージ アカウントを作成します。

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location westus \
    --sku Standard_LRS
```

## <a name="show-the-storage-account-keys"></a>ストレージ アカウント キーの表示

Azure ストレージ アカウント キーは、ストレージ アカウントの作成時にペアで作成されます。 ストレージ アカウント キーは、サービスを中断させることなく交互に使用できるように、ペアで作成されます。 ペアの&2; つ目のキーを使用したら、新しいキーのペアを作成します。 新しいストレージ アカウント キーは必ずペアで作成されるため、常に未使用のストレージ キーが&1; つ以上用意されていることになり、すぐに切り換えることができます。

[az storage account keys list](/cli/azure/storage/account/keys#list) を実行して、ストレージ アカウント キーを確認します。 次の例は、`mystorageaccount` という名前のストレージ アカウントのストレージ アカウント キーの一覧を取得します。

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount
```

1 つのキーを抽出するには、`--query` フラグを使用します。 次の例は、最初のキー (`[0]`) を抽出します。

```azurecli
az storage account keys list --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query '[0].{Key:value}' --output tsv
```


## <a name="create-the-azure-file-storage-share"></a>Azure File Storage 共有の作成

[az storage share create](/cli/azure/storage/share#create) を実行して、SMB 共有を格納する File Storage 共有を作成します。 クォータは常にギガバイト (GB) で表されます。 前の **az storage account keys list** コマンドで取得したいずれかのキーを渡します。 次の例は、`mystorageshare` という名前の `10` GB クオータのファイル共有を作成します。

```azurecli
az storage share create --name mystorageshare \
    --quota 10 \
    --account-name mystorageaccount \
    --account-key nPOgPR<--snip-->4Q==
```

## <a name="create-the-mount-point-directory"></a>マウント ポイントのディレクトリを作成します。

SMB 共有のマウント先となるローカルのディレクトリが Linux ファイル システム上に必要です。 ローカル マウント ディレクトリに書き込まれるファイルや読み取られるファイルは、Azure File Storage にホストされる SMB に転送されます。 次の例は、`/mnt/mymountdirectory` にローカル ディレクトリを作成します。

```bash
sudo mkdir -p /mnt/mymountdirectory
```

## <a name="mount-the-smb-share"></a>SMB 共有のマウント
作成したローカル ディレクトリに SMB 共有をマウントします。 次の例のように、マウントの資格情報として独自のストレージ アカウント ユーザー名とストレージ アカウント キーを指定します。

```azurecli
sudo mount -t cifs //myStorageAccount.file.core.windows.net/mystorageshare /mnt/mymountdirectory -o vers=3.0,username=mystorageaccount,password=mystorageaccountkey,dir_mode=0777,file_mode=0777
```

## <a name="persist-the-smb-mount-through-reboots"></a>再起動後の SMB のマウントの維持

Linux VM を再起動すると、マウントされた SMB 共有はシャットダウン中にアンマウントされます。 起動時に SMB 共有を再マウントするは、Linux の `/etc/fstab` に行を追加します。 Linux は `fstab` ファイルを使用して、起動中にマウントする必要があるファイル システムを認識します。 SMB 共有を追加することによって、Azure File Storage 共有は Linux VM に永続的にマウントされるファイル システムとして設定されます。 Azure File Storage SMB 共有を新しい VM に追加するには、`cloud-init` を使用します。

```bash
//myaccountname.file.core.windows.net/mysharename /mymountpoint cifs vers=3.0,username=myaccountname,password=StorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

## <a name="next-steps"></a>次のステップ

- [cloud-init を利用し、作成時に Linux VM をカスタマイズする](virtual-machines-linux-using-cloud-init.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Linux VM へのディスクの追加](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Azure CLI を使って Linux VM のディスクを暗号化する](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

