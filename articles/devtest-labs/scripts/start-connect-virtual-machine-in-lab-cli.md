---
title: Azure CLI スクリプト サンプル - ラボの仮想マシンを起動する
description: この Azure CLI スクリプトは、Azure DevTest Labs のラボの仮想マシンを起動します。
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: f28427c13587af7654f56fb728a44ea42cce5f5a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557564"
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
