---
title: "Azure CLI のサンプル スクリプト - NGINX がインストールされた Linux VM の作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - NGINX がインストールされた Linux VM の作成"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: 07d91dfa905d22334bd051f9d5f3d936d38efc88
ms.openlocfilehash: 94e2593271bd7828aab4dcefc0d0df47086e47ad
ms.lasthandoff: 02/28/2017

---

# <a name="create-a-vm-with-nginx"></a>NGINX がインストールされた VM の作成

このスクリプトでは、Azure 仮想マシンを作成してから、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して NGINX をインストールします。 このスクリプトを実行すると、仮想マシンのパブリック IP アドレス上のデモ Web サイトに到達できるようになります。

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

次のスクリプトでは、仮想マシンを作成し、カスタム スクリプト拡張機能を呼び出します。

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-nginx/create-vm-nginx.sh "VM の簡易作成")]

## <a name="custom-script-extension"></a>カスタム スクリプト拡張機能

このサンプルのカスタム スクリプト拡張機能では、次のスクリプトを仮想マシンにコピーします。 その後、このスクリプトを実行して NGINX Web サーバーをインストールおよび構成します。 

```bash
#!/bin/bash
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/network/nsg/rule#create) | 受信トラフィックを許可するネットワーク セキュリティ グループ規則を作成します。 このサンプルでは、HTTP トラフィック用にポート 80 を開きます。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | VM に仮想マシン拡張機能を追加して実行します。 このサンプルでは、カスタム スクリプト拡張機能を使用して NGINX をインストールします。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

