---
title: "Azure CLI のサンプル スクリプト - Docker ホストの作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Docker ホストの作成"
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
ms.openlocfilehash: 3693e6f78541caeca8fd1c276028c0f64ca71ddc
ms.lasthandoff: 03/01/2017

---

# <a name="create-a-vm-with-docker"></a>Docker がインストールされた VM の作成

このサンプル スクリプトでは、仮想マシンを作成してから、Azure Docker VM 拡張機能を使用して Docker ホストを構成します。 その後、NGINX を実行するコンテナーを Docker VM 拡張機能により作成します。 最後に、ポート 80 のすべての受信トラフィックに対して Azure ネットワーク セキュリティ グループを構成します。 このスクリプトが正常に実行されると、Azure 仮想マシンの FQDN 経由で NGINX Web サーバーにアクセスできるようになります。 

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-docker-host/create-docker-host.sh "Docker ホスト")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az vm open-port](https://docs.microsoft.com/cli/azure/vm#open-port) | 受信トラフィックを許可するネットワーク セキュリティ グループ規則を作成します。 このサンプルでは、HTTP トラフィック用にポート 80 を開きます。 |
| [azure vm extension set](https://docs.microsoft.com/cli/azure/vm/extension#set) | VM に仮想マシン拡張機能を追加して実行します。 このサンプルでは、Docker VM 拡張機能を使用して Docker ホストを構成します。|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

