---
title: Azure Container Instances に gitRepo ボリュームをマウントする
description: gitRepo ボリュームをマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/16/2018
ms.author: marsma
ms.openlocfilehash: e40d841c07534c9c0074c038d1e3c6e435265564
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
ms.locfileid: "32166781"
---
# <a name="mount-a-gitrepo-volume-in-azure-container-instances"></a>Azure Container Instances に gitRepo ボリュームをマウントする

*gitRepo ボリューム*をマウントし、Git リポジトリの複製をコンテナー インスタンスに作成する方法について説明します。

> [!NOTE]
> *gitRepo* ボリュームのマウントは現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="gitrepo-volume"></a>gitRepo ボリューム

*gitRepo* ボリュームはディレクトリをマウントし、コンテナーの起動時、指定の Git リポジトリの複製をそのディレクトリに作成します。 コンテナー インスタンスで *gitRepo* ボリュームを使用することで、そのためのコードを自分のアプリケーションに追加する必要がなくなります。

*gitRepo* ボリュームをマウントするとき、次の 3 つのプロパティを設定し、ボリュームを構成できます。

| プロパティ | 必須 | [説明] |
| -------- | -------- | ----------- |
| `repository` | [はい] | 完全 URL。複製を作成する Git リポジトリの `http://` または `https://` も含まれます。|
| `directory` | いいえ  | リポジトリの複製を作成するディレクトリ。 パスには "`..`" を含めることができません。  "`.`" を指定すると、リポジトリの複製がボリュームのディレクトリに作成されます。 指定しない場合、ボリューム ディレクトリ内の指定の下位ディレクトリに Git リポジトリの複製が作成されます。 |
| `revision` | いいえ  | 複製を作成するリビジョンのコミット ハッシュ。 指定しない場合、`HEAD` リビジョンが複製されます。 |

## <a name="mount-gitrepo-volume-azure-cli"></a>gitRepo ボリュームのマウント: Azure CLI

[Azure CLI](/cli/azure)を使ってコンテナー インスタンスをデプロイするときに gitRepo ボリュームをマウントするには、[az container create][az-container-create] コマンドに `--gitrepo-url` および `--gitrepo-mount-path` パラメーターを指定します。 必要に応じて、複製先となるボリューム内のディレクトリ (`--gitrepo-dir`) と複製されるリビジョンのコミット ハッシュ (`--gitrepo-revision`) を指定することもできます。

この例のコマンドは、[aci-helloworld][aci-helloworld] サンプル アプリケーションをコンテナー インスタンス内の `/mnt/aci-helloworld`に複製しています。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name hellogitrepo \
    --image microsoft/aci-helloworld \
    --dns-name-label aci-demo \
    --ports 80 \
    --gitrepo-url https://github.com/Azure-Samples/aci-helloworld \
    --gitrepo-mount-path /mnt/aci-helloworld
```

gitRepo ボリュームがマウントされたことを確認するには、[az container exec][az-container-exec] を使ってコンテナー内のシェルを起動し、ディレクトリを一覧表示します。

```console
$ az container exec --resource-group myResourceGroup --name hellogitrepo --exec-command /bin/sh
/usr/src/app # ls -l /mnt/aci-helloworld/
total 16
-rw-r--r--    1 root     root           144 Apr 16 16:35 Dockerfile
-rw-r--r--    1 root     root          1162 Apr 16 16:35 LICENSE
-rw-r--r--    1 root     root          1237 Apr 16 16:35 README.md
drwxr-xr-x    2 root     root          4096 Apr 16 16:35 app
```

## <a name="mount-gitrepo-volume-resource-manager"></a>gitRepo ボリュームのマウント: Resource Manager

[Azure Resource Manager テンプレート](/azure/templates/microsoft.containerinstance/containergroups)を使ってコンテナー インスタンスをデプロイするときに gitRepo ボリュームをマウントするには、最初にテンプレートのコンテナー グループの `properties` セクションにある `volumes` 配列を設定します。 次に、*gitRepo* ボリュームをマウントするコンテナー グループ内の各コンテナーに対して、コンテナー定義の `properties` セクションで `volumeMounts` 配列を設定します。

たとえば、次の Resource Manager テンプレートでは、1 つのコンテナーから構成されるコンテナー グループが作成されます。 このコンテナーによって、*gitRepo* ボリューム ブロックにより指定される 2 つの GitHub リポジトリが複製されます。 2 つ目のボリュームには、複製先のディレクトリを指定する追加プロパティと複製する特定のリビジョンのコミット ハッシュが含まれています。

<!-- https://github.com/Azure/azure-docs-json-samples/blob/master/container-instances/aci-deploy-volume-gitrepo.json -->
[!code-json[volume-gitrepo](~/azure-docs-json-samples/container-instances/aci-deploy-volume-gitrepo.json)]

先のテンプレートに定義されていた 2 つの複製リポジトリのディレクトリ構造は結果的に次のようになります。

```
/mnt/repo1/aci-helloworld
/mnt/repo2/my-custom-clone-directory
```

Azure Resource Manager テンプレートによるコンテナー インスタンスのデプロイ例を見るには、[Azure Container Instances での複数コンテナーのデプロイ](container-instances-multi-container-group.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Azure Container Instances にその他の種類のボリュームをマウントする方法について学習してください。

* [Azure Container Instances に Azure ファイル共有をマウントする](container-instances-volume-azure-files.md)
* [Azure Container Instances に emptyDir ボリュームをマウントする](container-instances-volume-emptydir.md)
* [Azure Container Instances にシークレット ボリュームをマウントする](container-instances-volume-secret.md)

<!-- LINKS - External -->
[aci-helloworld]: https://github.com/Azure-Samples/aci-helloworld

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec