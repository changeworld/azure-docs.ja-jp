---
title: サプライ チェーン成果物のプッシュとプル
description: Azure のプライベート コンテナー レジストリを使用して、サプライ チェーン成果物成果物をプッシュおよびプルする
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 11/11/2021
ms.author: stevelas
ms.custom: references_regions
ms.openlocfilehash: 4a8d3a4b73993a386d2cd49e2daa784a8bb7aced
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495272"
---
# <a name="push-and-pull-supply-chain-artifacts-using-a-private-container-registry-in-azure-preview"></a>Azure のプライベート コンテナー レジストリを使用して、サプライ チェーン成果物成果物をプッシュおよびプルする (プレビュー)

Azure コンテナー レジストリを使用して、シグネチャ、ソフトウェア部品表 (SBoM)、セキュリティ スキャン結果、またはその他の種類を含む成果物のグラフを保存して管理します。 

![コンテナー イメージ、シグネチャ、署名付きソフトウェア部品表などの成果物のグラフ](./media/container-registry-artifacts/oras-artifact-graph.svg)

この機能を示すため、この記事では [OCI Registry as Storage (ORAS)](https://oras.land) ツールを使用して、成果物のグラフを Azure コンテナー レジストリにプッシュおよびプルする方法について説明します。

ORAS Artifacts のサポートはプレビュー機能であり、[制限](#preview-limitations)が適用されます。 それには、Premium サービス レベルで使用可能な[ゾーン冗長](zone-redundancy.md)が必要です。 レジストリ サービスのレベルと制限については、[Azure Container Registry のサービス レベル](container-registry-skus.md)に関する記事を参照してください。

## <a name="prerequisites"></a>前提条件

* **ORAS CLI** - ORAS CLI により、ORAS Artifacts 対応レジストリへの成果物のプッシュ、検出、プルが可能になります。
* **Azure CLI** - ID の作成、リポジトリの一覧表示と削除を行うには、Azure CLI のローカル インストールが必要です。 バージョン 2.29.1 以降が推奨されます。 バージョンを確認するには、`az --version ` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。
* **Docker (省略可能)** - チュートリアルを完了するために、コンテナー イメージが参照されます。 ローカルにインストールされた Docker を使用して、コンテナー イメージをビルドしてプッシュしたり、既存のコンテナー イメージを参照したりすることができます。 Docker では、[macOS][docker-mac]、[Windows][docker-windows]、または [Linux][docker-linux] システムで Docker を簡単に構成できるパッケージが提供されています。

## <a name="preview-limitations"></a>プレビューの制限事項

ORAS Artifacts のサポートは、可用性ゾーンのサポートがある米国中南部リージョンに制限されます。

* geo レプリケートされたレジストリでは、参照された成果物が他のリージョンにレプリケートされません。 追加のリージョンでは ORAS Artifacts がサポートされているため、参照された成果物がレプリケートされます。

## <a name="oras-installation"></a>ORAS のインストール

ご使用のオペレーティング システムに対応するプレビュー ORAS リリースをダウンロードしてインストールします。 ご使用のオペレーティング システムに対応するファイルを抽出してインストールする方法については、[ORAS のインストール手順][oras-install-docs]を参照し、[ORAS GitHub リポジトリ][oras-preview-install]の Alpha.1 プレビュー ビルドを参照してください

## <a name="configure-a-private-registry"></a>プライベート レジストリを構成する

コマンドをシェルに簡単にコピーまたは貼り付けるための環境変数を構成します。 コマンドは [Azure Cloud Shell](https://shell.azure.com/) で実行できます

```console
ACR_NAME=myregistry
REGISTRY=$ACR_NAME.azurecr.io
REPO=net-monitor
TAG=v1
IMAGE=$REGISTRY/${REPO}:$TAG
```

### <a name="create-a-resource-group"></a>リソース グループを作成する

必要に応じて、[az group create](/cli/azure/group#az_group_create) コマンドを実行して、レジストリ用のリソース グループを作成します。

```azurecli
az group create --name $ACR_NAME --location southcentralus
```
### <a name="create-oras-artifact-enabled-registry"></a>ORAS Artifact 対応レジストリを作成する

ORAS Artifacts のプレビュー サポートにはゾーン冗長が必要であり、これには、米国中南部リージョンの Premium サービス レベルが必要です。 [az acr create](/cli/azure/acr#az_acr_create) コマンドを実行して ORAS Artifacts 対応レジストリを作成します。 レジストリ オプションの詳細については、`az acr create` コマンドのヘルプを参照してください。

```azurecli
az acr create \
  --resource-group $ACR_NAME \
  --name $ACR_NAME \
  --zone-redundancy enabled \
  --sku Premium \
  --output jsonc
```

コマンドの出力で、レジストリの `zoneRedundancy` プロパティを確認します。 有効にすると、レジストリはゾーン冗長で、ORAS Artifact 対応になります。

```JSON
{
  [...]
  "zoneRedundancy": "Enabled",
}
```

### <a name="sign-in-with-azure-cli"></a>Azure CLI を使用してサインインする

コンテナー レジストリに対して成果物のプッシュとプルを行うために、ご自分の ID で Azure CLI に[サインイン](/cli/azure/authenticate-azure-cli)します。

次に、Acure CLI コマンド [az acr login](/cli/azure/acr#az_acr_login) を使用して、レジストリにアクセスします。

```azurecli
az login
az acr login --name $ACR_NAME
```

> [!NOTE]
> `az acr login` では、Docker クライアントを使用して、`docker.config` ファイル内に Azure Active Directory トークンが設定されます。 個々の認証フローを完了するには、Docker クライアントをインストールして実行する必要があります。

## <a name="sign-in-with-oras"></a>ORAS を使用してサインインする

このセクションでは、レジストリにサインインするためのオプションを示します。 ご使用の環境に適した方法を選択してください。

`oras login` を実行してレジストリで認証します。 サービス プリンシパルの資格情報、ユーザー ID、リポジトリスコープのトークン (プレビュー) など、シナリオに適した[レジストリ資格情報](container-registry-authentication.md)を渡すことができます。

- [個人の Azure AD ID](container-registry-authentication.md?tabs=azure-cli#individual-login-with-azure-ad) を使用して認証し、AD トークンを使用します。

  ```bash
  USER_NAME="00000000-0000-0000-0000-000000000000"
  PASSWORD=$(az acr login --name $ACR_NAME --expose-token --output tsv --query accessToken)
  ```

- [リポジトリ スコープのトークン](container-registry-repository-scoped-permissions.md) (プレビュー) で認証し、AD ベースでないトークンを使用します。

  ```bash
  USER_NAME="oras-token"
  PASSWORD=$(az acr token create -n $USER_NAME \
                    -r $ACR_NAME \
                    --repository $REPO content/write \
                    --only-show-errors \
                    --query "credentials.passwords[0].value" -o tsv)
  ```

- レジストリに対する[プルおよびプッシュ権限 (AcrPush ロール) がある Azure Active Directory サービス プリンシパル](container-registry-auth-service-principal.md#create-a-service-principal)を使用して認証します。

  ```bash
  SERVICE_PRINCIPAL_NAME="oras-sp"
  ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)
  PASSWORD=$(az ad sp create-for-rbac --name $SERVICE_PRINCIPAL_NAME \
            --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
             --role acrpush \
            --query "password" --output tsv)
  USER_NAME=$(az ad sp list --display-name $SERVICE_PRINCIPAL_NAME --query "[].appId" --output tsv)
  ```

### <a name="sign-in-with-oras"></a>ORAS を使用してサインインする

`oras login` に資格情報を指定します。

  ```bash
  oras login $REGISTRY \
    --username $USER_NAME \
    --password $PASSWORD
  ```

Stdin からパスワードを読み取るには、`--password-stdin` を使用します。

## <a name="push-a-container-image"></a>コンテナー イメージをプッシュする

この例では、成果物のグラフをコンテナー イメージに関連付けます。 コンテナー イメージをビルドしてプッシュするか、プライベート レジストリ内の既存のイメージを参照します。

```bash
docker build -t $IMAGE https://github.com/wabbit-networks/net-monitor.git#main
docker push $IMAGE
```

## <a name="create-a-sample-signature-to-the-container-image"></a>コンテナー イメージへのサンプル シグネチャを作成する

```bash
echo '{"artifact": "'${IMAGE}'", "signature": "pat hancock"}' > signature.json
```

### <a name="push-a-signature-to-the-registry-as-a-reference-to-the-container-image"></a>コンテナー イメージへの参照としてレジストリにシグネチャをプッシュする

ORAS コマンドは、シグネチャをリポジトリにプッシュし、`subject` パラメーター経由で別の成果物を参照します。 `--artifact-type` は、さまざまなファイルの種類を使用できるようにするファイル拡張子と同様に、成果物を区別するために指定します。 `file:mediaType` を指定することによって、1 つ以上のファイルをプッシュできます

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'signature/example' \
    --subject $IMAGE \
    ./signature.json:application/json
```

oras push の詳細については [ORAS のドキュメント][oras-push-docs]を参照してください。

## <a name="push-a-multi-file-artifact-as-a-reference"></a>参照として複数ファイルの成果物をプッシュする

成果物に関するドキュメントを作成する

```bash
echo 'Readme Content' > readme.md
echo 'Detailed Content' > readme-details.md
```

参照として複数ファイルの成果物をプッシュする

```bash
oras push $REGISTRY/$REPO \
    --artifact-type 'readme/example' \
    --subject $IMAGE \
    ./readme.md:application/markdown \
    ./readme-details.md:application/markdown
```

## <a name="discovering-artifact-references"></a>成果物参照の検出

ORAS Artifacts 仕様では、`subject` 成果物への参照を検出するための [referrers API][oras-artifacts-referrers] が定義されています。 `oras discover` コマンドでは、コンテナー イメージへの参照の一覧を表示できます。

`oras discover` を使用して、レジストリに保存されている成果物のグラフを表示します

```bash
oras discover -o tree $IMAGE
```

出力には、成果物のグラフの先頭が表示されます。ここには、シグネチャとドキュメントがコンテナー イメージの子として表示されます

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
└── readme/example
    └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
```

## <a name="creating-a-deep-graphs-of-artifacts"></a>成果物の詳細なグラフの作成

ORAS Artifacts 仕様では、詳細なグラフが可能であり、署名付きソフトウェア部品表 (SBoM) や他の成果物の種類を使用できます。

### <a name="create-a-sample-sbom"></a>サンプル SBoM を作成する

```bash
echo '{"version": "0.0.0.0", "artifact": "'${IMAGE}'", "contents": "good"}' > sbom.json
```

### <a name="push-a-sample-sbom-to-the-registry"></a>サンプル SBoM をレジストリにプッシュする

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'sbom/example' \
  --subject $IMAGE \
  ./sbom.json:application/json
```

### <a name="sign-the-sbom"></a>SBoM に署名する

参照としてプッシュされる成果物は、通常、サブジェクト成果物の一部と見なされるため、タグがありません。 別の成果物の子である成果物にシグネチャをプッシュするには、ダイジェストを検索するための `--artifact-type` フィルタリングを付けて、`oras discover` を使用します。

```bash
SBOM_DIGEST=$(oras discover -o json \
                --artifact-type sbom/example \
                $IMAGE | jq -r ".references[0].digest")
```

SBoM のシグネチャを作成する

```bash
echo '{"artifact": "'$REGISTRY/${REPO}@$SBOM_DIGEST'", "signature": "pat hancock"}' > sbom-signature.json
```

### <a name="push-the-sbom-signature"></a>SBoM シグネチャをプッシュする

```bash
oras push $REGISTRY/$REPO \
  --artifact-type 'signature/example' \
  --subject $REGISTRY/$REPO@$SBOM_DIGEST \
  ./sbom-signature.json:application/json
```

### <a name="view-the-graph"></a>グラフを表示する

```bash
oras discover -o tree $IMAGE
```

次の出力が生成されます。

```output
myregistry.azurecr.io/net-monitor:v1
├── signature/example
│   └── sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcb...
├── readme/example
│   └── sha256:1a118663d1085e229ff1b2d4d89b5f6d67911f22e55...
└── sbom/example
    └── sha256:4280eef9adb632b42cf200e7cd5a822a456a558e4f3142da6b...
        └── signature/example
            └── sha256:a31ab875d37eee1cca68dbb14b2009979d05594d44a075bdd7...
```

## <a name="pull-a-referenced-artifact"></a>参照された成果物をプルする

参照された種類をプルするには、`oras discover` コマンドによって参照のダイジェストが検出されます

```bash
DOC_DIGEST=$(oras discover -o json \
              --artifact-type 'readme/example' \
              $IMAGE | jq -r ".references[0].digest")
```

### <a name="create-a-clean-directory-for-downloading"></a>ダウンロード用のクリーン ディレクトリを作成する

```bash
mkdir ./download
```

### <a name="pull-the-docs-into-the-download-directory"></a>ダウンロード ディレクトリにドキュメントをプルする
```bash
oras pull -a -o ./download $REGISTRY/$REPO@$DOC_DIGEST
```
### <a name="view-the-docs"></a>ドキュメントを表示する

```bash
ls ./download
```

## <a name="view-the-repository-and-tag-listing"></a>リポジトリとタグの一覧を表示する

ORAS Artifacts により、タグを割り当てなくても、成果物グラフをプッシュ、検出、プル、コピーできます。 これにより、コンテナー イメージ、Helm チャート、およびその他の成果物に関連付けられているシグネチャや SBoM とは対照的に、ユーザーが考える成果物に焦点を当てたタグの一覧表示が可能になります。

### <a name="view-a-list-of-tags"></a>タグの一覧を表示する

```azurecli
az acr repository show-tags \
  -n $ACR_NAME \
  --repository $REPO \
  -o jsonc
```

### <a name="view-a-list-of-manifests"></a>マニフェストの一覧を表示する

リポジトリには、タグ付きとタグなしの両方のマニフェストの一覧を含めることができます

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

コンテナー イメージ マニフェストに、`"tags":` があることに注意してください

```json
{
  "architecture": "amd64",
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "configMediaType": "application/vnd.docker.container.image.v1+json",
  "createdTime": "2021-11-12T00:18:54.5123449Z",
  "digest": "sha256:a0fc570a245b09ed752c42d600ee3bb5b4f77bbd70d8898780b7ab4...",
  "imageSize": 2814446,
  "lastUpdateTime": "2021-11-12T00:18:54.5123449Z",
  "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
  "os": "linux",
  "tags": [
    "v1"
  ]
}
```

シグネチャはタグ付けされませんが、コンテナー イメージへの `oras.artifact.manifest` 参照として追跡されます

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2021-11-12T00:19:10.987156Z",
  "digest": "sha256:555ea91f39e7fb30c06f3b7aa483663f067f2950dcbcc0b0d...",
  "imageSize": 85,
  "lastUpdateTime": "2021-11-12T00:19:10.987156Z",
  "mediaType": "application/vnd.cncf.oras.artifact.manifest.v1+json"
}
```
## <a name="delete-all-artifacts-in-the-graph"></a>グラフのすべての成果物を削除する

ORAS Artifacts 仕様のサポートにより、ルート成果物に関連付けられている成果物のグラフを削除できます。 [az acr repository delete][az-acr-repository-delete] コマンドを使用して、シグネチャ、SBoM と SBoM のシグネチャを削除します。

```bash
az acr repository delete \
  -n $ACR_NAME \
  -t ${REPO}:$TAG -y
```

### <a name="view-the-remaining-manifests"></a>残りのマニフェストを表示する

```azurecli
az acr repository show-manifests \
  -n $ACR_NAME \
  --repository $REPO \
  --detail -o jsonc
```

## <a name="next-steps"></a>次の手順

* [ORAS CLI](https://oras.land) の詳細を確認する
* サプライ チェーン成果物のグラフのプッシュ、検出、プル、コピーの方法について、[ORAS Artifacts][oras-artifacts] の詳細を確認する

<!-- LINKS - external -->
[docker-linux]:         https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]:           https://docs.docker.com/docker-for-mac/
[docker-windows]:       https://docs.docker.com/docker-for-windows/
[oras-install-docs]:    https://oras.land/cli/
[oras-preview-install]: https://github.com/oras-project/oras/releases/tag/v0.2.1-alpha.1
[oras-push-docs]:       https://oras.land/cli/1_pushing/
[oras-artifacts]:       https://github.com/oras-project/artifacts-spec/
<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
