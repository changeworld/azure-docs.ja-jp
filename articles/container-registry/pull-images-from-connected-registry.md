---
title: コネクテッド レジストリからイメージをプルする
description: Azure Container Registry の CLI コマンドを使用してクライアント トークンを構成し、IoT Edge デバイス上のコネクテッド レジストリからイメージをプルします。
ms.topic: quickstart
ms.date: 10/21/2021
ms.author: memladen
author: toddysm
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5409afea3ab1ab1ebedaed87adbafd2b15fe9418
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017378"
---
# <a name="pull-images-from-a-connected-registry-on-iot-edge-device"></a>IoT Edge デバイス上のコネクテッド レジストリからイメージをプルする

[コネクテッド レジストリ](intro-connected-registry.md)からイメージをプルするには、[クライアント トークン](overview-connected-registry-access.md#client-tokens)を構成し、その資格情報を渡すことでレジストリの内容にアクセスします。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]
* Azure 内のコネクテッド レジストリ リソース。 デプロイ手順については、[Azure CLI を使用したコネクテッド レジストリの作成に関するクイックスタート][quickstart-connected-registry-cli]を参照してください。
* IoT Edge デバイスにデプロイされたコネクテッド レジストリ インスタンス。 デプロイ手順については、[IoT Edge デバイスへのコネクテッド レジストリのデプロイに関するクイックスタート](quickstart-deploy-connected-registry-iot-edge-cli.md)または[入れ子になった IoT Edge デバイスへのコネクテッド レジストリのデプロイに関するチュートリアル](tutorial-deploy-connected-registry-nested-iot-edge-cli.md)を参照してください。 この記事のコマンドでは、コネクテッド レジストリの名前が *$CONNECTED_REGISTRY_RW* という環境変数に格納されています。

## <a name="create-a-scope-map"></a>スコープ マップを作成する

`hello-world` リポジトリへの読み取りアクセスのためのスコープ マップを作成するには、[az acr scope-map create][az-acr-scope-map-create] コマンドを使用します。

```azurecli
# Use the REGISTRY_NAME variable in the following Azure CLI commands to identify the registry
REGISTRY_NAME=<container-registry-name>

az acr scope-map create \
  --name hello-world-scopemap \
  --registry $REGISTRY_NAME \
  --repository hello-world content/read \
  --description "Scope map for the connected registry."
```

## <a name="create-a-client-token"></a>クライアント トークンを作成する

[az acr token create][az-acr-token-create] コマンドを使用してクライアント トークンを作成し、新しく作成したスコープ マップにそれを関連付けます。

```azurecli
az acr token create \
  --name myconnectedregistry-client-token \
  --registry $REGISTRY_NAME \
  --scope-map hello-world-scopemap
```

このコマンドからは、新しく生成されたトークンについての詳細が返されます。その中にはパスワードも含まれます。

  > [!IMPORTANT]
  > 生成されたパスワードは必ず保存しておいてください。 これらはワンタイム パスワードであり、取得することはできません。 新しいパスワードは、[az acr token credential generate][az-acr-token-credential-generate] コマンドを使用して生成できます。

## <a name="update-the-connected-registry-with-the-client-token"></a>クライアント トークンを使用してコネクテッド レジストリを更新する

新しく作成したクライアント トークンでコネクテッド レジストリを更新するには、[az acr connected-registry update][az-acr-connected-registry-update] コマンドを使用します。 

```azurecli
az acr connected-registry update \
  --name $CONNECTED_REGISTRY_RW \
  --registry $REGISTRY_NAME \
  --add-client-token myconnectedregistry-client-token
```

## <a name="pull-an-image-from-the-connected-registry"></a>コネクテッド レジストリからイメージをプルする

IoT Edge デバイスにアクセスできるマシンから次の例のコマンドを使用し、クライアント トークンの資格情報を使用してコネクテッド レジストリにサインインします。 ログイン資格情報の管理のベスト プラクティスについては、[docker login](https://docs.docker.com/engine/reference/commandline/login/) コマンドのリファレンスを参照してください。

> [!CAUTION]
> セキュリティで保護されていないレジストリとしてコネクテッド レジストリをセットアップした場合は、Docker デーモンの構成にあるセキュリティで保護されていないレジストリのリストに、IoT Edge デバイス上のコネクテッド レジストリの IP アドレス (または FQDN) とポートを追加して更新してください。 この構成の用途はテスト目的に限定してください。 詳細については、「[セキュリティで保護されていないレジストリをテストする](https://docs.docker.com/registry/insecure/)」を参照してください。

```
docker login --username myconnectedregistry-client-token \
  --password <token_password> <IP_address_or_FQDN_of_connected_registry>:<port>
```

IoT Edge のシナリオでは、デバイス上のコネクテッド レジストリにアクセスするためのポートを必ず含めてください。 例:

```
docker login --username myconnectedregistry-client-token \
  --password xxxxxxxxxxx 192.0.2.13:8000
```

そのうえで、次のコマンドを使用して `hello-world` イメージをプルします。

```
docker pull <IP_address_or_FQDN_of_connected_registry>:<port>/hello-world
```

## <a name="next-steps"></a>次のステップ

* [リポジトリスコープのトークン](container-registry-repository-scoped-permissions.md)について詳細を確認します。
* [コネクテッド レジストリへのアクセス](overview-connected-registry-access.md)について詳細を確認します。

<!-- LINKS - internal -->
[az-acr-scope-map-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-create]: /cli/azure/acr/token/#az_acr_token_create
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential#az_acr_token_credential_generate
[az-acr-connected-registry-update]: /cli/azure/acr/connect-registry#az_acr_connected_registry_update] 
[container-registry-intro]: container-registry-intro.md
[quickstart-connected-registry-cli]: quickstart-connected-registry-cli.md
