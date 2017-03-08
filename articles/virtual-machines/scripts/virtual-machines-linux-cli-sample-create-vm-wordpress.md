---
title: "Azure CLI のサンプル スクリプト - WordPress がインストールされた Linux VM の作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - WordPress がインストールされた Linux VM の作成"
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
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 8e4068428b814265140b76905bc7f90d9e88f94b
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-wordpress"></a>WordPress がインストールされた VM の作成

このスクリプトでは、仮想マシンを作成してから、Azure 仮想マシンのカスタム スクリプト拡張機能を使用して WordPress をインストールします。 このスクリプトを実行すると、`http://<public IP of VM>/wordpress` にある WordPress 構成サイトにアクセスできるようになります。 

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-wordpress-mysql/create-wordpress-mysql.sh "VM の簡易作成")]

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
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびNSG に接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | 受信トラフィックを許可するネットワーク セキュリティ グループ規則を作成します。 このサンプルでは、HTTP トラフィック用にポート 80 を開きます。 |
| [az vm extension set](https://docs.microsoft.com/cli/azure/vm#create) | WordPress のインストール スクリプトを呼び出すカスタム スクリプト拡張機能を、仮想マシンに追加します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

