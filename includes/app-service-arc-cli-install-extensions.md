---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: f400c7dbfcf3307ccc283a8e11c5d905dd152b85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371984"
---
## <a name="add-azure-cli-extensions"></a>Azure CLI の拡張機能を追加する

[Azure Cloud Shell](../articles/cloud-shell/quickstart.md) で Bash 環境を起動します。

[![新しいウィンドウで Cloud Shell を起動する](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

これらの CLI コマンドはまだコア CLI セットに含まれていないため、次のコマンドを使用して追加します。

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```