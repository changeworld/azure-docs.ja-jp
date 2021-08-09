---
title: Kubernetes プル シークレットを使用して Azure コンテナー レジストリの認証を行う
description: サービス プリンシパルを使用してプル シークレットを作成することにより、Kubernetes クラスターに、Azure コンテナー レジストリ内のイメージへのアクセスを提供する方法について説明します
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 06/02/2021
ms.openlocfilehash: 149035de0fc84c75cdcaa73c91d6cd5379c53498
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111439622"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster-using-a-pull-secret"></a>プル シークレットを使用して Azure コンテナー レジストリから Kubernetes クラスターにイメージをプルする

[minikube](https://minikube.sigs.k8s.io/) や [kind](https://kind.sigs.k8s.io/) などの "ローカルな" Kubernetes クラスターを含め、任意の Kubernetes クラスターで、Azure コンテナー レジストリをコンテナー イメージのソースとして使用できます。 この記事では、Azure コンテナー レジストリの資格情報を使用して Kubernetes プル シークレットを作成する方法について説明します。 次に、シークレットを使用して、ポッド デプロイ内の Azure コンテナー レジストリからイメージをプルします。

この例では、Azure Active Directory の[サービス プリンシパルの資格情報](container-registry-auth-service-principal.md)を使用して、プル シークレットを作成します。 また、[リポジトリ スコープのアクセス トークン](container-registry-repository-scoped-permissions.md)など、他の Azure コンテナー レジストリの資格情報を使用して、プル シークレットを構成することもできます。

> [!NOTE]
> プル シークレットは一般的に使用されますが、追加の管理オーバーヘッドが発生します。 [Azure Kubernetes Service](../aks/intro-kubernetes.md) を使用している場合は、クラスターのマネージド ID やサービス プリンシパルなどの[他のオプション](authenticate-kubernetes-options.md)を使用し、各ポッドで `imagePullSecrets` を追加設定することなく、イメージを安全にプルすることをお勧めします。

## <a name="prerequisites"></a>前提条件

この記事では、プライベート Azure コンテナー レジストリを作成済みであることを前提としています。 また、`kubectl` コマンドラインツールを介して、Kubernetes クラスターを実行し、アクセス可能にする必要があります。

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

サービス プリンシパルのパスワードを保存していない場合や覚えていない場合は、[az ad sp credential reset][az-ad-sp-credential-reset] コマンドでリセットできます。

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

このコマンドでは、サービス プリンシパルの新しい有効なパスワードが返されます。

## <a name="create-an-image-pull-secret"></a>イメージのプル シークレットを作成する

Kubernetes では、*イメージのプル シークレット* を使用して、認証に必要な情報をレジストリに格納します。 Azure コンテナー レジストリのプル シークレットを作成するには、サービス プリンシパルの ID、パスワード、およびレジストリ URL を指定します。 

次の `kubectl` コマンドを使用してイメージのプル シークレットを作成します。

```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```
各値の説明:

| 値 | 説明 |
| :--- | :--- |
| `secret-name` | イメージのプル シークレットの名前 (例: *acr-secret)* |
| `namespace` | シークレット格納先の Kubernetes 名前空間 <br/> シークレットを、既定の名前空間以外の名前空間に配置する場合にのみ必要 |
| `container-registry-name` | Azure コンテナー レジストリの名前。例: *myregistry*<br/><br/>`--docker-server` はレジストリ ログイン サーバーの完全修飾名です  |
| `service-principal-ID` | レジストリにアクセスするために Kubernetes によって使用されるサービス プリンシパルの ID |
| `service-principal-password` | サービス プリンシパルのパスワード |

## <a name="use-the-image-pull-secret"></a>イメージのプル シークレットを使用する

イメージのプル シークレットを作成したら、それを使用して Kubernetes のポッドとデプロイを作成できます。 デプロイ ファイルの `imagePullSecrets` の下にシークレットの名前を指定します。 次に例を示します。

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: myregistry.azurecr.io/my-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

前の例では、`my-awesome-app:v1` は、Azure コンテナー レジストリからプルするイメージの名前であり、`acr-secret` は、レジストリにアクセスするために作成したプル シークレットの名前です。 ポッドをデプロイすると、イメージがクラスター上にまだ存在しない場合、Kubernetes によって、レジストリからイメージが自動的にプルされます。


## <a name="next-steps"></a>次のステップ

* サービス プリンシパルと Azure Container Registry の使用ついて詳しくは、「[サービス プリンシパルによる Azure Container Registry 認証](container-registry-auth-service-principal.md)」を参照してください
* [Kubernetes のドキュメント](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod)でイメージのプル シークレットの詳細を確認します


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset