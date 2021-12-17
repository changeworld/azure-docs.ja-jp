---
title: Debian Linux VHD を準備する
description: Azure で VM をデプロイするために Debian VHD イメージを作成する方法について説明します。
author: srijang
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 11/10/2021
ms.author: srijangupta
ms.openlocfilehash: d221dcbce9834191b3bc95db898349f6221dec8e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724864"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Azure 用の Debian VHD の準備

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: フレキシブル スケール セット 

## <a name="prerequisites"></a>前提条件
このセクションでは、 [Debian Web サイト](https://www.debian.org/distrib/) から仮想ハード ディスクにダウンロードした .iso ファイルから Debian Linux オペレーティング システムを既にインストールしているものと想定しています。 .vhd ファイルを作成するためのツールは複数あり、Hyper-V は 1 つの例にすぎません。 Hyper-V の使い方の詳細については、 [Hyper-V の役割のインストールと仮想マシンの構成](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))に関するページを参照してください。

## <a name="installation-notes"></a>インストールに関する注記
* Azure で Linux を使用するためのその他のヒントについては、「[Linux のインストールに関する一般的な注記](create-upload-generic.md#general-linux-installation-notes)」も参照してください。
* 新しい VHDX 形式は、Azure ではサポートされていません。 Hyper-V マネージャーまたは **convert-vhd** コマンドレットを使用して、ディスクを VHD 形式に変換できます。
* Linux システムをインストールする場合は、LVM (通常、多くのインストールで既定) ではなく標準パーティションを使用することをお勧めします。 これにより、特に OS ディスクをトラブルシューティングのために別の VM に接続する必要がある場合に、LVM 名と複製された VM の競合が回避されます。 必要な場合は、[LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) または [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) をデータ ディスク上で使用できます。
* OS ディスクにスワップ パーティションを構成しないでください。 Azure Linux エージェントは、一時的なリソース ディスク上にスワップ ファイルを作成するよう構成できます。 詳細については、以下の手順を参照してください。
* Azure の VHD の仮想サイズはすべて、1 MB にアラインメントさせる必要があります。 未フォーマット ディスクから VHD に変換するときに、変換する前の未フォーマット ディスクのサイズが 1 MB の倍数であることを確認する必要があります。 詳細については、「[Linux のインストールに関する一般的な注記](create-upload-generic.md#general-linux-installation-notes)」に関する記事を参照してください。

## <a name="use-azure-manage-to-create-debian-vhds"></a>Azure-Manage を使用した Debian VHD の作成
[credativ](https://www.credativ.com/) の [azure-manage](https://github.com/credativ/azure-manage) スクリプトなど、Azure 用に Debian VHD を生成するために使用できるツールがあります。 これは、最初からイメージを作成するよりもお勧めの方法です。 たとえば、Debian 8 VHD を作成するには、次のコマンドを実行して `azure-manage` ユーティリティ (および依存関係ファイル) をダウンロードし、`azure_build_image` スクリプトを実行します。

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="prepare-a-debian-image-for-azure"></a>Azure 用の Debian イメージを準備する

Azure Debian Cloud の基本イメージは、[FAI クラウド イメージ ビルダー](https://salsa.debian.org/cloud-team/debian-cloud-images)を使用して作成できます。

(次の git clone コマンドと apt install コマンドは、Debian Cloud イメージ リポジトリから取得しました) まず、リポジトリをクローンし、依存関係をインストールします。

```
$ git clone https://salsa.debian.org/cloud-team/debian-cloud-images.git
$ sudo apt install --no-install-recommends ca-certificates debsums dosfstools \
    fai-server fai-setup-storage make python3 python3-libcloud python3-marshmallow \
    python3-pytest python3-yaml qemu-utils udev
$ cd ./debian-cloud-images
```

(省略可能) スクリプト (シェル スクリプトなど) を `./config_space/scripts/AZURE` に追加して、ビルドをカスタマイズします。



## <a name="an-example-of-a-script-to-customize-the-image-is"></a>イメージをカスタマイズするためのスクリプトの例を次に示します。

```
$ mkdir -p ./config_space/scripts/AZURE
$ cat > ./config_space/scripts/AZURE/10-custom <<EOF
#!/bin/bash

\$ROOTCMD bash -c "echo test > /usr/local/share/testing"
EOF
$ sudo chmod 755 ./config_space/scripts/AZURE/10-custom
```

イメージをカスタマイズするために必要なすべてのコマンドに `$ROOTCMD` プレフィックスを付けることが重要です (これは `chroot $target` というエイリアスになります)。


## <a name="build-the-azure-debian-10-image"></a>Azure Debian 10 イメージをビルドします。

```
$ make image_buster_azure_amd64
```


これにより、現在のディレクトリにいくつかのファイルが出力されます。最も重要なのは `image_buster_azure_amd64.raw` イメージ ファイルです。

生イメージを Azure 用の VHD に変換するには、次のようにします。

```
rawdisk="image_buster_azure_amd64.raw"
vhddisk="image_buster_azure_amd64.vhd"

MB=$((1024*1024))
size=$(qemu-img info -f raw --output json "$rawdisk" | \
gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

rounded_size=$(((($size+$MB-1)/$MB)*$MB))
rounded_size_adjusted=$(($rounded_size + 512))

echo "Rounded Size Adjusted = $rounded_size_adjusted"

sudo qemu-img resize "$rawdisk" $rounded_size
qemu-img convert -f raw -o subformat=fixed,force_size -O vpc "$rawdisk" "$vhddisk"
```


これにより、Azure ディスクに正常にコピーできるようにサイズが丸められた VHD `image_buster_azure_amd64.vhd` が作成されます。

ここで、このイメージ用の Azure リソースを作成する必要があります (これには `$rounded_size_adjusted` 変数を使用するため、上記と同じシェル プロセス内から行う必要があります)。

```
az group create -l $LOCATION -n $RG

az disk create \
    -n $DISK \
    -g $RG \
    -l $LOCATION \
    --for-upload --upload-size-bytes "$rounded_size_adjusted" \
    --sku standard_lrs --hyper-v-generation V1

ACCESS=$(az disk grant-access \
    -n $DISK -g $RG \
    --access-level write \
    --duration-in-seconds 86400 \
    --query accessSas -o tsv)

azcopy copy "$vhddisk" "$ACCESS" --blob-type PageBlob

az disk revoke-access -n $DISK -g $RG
az image create \
    -g $RG \
    -n $IMAGE \
    --os-type linux \
    --source $(az disk show \
        -g $RG \
        -n $DISK \
        --query id -o tsv)
az vm create \
    -g $RG \
    -n $VM \
    --ssh-key-value $SSH_KEY_VALUE \
    --public-ip-address-dns-name $VM \
    --image $(az image show \
        -g $RG \
        -n $IMAGE \
        --query id -o tsv)
```


>[!Note]
> ローカル コンピューターから Azure ディスクへの帯域幅により、azcopy でのアップロードの処理に長時間かかる場合は、Azure VM ジャンプボックスを使用してプロセスを高速化できます。 この実行方法を示します。
>
>1. ローカル マシンに VHD の tarball を作成します: `tar -czvf ./image_buster_azure_amd64.vhd.tar.gz ./image_buster_azure_amd64.vhd`。
>2. Azure Linux VM を作成します (任意のディストリビューションを選択)。 抽出した VHD を保持するのに十分なサイズのディスクを使用して作成してください。
>3. Azure Linux VM に azcopy ユーティリティをダウンロードします。 これは、[こちら](../../storage/common/storage-use-azcopy-v10.md#download-azcopy)から取得できます。
>4. tarball を VM にコピーします: `scp ./image_buster_azure_amd64.vhd.tar.gz <vm>:~`。
>5. VM で VHD を抽出します: `tar -xf ./image_buster_azure_amd64.vhd.tar.gz` (ファイルのサイズによっては、少し時間がかかります)。
>6. 最後に、VM で、`azcopy` (上記のコマンド) を使用して VHD を Azure ディスクにコピーします。


**次のステップ:** これで、Debian Linux 仮想ハード ディスク を使用して、Azure に新しい仮想マシンを作成する準備が整いました。 .vhd ファイルを Azure に初めてアップロードする場合は、「[Create a Linux VM from a custom disk (カスタム ディスクから Linux VM を作成する)](./upload-vhd.md#option-1-upload-a-vhd)」を参照してください。