---
title: Azure Container Instances で環境変数を設定する
description: Azure Container Instances で環境変数を設定する方法を説明します
services: container-instances
author: david-stanford
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/13/2018
ms.author: dastanfo
ms.openlocfilehash: 37fde41b6dc2ea0a4d3b4b38a0e3df81a297c125
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="set-environment-variables"></a>環境変数の設定

Container Instances で環境変数を設定すると、コンテナーによって実行されるアプリケーションまたはスクリプトの動的な構成を提供できます。

現在は、CLI と PowerShell から環境変数を設定することができます。 どちらの場合も、コマンドのフラグを使って環境変数を設定します。 ACI での環境変数の設定は、`docker run` に対する `--env` コマンド ライン引数に似ています。 たとえば、microsoft/aci-wordcount コンテナー イメージを使う場合は、次の環境変数を指定することによって動作を変更できます。

*NumWords*: STDOUT に送信された単語の数。

*MinLength*: 単語内のカウントする文字の最小数。 数値を大きくすると、"of" や "the" のようなよく使用される単語は無視されます。

## <a name="azure-cli-example"></a>Azure CLI の例

コンテナーの既定の出力を確認するには、次のコマンドを実行します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

コンテナーの環境変数に `NumWords=5` および `MinLength=8` を指定することで、コンテナー ログに別の出力が表示されます。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

コンテナーの状態が *Terminated* になったら (状態を確認するには [az container show][az-container-show] を使います)、コンテナーのログを表示して出力を確認します。  環境変数が設定されていないコンテナーの出力を見るには、mycontainer2 ではなく mycontainer1 を --name に設定します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

## <a name="azure-powershell-example"></a>Azure PowerShell の例

コンテナーの既定の出力を確認するには、次のコマンドを実行します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

コンテナーの環境変数に `NumWords=5` および `MinLength=8` を指定することで、コンテナー ログに別の出力が表示されます。

```azurepowershell-interactive
$envVars = @{NumWord=5;MinLength=8}
New-AzureRmContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image microsoft/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

コンテナーの状態が *Terminated* になったら (状態を確認するには [Get-AzureRmContainerInstanceLog][azure-instance-log] を使います)、コンテナーのログを表示して出力を確認します。 環境変数が設定されていないコンテナーのログを見るには、mycontainer2 ではなく mycontainer1 を ContainerGroupName に設定します。

```azurepowershell-interactive
Get-AzureRmContainerInstanceLog `
    -ResourceGroupName myResourceGroup `
    -ContainerGroupName mycontainer2
```

## <a name="example-output-without-environment-variables"></a>環境変数が設定されていない場合の出力例

```bash
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]
```

## <a name="example-output-with-environment-variables"></a>環境変数が設定されている場合の出力例

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="next-steps"></a>次の手順

コンテナーへの入力をカスタマイズする方法がわかったので、コンテナーの実行が完了するまで出力を保持する方法を学習してください。
> [!div class="nextstepaction"]
> [Azure Container Instances での Azure ファイル共有のマウント](container-instances-mounting-azure-files-volume.md)

<!-- LINKS Internal -->
[azure-cloud-shell]: ../cloud-shell/overview.md
[azure-cli-install]: /cli/azure/
[azure-powershell-install]: /powershell/azure/install-azurerm-ps
[azure-instance-log]: /powershell/module/azurerm.containerinstance/get-azurermcontainerinstancelog
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az_container_show