---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: 549104e9a0721d8edb58ad57cede0e4790cad4cf
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455866"
---
## <a name="add-azure-cli-extensions"></a>Azure CLI の拡張機能を追加する

[Azure Cloud Shell](../articles/cloud-shell/quickstart.md) で Bash 環境を起動します。

[![新しいウィンドウで Cloud Shell を起動する](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

これらの CLI コマンドはまだコア CLI セットに含まれていないため、次のコマンドを使用して追加します。

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --upgrade --yes --name appservice-kube
```