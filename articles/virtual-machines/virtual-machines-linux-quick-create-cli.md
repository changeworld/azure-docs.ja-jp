---
title: "Azure CLI 2.0 を使用した Linux VM の作成 | Microsoft Azure"
description: "Azure CLI 2.0 を使用して Linux VM を作成します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
ms.assetid: 82005a05-053d-4f52-b0c2-9ae2e51f7a7e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/13/2017
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 892e3c62a2ad4dc4fd0691874d46bb296e379524
ms.openlocfilehash: cc51b04c31c02aabf25c9efb1e9cd975077811a4
ms.lasthandoff: 02/27/2017


---

# <a name="create-a-linux-vm-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用して Linux VM を作成する
この記事では、Azure CLI 2.0 で [az vm create](/cli/azure/vm#create) コマンドを使用して、管理ディスクを使用する Linux 仮想マシン (VM) とネイティブ ストレージ アカウント内の非管理対象ディスクを使用する Linux 仮想マシン (VM) の両方を Azure にすばやくデプロイする方法を説明します。 これらの手順は、[Azure CLI 1.0](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して実行することもできます。

VM を作成するには、以下が必要です。 

* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* [Azure CLI 2.0](/cli/azure/install-az-cli2) のインストール
* Azure アカウントにログインする (「[az login](/cli/azure/#login)」と入力します)

([Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して Linux VM をデプロイすることもできます。)

次の例は、Debian VM をデプロイし、それに Secure Shell (SSH) キーを使用して接続する方法を示しています。 引数は異なる場合があります。別のイメージが必要な場合は、[検索できます](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="using-managed-disks"></a>Managed Disks の使用

Azure Managed Disks を使用するには、Azure Managed Disks をサポートしているリージョンを使用する必要があります。 まず、「[az group create](/cli/azure/group#create)」と入力して、デプロイされたリソースをすべて含むリソース グループを作成します。

```azurecli
 az group create -n myResourceGroup -l westus
```

出力は次のようになります (別の形式で表示したい場合は、別の`--output`オプションを指定できます)。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```
### <a name="create-your-vm"></a>VM の作成 
これで、VM とその環境を作成できます。 必ず `--public-ip-address-dns-name` の値を一意の値に置き換えます。以下の値は既に使用されている可能性があります。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name manageddisks \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


出力は次のようになります。 VM に **SSH** 接続するために、`publicIpAddress` または `fqdn` の値をメモします。


```json
{
  "fqdn": "manageddisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-E9-41",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "104.42.127.53",
  "resourceGroup": "myResourceGroup"
}
```

この出力に示されているパブリック IP アドレスまたは完全修飾ドメイン名 (FQDN) を使用して、VM にログインします。

```bash
ssh ops@manageddisks.westus.cloudapp.azure.com
```

選択したディストリビューションによっては、次のような出力が表示されます。

```bash
The authenticity of host 'manageddisks.westus.cloudapp.azure.com (134.42.127.53)' can't be established.
RSA key fingerprint is c9:93:f5:21:9e:33:78:d0:15:5c:b2:1a:23:fa:85:ba.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'manageddisks.westus.cloudapp.azure.com' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Fri Jan 13 14:44:21 2017 from net-37-117-240-123.cust.vodafonedsl.it
ops@myVM:~$ 
```

Managed Disks を使用する新しい VM で実行できるその他の作業については、「[次のステップ](#next-steps)」をご覧ください。

## <a name="using-unmanaged-disks"></a>非管理対象ディスクの使用 

非管理対象ストレージ ディスクを使用する VM は、非管理対象ストレージ アカウントを持ちます。 まず、「[az group create](/cli/azure/group#create)」と入力して、デプロイされたリソースをすべて含むリソース グループを作成します。

```azurecli
az group create --name nativedisks --location westus
```

出力は次のようになります (必要に応じて、別の `--output` オプションを選択できます)。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks",
  "location": "westus",
  "managedBy": null,
  "name": "nativedisks",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

### <a name="create-your-vm"></a>VM の作成 

これで、VM とその環境を作成できます。 `--use-unmanaged-disk` フラグを使用して、非管理対象ディスクを使用して VM を作成します。 非管理対象ストレージ アカウントも作成されます。 必ず `--public-ip-address-dns-name` の値を一意の値に置き換えます。以下の値は既に使用されている可能性があります。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username azureuser \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name nativedisks \
--resource-group nativedisks \
--location westus \
--name myVM \
--use-unmanaged-disk
```

出力は次のようになります。 VM に **SSH** 接続するために、`publicIpAddress` または `fqdn` の値をメモします。

```json
{
  "fqdn": "nativedisks.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/nativedisks/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-33-24-3C",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.91.91.195",
  "resourceGroup": "nativedisks"
}
```

上記の出力に示されているパブリック IP アドレスまたは完全修飾ドメイン名 (FQDN) を使用して、VM にログインします。

```bash
ssh ops@nativedisks.westus.cloudapp.azure.com
```

選択したディストリビューションによっては、次のような出力が表示されます。

```
The authenticity of host 'nativedisks.westus.cloudapp.azure.com (13.91.93.195)' can't be established.
RSA key fingerprint is 3f:65:22:b9:07:c9:ef:7f:8c:1b:be:65:1e:86:94:a2.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'nativedisks.westus.cloudapp.azure.com,13.91.93.195' (RSA) to the list of known hosts.
Enter passphrase for key '/home/ops/.ssh/id_rsa':

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@myVM:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>次のステップ
`az vm create` を使用すると、bash シェルにログインして作業できるように、VM をすばやくデプロイできます。 ただし、 `az vm create` を使用した場合、広範な制御を行ったり、より複雑な環境を作成したりすることはできません。  インフラストラクチャに合わせてカスタマイズした Linux VM をデプロイする方法については、次の記事を参照してください。

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して、SSH で保護された Linux VM を Azure で作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[さまざまなコマンドで `docker-machine` Azure ドライバーを使用すると、Linux VM を Docker ホストとしてもすばやく作成](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)できます。Java を使用している場合、[create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) メソッドを試してください。


