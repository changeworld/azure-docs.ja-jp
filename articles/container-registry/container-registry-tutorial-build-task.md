---
title: チュートリアル - Azure Container Registry Build を使用してコンテナー イメージ ビルドを自動化する
description: このチュートリアルでは、Git リポジトリにソース コードをコミットしたときにクラウドでコンテナー イメージ ビルドを自動的にトリガーするようにビルド タスクを構成する方法を説明します。
services: container-registry
author: mmacy
manager: jeconnoc
ms.service: container-registry
ms.topic: tutorial
ms.date: 05/11/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 71ea0f489df6969f0916ac14d187e10a90a520cd
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38722713"
---
# <a name="tutorial-automate-container-image-builds-with-azure-container-registry-build"></a>チュートリアル: Azure Container Registry Build を使用してコンテナー イメージ ビルドを自動化する

[クイック ビルド](container-registry-tutorial-quick-build.md)に加えて、ACR Build は、"*ビルド タスク*" での自動 Docker コンテナー イメージ ビルドをサポートしています。 このチュートリアルでは、Git リポジトリにソース コードをコミットするとクラウドでイメージのビルドを自動的にトリガーするビルド タスクを、Azure CLI を使って作成します。

シリーズの第 2 部であるこのチュートリアルでは、次のことを行います。

> [!div class="checklist"]
> * ビルド タスクを作成する
> * ビルド タスクをテストする
> * ビルドの状態を表示する
> * コードのコミットでビルド タスクをトリガーする

このチュートリアルは、[前のチュートリアル](container-registry-tutorial-quick-build.md)の手順が既に完了していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](container-registry-tutorial-quick-build.md#prerequisites)」セクションの手順を行ってください。

[!INCLUDE [container-registry-build-preview-note](../../includes/container-registry-build-preview-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

ローカルで Azure CLI を使用する場合は、Azure CLI のバージョン **2.0.32** 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

### <a name="get-sample-code"></a>サンプル コードを取得する

このチュートリアルは、[前のチュートリアル](container-registry-tutorial-quick-build.md)の手順を既に完了し、サンプル リポジトリをフォークおよび複製していることを前提としています。 完了していない場合は、先に進む前に、前のチュートリアルの「[前提条件](container-registry-tutorial-quick-build.md#prerequisites)」セクションの手順を行ってください。

### <a name="container-registry"></a>コンテナー レジストリ

このチュートリアルを行うには、Azure サブスクリプションに Azure コンテナー レジストリが必要です。 レジストリが必要な場合は、[前のチュートリアル](container-registry-tutorial-quick-build.md)または「[クイック スタート: Azure CLI を使用したコンテナー レジストリの作成](container-registry-get-started-azure-cli.md)」をご覧ください。

## <a name="build-task"></a>ビルド タスク

ビルド タスクでは、自動化するビルドについて、コンテナー イメージのソース コードの場所や、ビルドをトリガーするイベントなどのプロパティを定義します。 Git リポジトリへのコミットなど、ビルド タスクで定義されているイベントが発生すると、ACR Build はクラウドでコンテナー イメージのビルドを開始します。 既定では、ACR Build は正常にビルドされたイメージを、タスクで指定されている Azure コンテナー レジストリにプッシュします。

現在、ACR Build は次のビルド タスク トリガーをサポートしています。

* Git リポジトリへのコミット
* 基本イメージの更新

## <a name="create-a-build-task"></a>ビルド タスクを作成する

このセクションでは最初に、ACR Build で使用する GitHub 個人用アクセス トークン (PAT) を作成します。 次に、リポジトリのフォークにコードがコミットされるとビルドをトリガーするビルド タスクを作成します。

### <a name="create-a-github-personal-access-token"></a>GitHub 個人用アクセス トークンを作成する

Git リポジトリへのコミットでビルドをトリガーするには、ACR Build がリポジトリにアクセスするための個人用アクセス トークン (PAT) が必要です。 GitHub で PAT を生成するには次の手順のようにします。

1. GitHub で PAT 作成ページ (https://github.com/settings/tokens/new) に移動します
1. トークンの短い**説明**を入力します (例: "ACR Build タスクのデモ")
1. **[repo]\(リポジトリ\)** で、**repo:status** と **public_repo** を有効にします

   ![GitHub の個人用アクセス トークン生成ページのスクリーンショット][build-task-01-new-token]

1. **[Generate token]\(トークンの生成\)** ボタンを選びます (パスワードの入力が必要な場合があります)
1. 生成されたトークンをコピーし、**安全な場所**に保存します (このトークンは、次のセクションでビルド タスクを定義するときに使います)

   ![GitHub で生成された個人用アクセス トークンのスクリーンショット][build-task-02-generated-token]

### <a name="create-the-build-task"></a>ビルド タスクを作成する

ACR Build がコミットの状態を読み取ってリポジトリに webhook を作成できるようにするために必要な手順が済んだので、リポジトリへのコミットでコンテナー イメージのビルドをトリガーするビルド タスクを作成できます。

最初に、次のシェル環境変数に、環境に適した値を設定します。 これは必須ではありませんが、このチュートリアルの複数行の Azure CLI コマンドの実行が少し簡単になります。 これらの値を設定しなかった場合、サンプル コマンドで任意に出現する各値を手動で置き換える必要があります。

```azurecli-interactive
ACR_NAME=mycontainerregistry # The name of your Azure container registry
GIT_USER=gituser             # Your GitHub user account name
GIT_PAT=personalaccesstoken  # The PAT you generated in the previous section
```

次の [az acr build-task create][az-acr-build-task-create] コマンドを実行して、ビルド タスクを作成します。

```azurecli-interactive
az acr build-task create \
    --registry $ACR_NAME \
    --name buildhelloworld \
    --image helloworld:{{.Build.ID}} \
    --context https://github.com/$GIT_USER/acr-build-helloworld-node \
    --branch master \
    --git-access-token $GIT_PAT
```

このビルド タスクでは、`--context` で指定されているリポジトリの *master* ブランチにコードがコミットされたら常に、ACR Build がそのブランチのコードからコンテナー イメージをビルドすることが指定されています。 `--image` 引数では、イメージのタグのバージョン部分に対する `{{.Build.ID}}` のパラメーター化された値が指定されており、ビルドされたイメージが特定のビルドに関連付けられ、一意にタグ付けされることを保証します。

[az acr build-task create][az-acr-build-task-create] コマンドが成功した場合、出力は次のようになります。

```console
$ az acr build-task create \
>     --registry $ACR_NAME \
>     --name buildhelloworld \
>     --image helloworld:{{.Build.ID}} \
>     --context https://github.com/$GIT_USER/acr-build-helloworld-node \
>     --branch master \
>     --git-access-token $GIT_PAT
{
  "additionalProperties": {},
  "alias": "buildhelloworld",
  "creationDate": "2018-05-10T19:34:48.086776+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/mycontainerregistry/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry/buildTasks/buildhelloworld",
  "location": "eastus",
  "name": "buildhelloworld",
  "platform": {
    "additionalProperties": {},
    "cpu": 1,
    "osType": "Linux"
  },
  "properties": {
    "additionalProperties": {
      "imageName": null
    },
    "baseImageDependencies": null,
    "baseImageTrigger": "Runtime",
    "branch": "master",
    "buildArguments": [],
    "contextPath": null,
    "dockerFilePath": "Dockerfile",
    "imageNames": [
      "helloworld:{{.Build.ID}}"
    ],
    "isPushEnabled": true,
    "noCache": false,
    "provisioningState": "Succeeded",
    "type": "Docker"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "mycontainerregistry",
  "sourceRepository": {
    "additionalProperties": {},
    "isCommitTriggerEnabled": true,
    "repositoryUrl": "https://github.com/gituser/acr-build-helloworld-node",
    "sourceControlAuthProperties": null,
    "sourceControlType": "GitHub"
  },
  "status": "Enabled",
  "tags": null,
  "timeout": 3600,
  "type": "Microsoft.ContainerRegistry/registries/buildTasks"
}
```

## <a name="test-the-build-task"></a>ビルド タスクをテストする

ビルドを定義するビルド タスクが完成しました。 ビルドの定義をテストするには、[az acr build-task run][az-acr-build-task-run] コマンドを実行してビルドを手動でトリガーします。

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld
```

既定では、`az acr build-task run` コマンドを実行すると、ログ出力がコンソールにストリーミングされます。 次の出力では、ビルド **aa2** がキューに登録されてビルドされたことが示されています。

```console
$ az acr build-task run --registry $ACR_NAME --name buildhelloworld
Queued a build with build ID: aa2
Waiting for a build agent...
time="2018-05-10T19:37:17Z" level=info msg="Running command git clone https://x-access-token:*************@github.com/gituser/acr-build-helloworld-node /root/acr-builder/src"
Cloning into '/root/acr-builder/src'...
time="2018-05-10T19:37:17Z" level=info msg="Running command git checkout master"
Already on 'master'
Your branch is up to date with 'origin/master'.
920f16cfafa36d0bc3f397c3dd48185a03499404
time="2018-05-10T19:37:17Z" level=info msg="Running command git rev-parse --verify HEAD"
time="2018-05-10T19:37:17Z" level=info msg="Running command docker build --pull -f Dockerfile -t mycontainerregistry.azurecr.io/helloworld:aa2 ."
Sending build context to Docker daemon  209.9kB
Step 1/5 : FROM node:9-alpine
9-alpine: Pulling from library/node
Digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
Status: Image is up to date for node:9-alpine
 ---> 7af437a39ec2
Step 2/5 : COPY . /src
 ---> 48a7735fa94e

[...]

26b0c207c4a9: Pushed
917e7cdebc8b: Pushed
aa2: digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89 size: 1367
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" mycontainerregistrtyy.azurecr.io/helloworld:aa2"
"["mycontainerregistrtyy.azurecr.io/helloworld@sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89"]"
time="2018-05-10T19:37:57Z" level=info msg="Running command docker inspect --format \"{{json .RepoDigests}}\" node:9-alpine"
"["node@sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3"]"
ACR Builder discovered the following dependencies:
- image:
    registry: mycontainerregistrtyy.azurecr.io
    repository: helloworld
    tag: aa2
    digest: sha256:6975f01e2e202c084581e676acbe6047788fbe616836328b0b31ce8c58e9fc89
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/node
    tag: 9-alpine
    digest: sha256:5149aec8f508d48998e6230cdc8e6832cba192088b442c8ef7e23df3c6892cd3
  git:
    git-head-revision: 920f16cfafa36d0bc3f397c3dd48185a03499404

Build complete
Build ID: aa2 was successful after 46.491407373s
```

## <a name="view-build-status"></a>ビルドの状態を表示する

手動でトリガーしていないビルドの進行状態を表示すると役に立つことがあります。 たとえば、ソース コードのコミットによってトリガーされるビルドのトラブルシューティングを行うような場合です。 このセクションでは、手動のビルドをトリガーしますが、ビルド ログをコンソールにストリーミングする既定の動作を抑制します。 その後、`az acr build-task logs` コマンドを使って実行中のビルドを監視します。

最初に、前に行ったように手動でビルドをトリガーします。ただし、`--no-logs` 引数を指定してコンソールへのログ出力を抑制します。

```azurecli-interactive
az acr build-task run --registry $ACR_NAME --name buildhelloworld --no-logs
```

次に、`az build-task logs` コマンドを使って、現在実行中のビルドのログを表示します。

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

現在実行中のビルドのログがコンソールにストリーミングされ、次のような出力が表示されます (ここでは切り捨てたものを示してあります)。

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa3

[...]

Build complete
Build ID: aa3 was successful after 1m14.26397548s
```

## <a name="trigger-a-build-with-a-commit"></a>コミットでビルドをトリガーする

手動実行によるビルド タスクのテストが済んだので、次に、ソース コードを変更して自動的にトリガーします。

最初に、[リポジトリ][sample-repo]のローカルな複製が含まれるディレクトリにいることを確認します。

```azurecli-interactive
cd acr-build-helloworld-node
```

次に、以下のコマンドを実行することで、新しいファイルを作成してコミットし、GitHub 上のリポジトリのフォークにプッシュします。

```azurecli-interactive
echo "Hello World!" > hello.txt
git add hello.txt
git commit -m "Testing ACR Build"
git push origin master
```

`git push` コマンドを実行するときに、GitHub の資格情報の入力を求められることがあります。 GitHub のユーザー名を指定し、前にパスワードに対して作成した個人用アクセス トークン (PAT) を入力します。

```console
$ git push origin master
Username for 'https://github.com': <github-username>
Password for 'https://githubuser@github.com': <personal-access-token>
```

リポジトリにコミットをプッシュすると、ACR Build によって作成された webhook が発生して、Azure Container Registry でのビルドを開始します。 現在実行中のビルドのビルド ログを表示し、ビルドの進行状況を確認および監視します。

```azurecli-interactive
az acr build-task logs --registry $ACR_NAME
```

次のような出力が表示され、現在実行中の (または最後に実行された) ビルドが示されます。

```console
$ az acr build-task logs --registry $ACR_NAME
Showing logs for the last updated build
Build ID: aa4

[...]

Build complete
Build ID: aa4 was successful after 39.164385024s
```

## <a name="list-builds"></a>ビルドの一覧を表示する

ユーザーのレジストリで ACR Build が完了したビルドの一覧を表示するには、[az acr build-task list-builds][az-acr-build-task-list-builds] コマンドを実行します。

```azurecli-interactive
az acr build-task list-builds --registry $ACR_NAME --output table
```

次のようなコマンドの出力が表示されます。 ACR Build が実行したビルドが表示され、最新のビルドの [TRIGGER] 列には "Git Commit" と表示されます。

```console
$ az acr build-task list-builds --registry $ACR_NAME --output table
BUILD ID    TASK             PLATFORM    STATUS     TRIGGER     STARTED               DURATION
----------  ---------------  ----------  ---------  ----------  --------------------  ----------
aa4         buildhelloworld  Linux       Succeeded  Git Commit  2018-05-10T19:49:40Z  00:00:45
aa3         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:41:50Z  00:01:20
aa2         buildhelloworld  Linux       Succeeded  Manual      2018-05-10T19:37:11Z  00:00:50
aa1                          Linux       Succeeded  Manual      2018-05-10T19:10:14Z  00:00:55
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ビルド タスクを使って、Git リポジトリにソース コードをコミットすると Azure でコンテナー イメージのビルドが自動的にトリガーされるようにする方法を説明しました。 次のチュートリアルに進んで、コンテナー イメージの基本イメージが更新されたらビルドをトリガーするビルド タスクを作成する方法を学習してください。

> [!div class="nextstepaction"]
> [基本イメージ更新時のビルドを自動化する](container-registry-tutorial-base-image-update.md)

<!-- LINKS - External -->
[sample-repo]: https://github.com/Azure-Samples/acr-build-helloworld-node

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build-task]: /cli/azure/acr#az-acr-build-task
[az-acr-build-task-create]: /cli/azure/acr#az-acr-build-task-create
[az-acr-build-task-run]: /cli/azure/acr#az-acr-build-task-run
[az-acr-build-task-list-builds]: /cli/azure/acr#az-acr-build-task-list-build

<!-- IMAGES -->
[build-task-01-new-token]: ./media/container-registry-tutorial-build-tasks/build-task-01-new-token.png
[build-task-02-generated-token]: ./media/container-registry-tutorial-build-tasks/build-task-02-generated-token.png
