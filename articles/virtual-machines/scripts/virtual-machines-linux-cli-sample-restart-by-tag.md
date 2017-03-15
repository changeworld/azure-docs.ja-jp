---
title: "Azure CLI のサンプル スクリプト - VM の再起動 | Microsoft Docs"
description: "Azure CLI サンプル スクリプト - VM をタグと ID によって再起動する"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: allclark
manager: douge
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2017
ms.author: allclark
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b292a02770fa74812e74fa38f870e47ed7473b63
ms.lasthandoff: 03/04/2017

---

# <a name="restart-vms-by-tag"></a>タグによって VM を再起動する

この例では、特定のタグを持つ仮想マシンを複数のリソース グループ内に作成します。
仮想マシンは、`--no-wait` を使用して並行して作成された後、全体の操作が完了するまで待機します。

仮想マシンは、作成後に、2 つの異なるクエリ メカニズムを使用して再起動されます。

最初に、非同期作成の待機で使用されたものと同じクエリで VM が再起動されます。
```bash
az vm restart --ids $(az vm list --query "join(' ', ${GROUP_QUERY}] | [].id)" \
    -o tsv) $1>/dev/null
```

次に、汎用リソースの一覧とクエリを使用して、タグによって ID が取得されます。
```bash
az vm restart --ids $(az resource list --tag ${TAG} \
    --query "[?type=='Microsoft.Compute/virtualMachines'].id" -o tsv) $1>/dev/null
```

このサンプルは、Bash シェルに対応しています。 Azure CLI スクリプトを Windows クライアントで実行する方法については、[Windows での Azure CLI の実行](../virtual-machines-windows-cli-options.md)に関する記事を参照してください。

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli[main](../../../cli_scripts/virtual-machine/restart-by-tag/restart-by-tag.sh "タグによって VM を再起動する")]

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ 

サンプル スクリプトの実行後、次のコマンドを使用すると、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete -n GROUP1 --no-wait --yes && \ 
az group delete -n GROUP2 --no-wait --yes && \
az group delete -n GROUP3 --no-wait --yes
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用して、リソース グループ、仮想マシン、可用性セット、ロード バランサー、およびすべての関連リソースを作成します。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | すべてのリソースを格納するリソース グループを作成します。 |
| [az vm create](https://docs.microsoft.com/cli/azure/vm/availability-set#create) | 仮想マシンを作成します。  |
| [az vm list](https://docs.microsoft.com/cli/azure/vm#list) | VM を再起動する前に、VM がプロビジョニングされていることを確認するために、`--query` と一緒に使用されます。 |
| [az vm restart](https://docs.microsoft.com/cli/azure/vm#list) | VM を再起動します。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](https://docs.microsoft.com/cli/azure/overview)のページをご覧ください。

その他の仮想マシン用の CLI サンプル スクリプトは、[Azure Linux VM のドキュメント](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)のページにあります。

