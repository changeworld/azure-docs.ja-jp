---
title: "Azure CLI のサンプル スクリプト - Windows Server 2016 の簡易作成 | Microsoft Docs"
description: "Azure CLI のサンプル スクリプト - Windows Server 2016 の簡易作成"
services: virtual-machines-Windows
documentationcenter: virtual-machines
author: rickstercdn
manager: timlt
editor: tysonn
tags: 
ms.assetid: 
ms.service: virtual-machines-Windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-Windows
ms.workload: infrastructure
ms.date: 02/23/2017
ms.author: rickstercdn
translationtype: Human Translation
ms.sourcegitcommit: 82d40c30c92f5da090e7ec4e2f25ead3908cc603
ms.openlocfilehash: 0ec8c8437e283f49cef27f241fe3d2059fc33898
ms.lasthandoff: 03/02/2017

---

# <a name="quick-create-a-virtual-machine-with-the-azure-cli"></a>Azure CLI を使用した仮想マシンの簡易作成

この記事で紹介するサンプル スクリプトでは、オペレーティング システムが Windows Server 2016 の Azure 仮想マシンを作成します。 スクリプトが正常に実行されると、この仮想マシンへ RDP でアクセスできるようになります。

このスクリプトを実行する前に、`az login` コマンドを使用して Azure との接続が作成されていることを確認してください。 また、スクリプトの冒頭にある $AdminPassword 変数を、一意かつパスワードの複雑性要件を満たしたものに変更してください。

このサンプルは、bash シェルに対応しています。 Azure CLI スクリプトを Windows で実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-vm-quick/create-windows-vm-quick.sh "VM の簡易作成")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | 仮想マシンを作成し、作成したマシンをネットワーク カード、仮想ネットワーク、サブネット、およびネットワーク セキュリティ グループに接続します。 このコマンドでは、使用する仮想マシン イメージと管理者の資格情報も指定します。  |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Windows VM のドキュメント](../virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)のページにあります。

