---
title: Azure CLI および YAML を使用して Azure Container Instances に複数コンテナー グループをデプロイする
description: Azure CLI および YAML ファイルを使用して複数のコンテナーを含むコンテナー グループを Azure Container Instances にデプロイする方法を説明します。
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 07/17/2018
ms.author: marsma
ms.openlocfilehash: 1d1885112b8e7f7b1e187073c86d561eb57fd23f
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114465"
---
# <a name="deploy-a-multi-container-container-group-with-yaml"></a>YAML を使用して複数のコンテナーを含むコンテナー グループをデプロイする

Azure Container Instances では、[コンテナー グループ](container-instances-container-groups.md)を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 複数のコンテナーを含むコンテナー グループは、サービスが 2 番目の接続プロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーをビルドするときに便利です。

Azure CLI を使用して複数コンテナー グループをデプロイする方法は 2 つあります。

* YAML ファイルのデプロイ (この記事)
* [Resource Manager テンプレートのデプロイ](container-instances-multi-container-group.md)

YAML フォーマットは簡潔であるため、コンテナー インスタンス*のみ*を含むデプロイには YAML ファイルを使用するデプロイをお勧めします。 コンテナー インスタンスのデプロイ時にその他の Azure サービス リソース (Azure Files 共有など) をデプロイする必要がある場合、Resource Manager テンプレートのデプロイをお勧めします。

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

## <a name="configure-the-yaml-file"></a>YAML ファイルを構成する

Azure CLI の [az container create][az-container-create] コマンドを使用して複数のコンテナーを含むコンテナー グループをデプロイするには、YAML ファイルでコンテナー グループ構成を指定してから YAML ファイルをパラメーターとしてコマンドに渡す必要があります。

まず、次の YAML を **deploy-aci.yaml** という名前の新しいファイルにコピーします。

この YAML ファイルでは、2 つのコンテナー、パブリック IP アドレス、および公開された 2 つのポートを備える "myContainerGroup" というコンテナー グループが定義されます。 グループの最初のコンテナーでは、インターネットに接続する Web アプリケーションが実行されます。 2 番目のコンテナーであるサイドカーは、コンテナー グループのローカル ネットワークを介して最初のコンテナーで実行されている Web アプリケーションに定期的に HTTP 要求を行います。

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: microsoft/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: microsoft/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
    - protocol: tcp
      port: '8080'
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="deploy-the-container-group"></a>コンテナー グループをデプロイする

[az group create][az-group-create] コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az container create][az-container-create] コマンドでコンテナー グループをデプロイし、YAML ファイルを引数として渡します。

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

数秒以内に、Azure から最初の応答を受信します。

## <a name="view-deployment-state"></a>デプロイ状態の表示

デプロイの状態を表示するには、次の [az container show][az-container-show] コマンドを使用します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

実行中のアプリケーションを表示するには、ご利用のブラウザーでその IP アドレスにアクセスします。 たとえば、次の出力例では IP は `52.168.26.124` です。

```bash
Name              ResourceGroup    ProvisioningState    Image                                                           IP:ports               CPU/Memory       OsType    Location
----------------  ---------------  -------------------  --------------------------------------------------------------  ---------------------  ---------------  --------  ----------
myContainerGroup  myResourceGroup  Succeeded            microsoft/aci-helloworld:latest,microsoft/aci-tutorial-sidecar  52.168.26.124:80,8080  1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-logs"></a>ログを表示する。

コンテナーのログ出力を表示するには、[az container logs][az-container-logs] コマンドを使用します。 `--container-name` 引数は、プルするログが含まれるコンテナーを指定します。 この例では、最初のコンテナーが指定されています。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

出力:

```console
listening on port 80
::1 - - [09/Jan/2018:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [09/Jan/2018:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

サイドカー コンテナーのログを表示するには、2 番目のコンテナー名を指定して、同じコマンドを実行します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

出力:

```console
Every 3s: curl -I http://localhost                          2018-01-09 23:25:11

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Tue, 09 Jan 2018 23:25:11 GMT
Connection: keep-alive
```

このように、サイドカーは、グループのローカル ネットワーク経由で、メインの Web アプリケーションに定期的に HTTP 要求を実行して、アプリケーションが実行中であることを確認します。 このサイドカーの例は、200 OK 以外の HTTP 応答コードを受け取ったときに、アラートをトリガーするように拡張できます。

## <a name="deploy-from-private-registry"></a>プライベート レジストリからデプロイする

プライベート コンテナー イメージ レジストリを使用するには、お使いの環境に合わせて変更された値を含む次の YAML を含めます。

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

たとえば次の YAML は、"myregistry" という名前のプライベート Azure Container Registry からイメージが抽出された 1 つのコンテナーを含むコンテナー グループをデプロイします。

```YAML
apiVersion: 2018-06-01
location: eastus
name: myContainerGroup2
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: myregistry.azurecr.io/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: '80'
  imageRegistryCredentials:
  - server: myregistry.azurecr.io
    username: myregistry
    password: REGISTRY_PASSWORD
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

## <a name="export-container-group-to-yaml"></a>コンテナー グループを YAML にエクスポートする

既存のコンテナー グループの構成を YAML ファイルにエクスポートできます。そのためには、Azure CLI の [az container export][az-container-export] コマンドを使用します。

エクスポートはコンテナー グループの構成を保存する際に便利です。バージョン コントロールでコンテナー グループの構成を「コードとしての構成」として格納できます。 または、エクスポートしたファイルを YAML で新しい構成を作成する際の出発点として使用します。

次の [az container export][az-container-export] コマンドを発行して、作成済みのコンテナー グループの構成をエクスポートします。

```azurecli-interactive
az container export --resource-group myResourceGroup --name myContainerGroup --file deployed-aci.yaml
```

コマンドが成功した場合出力は表示されませんが、ファイルの内容を表示して結果を確認できます。 たとえば、`head` を含む最初の数行です。

```console
$ head deployed-aci.yaml
additional_properties: {}
apiVersion: '2018-06-01'
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      environmentVariables: []
      image: microsoft/aci-helloworld:latest
```

## <a name="next-steps"></a>次の手順

この記事では、複数コンテナーの Azure コンテナー インスタンスのデプロイに必要な手順について説明しました。 プライベート Azure コンテナー レジストリの使用を含むエンド ツー エンドの Azure Container Instances の操作については、Azure Container Instances チュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル][aci-tutorial]

<!-- LINKS - External -->
[cli-issue-6525]: https://github.com/Azure/azure-cli/issues/6525

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-export]: /cli/azure/container#az-container-export
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
