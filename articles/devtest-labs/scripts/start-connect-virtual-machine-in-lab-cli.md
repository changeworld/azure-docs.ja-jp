---
title: Azure CLI スクリプト サンプル - ラボの仮想マシンを起動する | Microsoft Docs
description: この Azure CLI スクリプトは、Azure DevTest Labs のラボの仮想マシンを起動します。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f82d43fc212da034d978ee3d65236d806daeb008
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764983"
---
# <a name="use-azure-cli-to-start-a-virtual-machine-in-a-lab-in-azure-devtest-labs"></a>Azure CLI を使用して Azure DevTest Labs のラボの仮想マシンを起動する

この Azure CLI スクリプトは、ラボの仮想マシン (VM) を起動します。 

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>サンプル スクリプト

[!code-azurecli-interactive[main](../../../cli_scripts/devtest-lab/start-connect-virtual-machine-in-lab/start-connect-virtual-machine-in-lab.sh "Start a VM")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。

| コマンド | Notes |
|---|---|
| [az lab vm start](/cli/azure/lab/vm#az_lab_vm_start) | ラボの仮想マシン (VM) を起動します。 この操作を完了するにはしばらく時間がかかることがあります。 |

## <a name="next-steps"></a>次のステップ

Azure CLI の詳細については、[Azure CLI のドキュメント](/cli/azure)のページをご覧ください。

Azure Lab Services のその他の CLI のサンプル スクリプトについては、[Azure Lab Services の CLI のサンプル](../samples-cli.md)に関する記事をご覧ください。
