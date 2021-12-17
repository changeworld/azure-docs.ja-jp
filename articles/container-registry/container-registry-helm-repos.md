---
title: Helm チャートの保存
description: Azure Container Registry のリポジトリを使用して Kubernetes アプリケーションの Helm チャートを保存する方法について説明します
ms.topic: article
ms.date: 10/20/2021
ms.openlocfilehash: 5c96df6458a1f1fc40f4033d367c988c246a0fde
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2021
ms.locfileid: "132135776"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Azure コンテナー レジストリに対する Helm チャートのプッシュおよびプル

アプリケーションを Kubernetes 用に簡単に管理し、デプロイするには、[オープン ソースの Helm パッケージ マネージャー][helm]を使用します。 Helm を使用すると、アプリケーション パッケージは[チャート](https://helm.sh/docs/topics/charts/)として定義されます。これは収集され、[Helm チャート リポジトリ](https://helm.sh/docs/topics/chart_repository/)に格納されます。

<<<<<<< HEAD
この記事では、Helm 3 のコマンドを使用して、Azure コンテナー レジストリで Helm チャート リポジトリをホストする方法について説明します。 多くのシナリオでは、開発したアプリケーション用の独自のチャートを構築し、アップロードすることになります。 独自の Helm チャートを作成する方法の詳細については、「[Chart Template Developer's Guide (チャート テンプレート開発者ガイド)][develop-helm-charts]」を参照してください。 また、別の Helm リポジトリから既存の Helm チャートを格納することもできます。

> [!IMPORTANT]
> Azure Container Registry での Helm チャートのサポートは現在プレビュー段階です。 プレビュー版は、追加の[使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="helm-3-or-helm-2"></a>Helm 3 か Helm 2 か

Helm チャートの格納、管理、およびインストールには、Helm クライアントと Helm CLI を使用します。 Helm クライアントのメジャー リリースには、Helm 3 と Helm 2 があります。 バージョン間の違いの詳細については、[バージョンに関する FAQ](https://helm.sh/docs/faq/) を参照してください。 
=======
この記事では、Helm 3 のコマンドを使用し、グラフを [OCI 成果物](container-registry-image-formats.md#oci-artifacts)として格納して、Azure コンテナー レジストリで Helm グラフ リポジトリをホストする方法について説明します。 多くのシナリオでは、開発したアプリケーション用の独自のグラフを構築し、アップロードすることになります。 独自の Helm グラフを作成する方法の詳細については、「[Chart Template Developer's Guide (グラフ テンプレート開発者ガイド)][develop-helm-charts]」を参照してください。 また、別の Helm リポジトリから既存の Helm グラフを格納することもできます。

> [!IMPORTANT]
> この記事は、バージョン **3.7.1** の時点の Helm 3 コマンドで更新されました。 Helm 3.7.1 には、以前のバージョンの Helm 3 で導入された、Helm CLI コマンドと OCI サポートへの変更が含まれています。 

## <a name="helm-3-or-helm-2"></a>Helm 3 か Helm 2 か

Helm グラフを格納、管理、インストールするには、Helm CLI のコマンドを使用します。 Helm のメジャー リリースには Helm 3 と Helm 2 が含まれています。 バージョン間の違いの詳細については、[バージョンに関する FAQ](https://helm.sh/docs/faq/) を参照してください。 
>>>>>>> repo_sync_working_branch

Azure Container Registry で Helm チャートをホストするには、Helm3 を使用する必要があります。 Helm 3 の場合:

<<<<<<< HEAD
* Azure コンテナー レジストリに 1 つ以上の Helm リポジトリを作成できます
* Helm 3 チャートをレジストリに [OCI 成果物](container-registry-image-formats.md#oci-artifacts)として格納します。 現在、OCI の Helm 3 のサポートは "*試験段階*" です。
* `helm registry login` コマンドを使用して、レジストリで認証します。
* Helm CLI で `helm chart` コマンドを使用して、レジストリ内の Helm チャートをプッシュ、プル、および管理します
* `helm install` を使用して、ローカル リポジトリ キャッシュから Kubernetes クラスターにチャートをインストールします。
> [!NOTE]
> Helm 3 以降、Helm 2 クライアントで使用する [az acr helm][az-acr-helm] コマンドは非推奨となります。 コマンドが削除される少なくとも 3 か月前に通知が提供されます。 以前に "Helm 2" チャートをデプロイした場合は、「[Migrating Helm v2 to v3 (Helm v2 から v3 への移行)](https://helm.sh/docs/topics/v2_v3_migration/)」を参照してください。
=======
* Azure Container Registry のリポジトリに、Helm グラフを格納して管理できます
* Helm グラフをレジストリに [OCI 成果物](container-registry-image-formats.md#oci-artifacts)として格納します。 Azure Container Registry では、Helm チャートなどの OCI 成果物に対する GA サポートが提供されます。
* `helm registry login` または `az acr login` コマンドを使用して、レジストリで認証します。
* `helm` コマンドを使用して、レジストリ内の Helm グラフをプッシュ、プル、管理します
* `helm install` を使用して、レジストリから Kubernetes クラスターにグラフをインストールします。

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
>>>>>>> repo_sync_working_branch

## <a name="prerequisites"></a>前提条件

この記事のシナリオでは、次のリソースが必要です。

- Azure サブスクリプションの **Azure コンテナー レジストリ**。 必要に応じて、[Azure portal](container-registry-get-started-portal.md) または [Azure CLI](container-registry-get-started-azure-cli.md) を使用してレジストリを作成します。
<<<<<<< HEAD
- **Helm クライアント バージョン 3.1.0 以降** - 現在のバージョンを確認するには、`helm version` を実行します。 Helm のインストール方法とアップグレード方法について詳しくは、「[Installing Helm (Helm のインストール)][helm-install]」をご覧ください。
- Helm チャートをインストールする **Kubernetes クラスター**。 必要に応じて、[Azure Kubernetes Service クラスター][aks-quickstart]を作成します。 
=======
- **Helm クライアント バージョン 3.7.1 以降** - 現在のバージョンを確認するには、`helm version` を実行します。 Helm のインストール方法とアップグレード方法について詳しくは、「[Installing Helm (Helm のインストール)][helm-install]」をご覧ください。 以前のバージョンの Helm 3 からアップグレードする場合は、[リリース ノート](https://github.com/helm/helm/releases)を確認してください。
- Helm グラフをインストールする **Kubernetes クラスター**。 必要に応じて、[Azure Kubernetes Service クラスター][aks-quickstart]を作成します。 
>>>>>>> repo_sync_working_branch
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

<<<<<<< HEAD
## <a name="create-a-sample-chart"></a>サンプル チャートを作成する
=======
ターゲット レジストリで次の環境変数を設定します。 ACR_NAME はレジストリ リソース名です。 ACR レジストリの URL が myregistry.azurecr.io の場合、ACR_NAME を myregistry に設定します。

```console
ACR_NAME=<container-registry-name>
```

## <a name="create-a-sample-chart"></a>サンプル グラフを作成する
>>>>>>> repo_sync_working_branch

次のコマンドを使用して、テスト チャートを作成します。

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

<<<<<<< HEAD
## <a name="save-chart-to-local-registry-cache"></a>ローカル レジストリ キャッシュにチャートを保存する

ディレクトリを `hello-world` サブディレクトリに変更します。 次に、`helm chart save` を実行してチャートのコピーをローカルに保存し、レジストリの完全修飾名 (すべて小文字) とターゲット リポジトリおよびタグを使用して別名を作成します。 

次の例では、レジストリ名は *mycontainerregistry*、ターゲット リポジトリは *hello-world*、ターゲット チャート タグは *v1* ですが、実際の環境に合わせて値を置き換えてください。
=======
## <a name="save-chart-to-local-archive"></a>グラフをローカル アーカイブに保存する

ディレクトリを `hello-world` サブディレクトリに変更します。 次に、`helm package` を実行して、グラフをローカル アーカイブに保存します。 

次の例では、グラフは `Chart.yaml` 内の名前とバージョンで保存されます。
>>>>>>> repo_sync_working_branch

```console
cd ..
helm package .
```

<<<<<<< HEAD
`helm chart list` を実行して、ローカル レジストリ キャッシュにチャートが保存されたことを確認します。 出力は次のようになります。
=======
出力は次のようになります。
>>>>>>> repo_sync_working_branch

```output
Successfully packaged chart and saved it to: /my/path/hello-world-0.1.0.tgz
```

## <a name="authenticate-with-the-registry"></a>レジストリで認証する

`helm registry login` を実行してレジストリで認証します。 サービス プリンシパルの資格情報、ユーザー ID、リポジトリスコープのトークンなど、シナリオに適した[レジストリ資格情報](container-registry-authentication.md)を渡すことができます。

- レジストリに対する[プルおよびプッシュ権限 (AcrPush ロール) がある Azure Active Directory サービス プリンシパル](container-registry-auth-service-principal.md#create-a-service-principal)を使用して認証します。
  ```bash
  SERVICE_PRINCIPAL_NAME=<acr-helm-sp>
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpull \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```
- [個人の Azure AD ID](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) を使用して認証し、AD トークンを使用して Helm グラフをプッシュおよびプルします。
  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```
- [リポジトリスコープのトークン](container-registry-repository-scoped-permissions.md)を使用して認証します (プレビュー)。
  ```bash
  USER_NAME="helm-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --scope-map _repositories_admin \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```
- その後、`helm registry login` で資格情報を指定します。
  ```bash
  helm registry login $ACR_NAME.azurecr.io \
    --username $USER_NAME \
    --password $PASSWORD
  ```

## <a name="push-chart-to-registry-as-oci-artifact"></a>OCI 成果物としてグラフをレジストリにプッシュする

Helm 3 CLI で `helm push` コマンドを実行して、完全修飾ターゲット リポジトリにグラフ アーカイブをプッシュします。 次の例では、ターゲット リポジトリの名前空間が `helm/hello-world` で、グラフに `0.1.0` というタグが付けられています。

```console
<<<<<<< HEAD
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>レジストリにチャートをプッシュする

Helm 3 CLI で `helm chart push` コマンドを実行して、完全修飾ターゲット リポジトリにチャートをプッシュします。

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
=======
helm push hello-world-0.1.0.tgz oci://$ACR_NAME.azurecr.io/helm
>>>>>>> repo_sync_working_branch
```

プッシュが成功すると、出力は次のようになります。

```output
Pushed: <registry>.azurecr.io/helm/hello-world:0.1.0
digest: sha256:5899db028dcf96aeaabdadfa5899db02589b2899b025899b059db02
```

## <a name="list-charts-in-the-repository"></a>リポジトリのチャート一覧

Azure コンテナー レジストリに格納されているイメージと同様に、[az acr repository][az-acr-repository] コマンドを使用して、チャートをホストしているリポジトリと、チャートのタグとマニフェストを表示できます。 

たとえば、[az acr repository show][az-acr-repository-show] を実行して、前の手順で作成したリポジトリのプロパティを表示します。

```azurecli
az acr repository show \
  --name $ACR_NAME \
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
  "createdTime": "2021-10-05T12:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2021-10-05T12:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

[az acr repository show-manifests][az-acr-repository-show-manifests] コマンドを実行して、リポジトリに格納されているチャートの詳細を表示します。 次に例を示します。

```azurecli
az acr repository show-manifests \
  --name $ACR_NAME \
  --repository helm/hello-world --detail
```

出力 (この例では省略されています) には、`application/vnd.cncf.helm.config.v1+json` の `configMediaType` が表示されます。

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2021-10-05T12:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2021-10-05T12:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "0.1.0"
    ]
```

<<<<<<< HEAD
## <a name="pull-chart-to-local-cache"></a>ローカル キャッシュにチャートをプルする

Helm チャートを Kubernetes にインストールするには、チャートがローカル キャッシュにある必要があります。 この例では、最初に `helm chart remove` を実行して、`mycontainerregistry.azurecr.io/helm/hello-world:v1` という名前の既存のローカル チャートを削除します。
=======
## <a name="install-helm-chart"></a>Helm グラフをインストールする

レジストリにプッシュした Helm グラフをインストールするには、`helm install` を実行します。 グラフのタグは、`--version` パラメーターを使用して渡します。 *myhelmtest* などのリリース名を指定するか、`--generate-name` パラメーターを渡します。 次に例を示します。
>>>>>>> repo_sync_working_branch

```console
helm install myhelmtest oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

<<<<<<< HEAD
`helm chart pull` を実行して、Azure コンテナー レジストリからローカル キャッシュにチャートをダウンロードします。
=======
グラフのインストールが成功した後の出力は次のようになります。
>>>>>>> repo_sync_working_branch

```console
NAME: myhelmtest
LAST DEPLOYED: Tue Oct  4 16:59:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

<<<<<<< HEAD
## <a name="export-helm-chart"></a>Helm チャートをエクスポートする

チャートをさらに操作するには、`helm chart export` を使用してローカル ディレクトリにエクスポートします。 たとえば、プルしたチャートを `install` ディレクトリにエクスポートします。
=======
インストールされていることを確認するには、`helm get manifest` コマンドを実行します。 

```console
helm get manifest myhelmtest
```

このコマンドは、`configmap.yaml` テンプレート ファイル内の YAML データを返します。

クラスター上のグラフのリリースをアンインストールするには、`helm uninstall` を実行します。
>>>>>>> repo_sync_working_branch

```console
helm uninstall myhelmtest
```

<<<<<<< HEAD
リポジトリにエクスポートされたチャートの情報を表示するには、チャートをエクスポートしたディレクトリで `helm show chart` コマンドを実行します。
=======
## <a name="pull-chart-to-local-archive"></a>グラフをローカル アーカイブにプルする

必要に応じて、`helm pull` を使用して、コンテナー レジストリからローカル アーカイブにグラフをプルできます。 グラフのタグは、`--version` パラメーターを使用して渡します。 ローカル アーカイブが現在のパスに存在する場合は、このコマンドによって上書きされます。
>>>>>>> repo_sync_working_branch

```console
helm pull oci://$ACR_NAME.azurecr.io/helm/hello-world --version 0.1.0
```

<<<<<<< HEAD
次の出力例に示されているように、チャートの最新バージョンに関する詳細情報が Helm によって返されます。
=======
## <a name="delete-chart-from-the-registry"></a>レジストリからグラフを削除する
>>>>>>> repo_sync_working_branch

コンテナー レジストリからグラフを削除するには、[az acr repository delete][az-acr-repository-delete] コマンドを使用します。 次のコマンドを実行し、プロンプトが表示されたら操作を確認します。

```azurecli
az acr repository delete --name $ACR_NAME --image helm/hello-world:0.1.0
```

<<<<<<< HEAD
## <a name="install-helm-chart"></a>Helm チャートをインストールする

ローカル キャッシュにプルしてエクスポートした Helm チャートをインストールするには、`helm install` を実行します。 *myhelmtest* などのリリース名を指定するか、`--generate-name` パラメーターを渡します。 次に例を示します。
=======
## <a name="migrate-your-registry-to-store-helm-oci-artifacts"></a>レジストリを移行して Helm OCI 成果物を格納する

以前に Helm 2 と `az acr helm` コマンドを使用して Azure Container Registry をグラフ リポジトリとして設定した場合、Helm 3 クライアントに[アップグレード][helm-install]することをお勧めします。 次に、次の手順に従って、グラフを OCI 成果物としてレジストリに格納します。 

> [!IMPORTANT]
> * Helm 2 スタイル (index.yaml ベース) のグラフ リポジトリから OCI 成果物リポジトリへの移行が完了したら、Helm CLI と `az acr repository` コマンドを使用してグラフを管理します。 この記事の前のセクションをご覧ください。 
> * Helm OCI 成果物リポジトリは、`helm search` や `helm repo list` などの Helm コマンドを使用して検出できません。 グラフを OCI 成果物として格納するために使用される Helm コマンドの詳細については、[Helm のドキュメント](https://helm.sh/docs/topics/registries/)を参照してください。

### <a name="enable-oci-support"></a>OCI のサポートを有効にする

Helm 3 クライアントを使用していることを確認します。
>>>>>>> repo_sync_working_branch

```console
helm version
```

<<<<<<< HEAD
チャートのインストールが成功した後の出力は次のようになります。
=======
Helm 3 クライアントで OCI サポートを有効にします。 現時点では、このサポートは試験段階であり、変更される可能性があります。
>>>>>>> repo_sync_working_branch

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="list-current-charts"></a>現在のグラフを一覧表示する

レジストリに現在格納されているグラフを一覧表示します。ここでは *myregistry* という名前です。

```console
helm search repo myregistry
```

出力には、グラフとグラフのバージョンが表示されます。

<<<<<<< HEAD
クラスター上のチャートのリリースをアンインストールするには、`helm uninstall` を実行します。
=======
```
NAME                            CHART VERSION   APP VERSION     DESCRIPTION                                       
myregistry/ingress-nginx        3.20.1          0.43.0          Ingress controller for Kubernetes...
myregistry/wordpress            9.0.3           5.3.2           Web publishing platform for building...
[...]
```
>>>>>>> repo_sync_working_branch

### <a name="pull-chart-archives-locally"></a>グラフ アーカイブをローカルにプルする

リポジトリ内のグラフごとに、グラフ アーカイブをローカルにプルし、ファイル名をメモします。

```console 
helm pull myregisry/ingress-nginx
ls *.tgz
```

<<<<<<< HEAD
## <a name="delete-chart-from-the-registry"></a>レジストリからチャートを削除する

コンテナー レジストリからチャートを削除するには、[az acr repository delete][az-acr-repository-delete] コマンドを使用します。 次のコマンドを実行し、プロンプトが表示されたら操作を確認します。
=======
`ingress-nginx-3.20.1.tgz` などのローカル グラフ アーカイブが作成されます。

### <a name="push-charts-as-oci-artifacts-to-registry"></a>OCI 成果物としてグラフをレジストリにプッシュする

レジストリにログインします。
>>>>>>> repo_sync_working_branch

```azurecli
az acr login --name $ACR_NAME
```

各グラフ アーカイブをレジストリにプッシュします。 例:

```console
helm push ingress-nginx-3.20.1.tgz oci://$ACR_NAME.azurecr.io/helm
```

グラフをプッシュした後、それがレジストリに格納されているのを確認します。

```azurecli
az acr repository list --name $ACR_NAME
```

すべてのグラフをプッシュした後、必要に応じて、Helm 2 スタイルのグラフ リポジトリをレジストリから削除します。 これにより、レジストリ内のストレージが減少します。

```console
helm repo remove $ACR_NAME
```

## <a name="next-steps"></a>次のステップ

* Helm チャートを作成してデプロイする方法の詳細については、[Helm チャートの開発][develop-helm-charts]に関するページを参照してください。
* [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md) での Helm を使用したアプリケーションのインストールについて詳細を確認します。
* Helm チャートは、コンテナーのビルド プロセスの一部として使用できます。 詳細については、[Azure Container Registry タスクの使用][acr-tasks]に関するページを参照してください。

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
