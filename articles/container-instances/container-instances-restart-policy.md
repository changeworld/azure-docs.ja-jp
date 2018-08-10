---
title: Azure Container Instances で再起動ポリシーを使ってコンテナー化タスクを実行する
description: Azure Container Instances を使用して、ビルド、テスト、イメージ レンダリングのジョブなど、完了まで実行するタスクを実行する方法を説明します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/26/2018
ms.author: marsma
ms.openlocfilehash: f4d30a9902261c0e785a1af36a7c1c7a8a0fec46
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2018
ms.locfileid: "39444947"
---
# <a name="run-containerized-tasks-with-restart-policies"></a>再起動ポリシーによるコンテナー化タスクの実行

Azure Container Instances ではコンテナー デプロイを簡単にすばやく行えるため、コンテナー インスタンスでのビルド、テスト、イメージ レンダリングなどの一度のみ実行されるタスクの実行に優れたプラットフォームを提供します。

構成可能な再起動ポリシーを使用して、プロセスが完了したらコンテナーが停止するように指定できます。 コンテナーのインスタンスは秒単位で課金されるため、タスクを実行するコンテナーの実行中に使用されるコンピューティング リソースのみが課金されます。

この記事にある例では、Azure CLI を使用します。 Azure CLI バージョン 2.0.21 以上が[ローカルにインストールされている][azure-cli-install]か、[Azure Cloud Shell](../cloud-shell/overview.md) で CLI を使用する必要があります。

## <a name="container-restart-policy"></a>コンテナー再起動ポリシー

Azure Container Instances でコンテナーを作成する場合、3 つの再起動ポリシー設定のいずれかを指定できます。

| 再起動ポリシー   | 説明 |
| ---------------- | :---------- |
| `Always` | コンテナー グループ内のコンテナーを常に再起動する。 これは**既定**の設定で、コンテナー作成時に再起動ポリシーが指定されていない場合に適用されます。 |
| `Never` | コンテナー グループ内のコンテナーを再起動しない。 コンテナーは最大で 1 回実行されます。 |
| `OnFailure` | コンテナーで実行されたプロセスが失敗 (0 以外の終了コードで終了) した場合にのみ、コンテナー グループ内のコンテナーを再起動する。 コンテナーは少なくとも 1 回実行されます。 |

## <a name="specify-a-restart-policy"></a>再起動ポリシーの指定

再起動ポリシーを指定する方法は、Azure CLI や Azure PowerShell コマンドレット、または Azure Portal を使うなどのコンテナー インスタンスを作成する方法によって異なります。 Azure CLI では、[az container create][az-container-create] を呼び出すときに、`--restart-policy` パラメーターを指定します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image mycontainerimage \
    --restart-policy OnFailure
```

## <a name="run-to-completion-example"></a>完了まで実行の例

再起動ポリシーが動作しているのを確認するには、[microsoft/aci-wordcount][aci-wordcount-image] イメージからコンテナー インスタンスを作成し、`OnFailure` 再起動ポリシーを指定します。 このコンテナー例では、既定でシェイクスピアの[ハムレット](http://shakespeare.mit.edu/hamlet/full.html)のテキストを解析し、最もよく使われる単語 10 個を STDOUT に書き込んで終了する Python スクリプトを実行します。

このコンテナー例を次の [az container create][az-container-create] コマンドで実行します。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure
```

Azure Container Instances はコンテナーを開始し、そのアプリケーション (ここではスクリプト) が終了すると停止します。 Azure Container Instances が再起動ポリシー `Never` または `OnFailure` のコンテナーを停止すると、そのコンテナーの状態は**終了**に設定されます。 [az container show][az-container-show] コマンドで、コンテナーの状態を確認できます。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query containers[0].instanceView.currentState.state
```

出力例:

```bash
"Terminated"
```

コンテナー例の状態が*終了*と表示されたら、コンテナー ログを表示してタスクの出力を確認できます。 [az container logs][az-container-logs] コマンドを実行して、スクリプトの出力を表示します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

出力:

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

この例は、スクリプトが STDOUT に送信した出力を示しています。 ただし、コンテナー化されたタスクでは、後で取得できるように、その出力を永続的ストレージに書き込む場合があります。 たとえば、[Azure ファイル共有](container-instances-mounting-azure-files-volume.md)に書き込むなどです。

## <a name="configure-containers-at-runtime"></a>実行時にコンテナーを構成する

コンテナー インスタンスを作成すると、その**環境変数**を設定できるだけでなく、コンテナーが開始されたときに実行するカスタム **コマンド ライン**を指定することもできます。 これらの設定をバッチ ジョブで使用して、タスク固有の構成で各コンテナーを準備できます。

## <a name="environment-variables"></a>環境変数

コンテナー内で環境変数を設定して、コンテナーが実行するアプリケーションまたはスクリプトの動的な構成を設定します。 これは、`docker run` に対する `--env` コマンドライン引数に似ています。

たとえば、コンテナー インスタンスを作成するときに次の環境変数を指定することで、コンテナー例のスクリプトの動作を変更できます。

*NumWords*: STDOUT に送信された単語の数。

*MinLength*: 単語内のカウントする文字の最小数。 数値を大きくすると、"of" や "the" のようなよく使用される単語は無視されます。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=5 MinLength=8
```

コンテナーの環境変数に `NumWords=5` および `MinLength=8` を指定することで、コンテナー ログに別の出力が表示されます。 コンテナーの状態が*終了*と表示されたら (状態を確認するには `az container show` を使用)、コンテナーのログを表示して新しい出力を確認します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer2
```

出力:

```bash
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="command-line-override"></a>コマンド ラインのオーバーライド

コンテナー イメージに組み込まれたコマンド ラインをオーバーライドするコンテナー インスタンスを作成する場合は、コマンドラインを指定します。 これは、`docker run` に対する `--entrypoint` コマンドライン引数に似ています。

たとえば、別のコマンドラインを指定することで、このコンテナー例で*ハムレット*以外のテキストを解析できます。 コンテナーで実行される Python スクリプト *wordcount.py* は、引数として URL を受け取り、既定のページではなくそのページのコンテンツを処理します。

たとえば、"*ロミオとジュリエット*" に含まれる 5 文字の単語の上位 3 つを決定するには、以下のようにします。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer3 \
    --image microsoft/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables NumWords=3 MinLength=5 \
    --command-line "python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html"
```

再び、コンテナーが*終了*になったら、コンテナー ログを表示することにより出力を表示します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer3
```

出力:

```bash
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>次の手順

### <a name="persist-task-output"></a>タスク出力を保持する

完了まで実行するコンテナーの出力を保存する方法の詳細については、「[Azure Container Instances での Azure ファイル共有のマウント](container-instances-mounting-azure-files-volume.md)」をご覧ください。

<!-- LINKS - External -->
[aci-wordcount-image]: https://hub.docker.com/r/microsoft/aci-wordcount/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container?view=azure-cli-latest#az-container-create
[az-container-logs]: /cli/azure/container?view=azure-cli-latest#az-container-logs
[az-container-show]: /cli/azure/container?view=azure-cli-latest#az-container-show
[azure-cli-install]: /cli/azure/install-azure-cli
