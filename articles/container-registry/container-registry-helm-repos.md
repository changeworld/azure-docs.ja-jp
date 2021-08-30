---
title: Helm グラフの保存
description: Azure Container Registry のリポジトリを使用して Kubernetes アプリケーションの Helm グラフを保存する方法について説明します
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: cdc4b0c6fb5aabdb96597cfbbe151598b16a2cc0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438910"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Azure コンテナー レジストリに対する Helm グラフのプッシュおよびプル

アプリケーションを Kubernetes 用に簡単に管理し、デプロイするには、[オープン ソースの Helm パッケージ マネージャー][helm]を使用します。 Helm を使用すると、アプリケーション パッケージは[グラフ](https://helm.sh/docs/topics/charts/)として定義されます。これは収集され、[Helm グラフ リポジトリ](https://helm.sh/docs/topics/chart_repository/)に格納されます。

この記事では、Helm 3 のコマンドを使用し、グラフを [OCI 成果物](container-registry-image-formats.md#oci-artifacts)として格納して、Azure コンテナー レジストリで Helm グラフ リポジトリをホストする方法について説明します。 多くのシナリオでは、開発したアプリケーション用の独自のグラフを構築し、アップロードすることになります。 独自の Helm グラフを作成する方法の詳細については、「[Chart Template Developer's Guide (グラフ テンプレート開発者ガイド)][develop-helm-charts]」を参照してください。 また、別の Helm リポジトリから既存の Helm グラフを格納することもできます。

## <a name="helm-3-or-helm-2"></a>Helm 3 か Helm 2 か

Helm グラフを格納、管理、インストールするには、Helm CLI のコマンドを使用します。 Helm のメジャー リリースには Helm 3 と Helm 2 が含まれています。 バージョン間の違いの詳細については、[バージョンに関する FAQ](https://helm.sh/docs/faq/) を参照してください。 

Azure Container Registry で Helm グラフをホストするには、Helm3 を使用する必要があります。 Helm 3 の場合:

* Azure Container Registry のリポジトリに、Helm グラフを格納して管理できます
* Helm グラフをレジストリに [OCI 成果物](container-registry-image-formats.md#oci-artifacts)として格納します。 Azure Container Registry では、Helm チャートなどの OCI 成果物に対する GA サポートが提供されます。
* `helm registry login` または `az acr login` コマンドを使用して、レジストリで認証します。
* `helm chart` コマンドを使用して、レジストリ内の Helm グラフをプッシュ、プル、管理します
* `helm install` を使用して、ローカル リポジトリ キャッシュから Kubernetes クラスターにグラフをインストールします。

### <a name="feature-support"></a>機能サポート

Azure Container Registry では、Helm 3 (現行) と Helm 2 (非推奨) のどちらを使用しているかに応じて、固有の Helm グラフ管理機能がサポートされます。

| 機能 | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| `az acr helm` コマンドを使用してグラフを管理する | :heavy_check_mark: | |
| OCI 成果物としてグラフを格納する | | :heavy_check_mark:  |
| Azure portal で `az acr repository` コマンドおよび **リポジトリ** ブレードを使用してグラフを管理する| | :heavy_check_mark:  |


> [!NOTE]
> Helm 3 以降、Helm 2 クライアントで使用する [az acr helm][az-acr-helm] コマンドは非推奨となります。 コマンドが削除される少なくとも 3 か月前に通知が提供されます。

### <a name="chart-version-compatibility"></a>グラフのバージョンの互換性

次の Helm [グラフのバージョン](https://helm.sh/docs/topics/charts/#the-apiversion-field) は Azure Container Registry に格納でき、Helm 2 および Helm 3 クライアントによってインストールされます。 

| バージョン | Helm 2 | Helm 3 |
| ---- | ---- | ---- |
| apiVersion v1 | :heavy_check_mark: | :heavy_check_mark: |
| apiVersion v2 | | :heavy_check_mark: |

### <a name="migrate-from-helm-2-to-helm-3"></a>Helm 2 から Helm 3 への移行

Helm 2 と Azure Container Registry を使用してグラフを格納してデプロイしたことがある場合、Helm 3 に移行することをお勧めします。 参照トピック

* Helm ドキュメントの [Helm 2 から 3 への移行](https://helm.sh/docs/topics/v2_v3_migration/)
* この記事の後半にある[レジストリを移行して Helm OCI 成果物を格納する](#migrate-your-registry-to-store-helm-oci-artifacts)

## <a name="prerequisites"></a>前提条件

この記事のシナリオでは、次のリソースが必要です。

- Azure サブスクリプションの **Azure コンテナー レジストリ**。 必要に応じて、[Azure portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用してレジストリを作成します。
- **Helm クライアント バージョン 3.1.0 以降** - 現在のバージョンを確認するには、`helm version` を実行します。 Helm のインストール方法とアップグレード方法について詳しくは、「[Installing Helm (Helm のインストール)][helm-install]」をご覧ください。
- Helm グラフをインストールする **Kubernetes クラスター**。 必要に応じて、[Azure Kubernetes Service クラスター][aks-quickstart]を作成します。 
- **Azure CLI バージョン 2.0.71 以降** - バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="enable-oci-support"></a>OCI のサポートを有効にする

`helm version` コマンドを使用して、Helm 3 がインストールされていることを確認します。

```console
helm version
```

次の環境変数を設定して、Helm 3 クライアントで OCI のサポートを有効にします。 現時点では、このサポートは試験段階であり、変更される可能性があります。 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>サンプル グラフを作成する

次のコマンドを使用して、テスト グラフを作成します。

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

基本的な例として、ディレクトリを `templates` フォルダーに変更し、まずその内容を削除します。

```console
cd hello-world/templates
rm -rf *
```

次のコマンドを実行して、`templates` フォルダー内に `configmap.yaml` というファイルを作成します。

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

この例の作成と実行の詳細については、Helm ドキュメントの「[Getting Started](https://helm.sh/docs/chart_template_guide/getting_started/)」を参照してください。

## <a name="save-chart-to-local-registry-cache"></a>ローカル レジストリ キャッシュにグラフを保存する

ディレクトリを `hello-world` サブディレクトリに変更します。 次に、`helm chart save` を実行してグラフのコピーをローカルに保存し、レジストリの完全修飾名 (すべて小文字) とターゲット リポジトリおよびタグを使用して別名を作成します。 

次の例では、レジストリ名は *mycontainerregistry*、ターゲット リポジトリは *helm/hello-world*、ターゲット グラフ タグは *0.1.0* です。 依存関係を正常に取得するには、ターゲット グラフ イメージの名前とタグが `Chart.yaml` の名前とバージョンと一致している必要があります。

```console
cd ..
helm chart save . hello-world:0.1.0
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

`helm chart list` を実行して、ローカル レジストリ キャッシュにグラフが保存されたことを確認します。 出力は次のようになります。

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:0.1.0                                        hello-world      0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:0.1.0    hello-world      0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>レジストリで認証する

`helm registry login` を実行してレジストリで認証します。 サービス プリンシパルの資格情報やリポジトリスコープのトークンなど、シナリオに適した[レジストリ資格情報](container-registry-authentication.md)を渡すことができます。

たとえば、レジストリに対する[プルおよびプッシュ権限 (AcrPush ロール) がある Azure Active Directory サービス プリンシパル](container-registry-auth-service-principal.md#create-a-service-principal)を作成します。 次に、サービス プリンシパルの資格情報を `helm registry login` に対して指定します。 次の例では、環境変数を使用してパスワードを指定しています。

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

> [!TIP]
> [個々の Azure AD ID](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) でレジストリにログインして、Helm グラフをプッシュしたりプルしたりすることもできます。

## <a name="push-chart-to-registry"></a>レジストリにグラフをプッシュする

Helm 3 CLI で `helm chart push` コマンドを実行して、完全修飾ターゲット リポジトリにグラフをプッシュします。

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

プッシュが成功すると、出力は次のようになります。

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>リポジトリのグラフ一覧

Azure コンテナー レジストリに格納されているイメージと同様に、[az acr repository][az-acr-repository] コマンドを使用して、グラフをホストしているリポジトリと、グラフのタグとマニフェストを表示できます。 

たとえば、[az acr repository show][az-acr-repository-show] を実行して、前の手順で作成したリポジトリのプロパティを表示します。

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

出力は次のようになります。

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

[az acr repository show-manifests][az-acr-repository-show-manifests] コマンドを実行して、リポジトリに格納されているグラフの詳細を表示します。 次に例を示します。

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

出力 (この例では省略されています) には、`application/vnd.cncf.helm.config.v1+json` の `configMediaType` が表示されます。

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "0.1.0"
    ]
```

## <a name="pull-chart-to-local-cache"></a>ローカル キャッシュにグラフをプルする

Helm グラフを Kubernetes にインストールするには、グラフがローカル キャッシュにある必要があります。 この例では、最初に `helm chart remove` を実行して、`mycontainerregistry.azurecr.io/helm/hello-world:0.1.0` という名前の既存のローカル グラフを削除します。

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

`helm chart pull` を実行して、Azure コンテナー レジストリからローカル キャッシュにグラフをダウンロードします。

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:0.1.0
```

## <a name="export-helm-chart"></a>Helm グラフをエクスポートする

グラフをさらに操作するには、`helm chart export` を使用してローカル ディレクトリにエクスポートします。 たとえば、プルしたグラフを `install` ディレクトリにエクスポートします。

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:0.1.0 \
  --destination ./install
```

リポジトリにエクスポートされたグラフの情報を表示するには、グラフをエクスポートしたディレクトリで `helm show chart` コマンドを実行します。

```console
cd install
helm show chart hello-world
```

次の出力例に示されているように、グラフの最新バージョンに関する詳細情報が Helm によって返されます。

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Helm グラフをインストールする

ローカル キャッシュにプルしてエクスポートした Helm グラフをインストールするには、`helm install` を実行します。 *myhelmtest* などのリリース名を指定するか、`--generate-name` パラメーターを渡します。 次に例を示します。

```console
helm install myhelmtest ./hello-world
```

グラフのインストールが成功した後の出力は次のようになります。

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

インストールされていることを確認するには、`helm get manifest` コマンドを実行します。 

```console
helm get manifest myhelmtest
```

このコマンドは、`configmap.yaml` テンプレート ファイル内の YAML データを返します。

クラスター上のグラフのリリースをアンインストールするには、`helm uninstall` を実行します。

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>レジストリからグラフを削除する

コンテナー レジストリからグラフを削除するには、[az acr repository delete][az-acr-repository-delete] コマンドを使用します。 次のコマンドを実行し、プロンプトが表示されたら操作を確認します。

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:0.1.0
```

## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>レジストリを移行して Helm OCI 成果物を格納する

以前に Helm 2 と `az acr helm` コマンドを使用して Azure Container Registry をグラフ リポジトリとして設定した場合、Helm 3 クライアントに[アップグレード][helm-install]することをお勧めします。 次に、次の手順に従って、グラフを OCI 成果物としてレジストリに格納します。 

> [!IMPORTANT]
> * Helm 2 スタイル (index.yaml ベース) のグラフ リポジトリから OCI 成果物リポジトリへの移行が完了したら、Helm CLI と `az acr repository` コマンドを使用してグラフを管理します。 この記事の前のセクションをご覧ください。 
> * Helm OCI 成果物リポジトリは、`helm search` や `helm repo list` などの Helm コマンドを使用して検出できません。 グラフを OCI 成果物として格納するために使用される Helm コマンドの詳細については、[Helm のドキュメント](https://helm.sh/docs/topics/registries/)を参照してください。

### <a name="enable-oci-support"></a>OCI のサポートを有効にする

Helm 3 クライアントを使用していることを確認します。

```console
helm version
```

Helm 3 クライアントで OCI サポートを有効にします。 現時点では、このサポートは試験段階であり、変更される可能性があります。

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>現在のグラフを一覧表示する

レジストリに現在格納されているグラフを一覧表示します。ここでは *myregistry* という名前です。

```console
helm search repo myregistry
```

出力には、グラフとグラフのバージョンが表示されます。

```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```

### <a name="save-charts-as-oci-artifacts"></a>OCI 成果物としてグラフを保存する

リポジトリのグラフごとに、ローカルにグラフをプルし、OCI 成果物として保存します。 例:

```console 
helm pull myregisry/ingress-nginx --untar
cd ingress-nginx
helm chart save . myregistry.azurecr.io/ingress-nginx:3.20.1
```

### <a name="push-charts-to-registry"></a>レジストリにグラフをプッシュする

レジストリにログインします。

```azurecli
az acr login --name myregistry
```

各グラフをレジストリにプッシュします。

```console
helm chart push myregistry.azurecr.io/ingress-nginx:3.20.1
```

グラフをプッシュした後、それがレジストリに格納されているのを確認します。

```azurecli
az acr repository list --name myregistry
```

すべてのグラフをプッシュした後、必要に応じて、Helm 2 スタイルのグラフ リポジトリをレジストリから削除します。 これにより、レジストリ内のストレージが減少します。

```console
helm repo remove myregistry
```

## <a name="next-steps"></a>次のステップ

* Helm グラフを作成してデプロイする方法の詳細については、[Helm グラフの開発][develop-helm-charts]に関するページを参照してください。
* [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md) での Helm を使用したアプリケーションのインストールについて詳細を確認します。
* Helm グラフは、コンテナーのビルド プロセスの一部として使用できます。 詳細については、[Azure Container Registry タスクの使用][acr-tasks]に関するページを参照してください。

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
