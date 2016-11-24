---
title: "Azure CLI 2.0 (プレビュー) を使用した Linux VM の作成 | Microsoft Azure"
description: "Azure CLI 2.0 (プレビュー) を使用して新しく Linux VM を作成します。"
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
ms.date: 09/26/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 317d6980d304cc503cc43358c4b91459d4abd1ba
ms.openlocfilehash: 6b08f27a27a31fcc665ab051438a962ddc711767


---

# <a name="create-a-linux-vm-using-the-azure-cli-20-preview"></a>Azure CLI 2.0 (プレビュー) を使用した Linux VM の作成
この記事では、Azure CLI 2.0 (プレビュー) で [az vm create](/cli/azure/vm?branch=master#create) コマンドを使用して、Azure に Linux 仮想マシン (VM) をすばやくデプロイする方法を説明します。 

> [!NOTE] 
> Azure CLI 2.0 (プレビュー) は、次世代マルチプラットフォーム CLI です。 試してみて、[GitHub プロジェクト ページ](https://github.com/Azure/azure-cli)でご意見をお聞かせください。
>
> ドキュメントの残りの部分では、既存の Azure CLI を使用します。 Azure CLI 2.0 (プレビュー) ではなく、既存の Azure CLI を使用して VM を作成するには、[Azure CLI を使用した VM の作成](virtual-machines-linux-quick-create-cli-nodejs.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事をご覧ください。

VM を作成するには、以下が必要です。 

* Azure アカウント ([無料試用版を入手](https://azure.microsoft.com/pricing/free-trial/))。
* [Azure CLI v.2.0 (プレビュー)](https://github.com/Azure/azure-cli#installation) をインストールする
* Azure アカウントにログインする (「[az login](/cli/azure/#login)」と入力します)

([Azure Portal](virtual-machines-linux-quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) を使用して、Linux VM をすばやくデプロイすることもできます。)

次の例は、Debian VM をデプロイし、Secure Shell (SSH) キーを添付する方法を示しています (引数は異なる場合があります。別のイメージが必要な場合は、[検索できます](virtual-machines-linux-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json))。

## <a name="create-a-resource-group"></a>リソース グループの作成

まず、「[az resource group create](/cli/azure/resource/group#create)」と入力して、デプロイされたリソースをすべて含むリソース グループを作成します。

```azurecli
az resource group create -n myResourceGroup -l westus
```

出力は次のようになります (必要に応じて、別の `--output` オプションを選択できます)。

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "westus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-your-vm-using-the-latest-debian-image"></a>最新の Debian イメージを使用した VM の作成

これで、VM とその環境を作成できます。 必ず `----public-ip-address-dns-name` の値を一意の値に置き換えます。以下の値は既に使用されている可能性があります。

```azurecli
az vm create \
--image credativ:Debian:8:latest \
--admin-username ops \
--ssh-key-value ~/.ssh/id_rsa.pub \
--public-ip-address-dns-name mydns \
--resource-group myResourceGroup \
--location westus \
--name myVM
```


出力は次のようになります。 VM に **SSH** 接続するために、`publicIpAddress` または `fqdn` の値をメモします。


```json
{
  "fqdn": "mydns.westus.cloudapp.azure.com",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "macAddress": "00-0D-3A-32-05-07",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.112.217.29",
  "resourceGroup": "myResourceGroup"
}
```

この出力に示されているパブリック IP アドレスを使用して、VM にログインします。 ここで示されている完全修飾ドメイン名 (FQDN) を使用することもできます。

```bash
ssh ops@mydns.westus.cloudapp.azure.com
```

選択したディストリビューションによっては、次のような出力が表示されます。

```
The authenticity of host 'mydns.westus.cloudapp.azure.com (40.112.217.29)' can't be established.
RSA key fingerprint is SHA256:xbVC//lciRvKild64lvup2qIRimr/GB8C43j0tSHWnY.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mydns.westus.cloudapp.azure.com,40.112.217.29' (RSA) to the list of known hosts.

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
ops@mynewvm:~$ ls /
bin  boot  dev  etc  home  initrd.img  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  vmlinuz
```

## <a name="next-steps"></a>次のステップ
`az vm create` を使用すると、bash シェルにログインして作業できるように、VM をすばやくデプロイできます。 ただし、 `az vm create` を使用した場合、広範な制御を行ったり、より複雑な環境を作成したりすることはできません。  インフラストラクチャに合わせてカスタマイズした Linux VM をデプロイする方法については、次の記事を参照してください。

* [Azure Resource Manager テンプレートを使用して特定のデプロイを作成する](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Azure CLI コマンドを直接使用して Linux VM 用の独自のカスタム環境を作成する](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [テンプレートを使用して、SSH で保護された Linux VM を Azure で作成する](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[さまざまなコマンドで `docker-machine` Azure ドライバーを使用すると、Linux VM を Docker ホストとしてもすばやく作成](virtual-machines-linux-docker-machine.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)できます。Java を使用している場合、[create()](/java/api/com.microsoft.azure.management.compute._virtual_machine) メソッドを試してください。




<!--HONumber=Nov16_HO3-->


