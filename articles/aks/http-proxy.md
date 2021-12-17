---
title: HTTP プロキシを使用した Azure Kubernetes Service (AKS) ノードの構成
description: Azure Kubernetes Service (AKS) ノードの HTTP プロキシ構成機能を使用します。
services: container-service
author: nickomang
ms.topic: article
ms.date: 09/09/2021
ms.author: nickoman
ms.openlocfilehash: 81631bfea3cc55b52dc95a81cb17c3420cf9638d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132346113"
---
# <a name="http-proxy-support-in-azure-kubernetes-service-preview"></a>Azure Kubernetes Service での HTTP プロキシのサポート (プレビュー)

マネージドとカスタムのどちらの仮想ネットワークにデプロイされているかにかかわらず、Azure Kubernetes Service (AKS) クラスターには、適切に機能するために必要な特定の送信依存関係があります。 以前は、HTTP プロキシ経由でインターネット アクセスをルーティングする必要がある環境において、これが問題になっていました。 ノードには、インターネット サービスにアクセスするために必要な構成、環境変数、および証明書をブートストラップする方法がありませんでした。

この機能によって AKS クラスターに HTTP プロキシのサポートが追加され、クラスター オペレーターがプロキシに依存する環境で AKS に必要なネットワーク トラフィックを保護するために使用できる、わかりやすいインターフェイスが公開されます。

複雑なソリューションの中には、ネットワーク全体でセキュリティで保護された通信を確立するために、信頼チェーンを作成することが必要なものがあります。 また、この機能により、クラスターのブートストラップの一環として、信頼された証明機関をノードにインストールすることもできます。

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="limitations-and-other-details"></a>制限事項とその他の詳細

次のシナリオはサポートされて **いません**。
- 監視アドオン
- ノード プールごとに異なるプロキシ構成
- クラスター作成後のプロキシ設定の更新
- ユーザーとパスワードの認証
- API サーバー通信用のカスタム CA
- Windows ベースのクラスター
- 仮想マシン可用性セット (VMAS) を使用したノード プール

既定では、*httpProxy*、*httpsProxy*、*trustedCa* に値はありません。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、[無料のアカウント](https://azure.microsoft.com/free)を作成できます。
* [Azure CLI がインストールされていること](/cli/azure/install-azure-cli)。

### <a name="install-the-aks-preview-azure-cli"></a>`aks-preview` Azure CLI をインストールする

また、拡張機能バージョン 0.5.25 以降Azure CLI *aks-preview* が必要です。 *aks-preview* Azure CLI 拡張機能は、[az extension add][az-extension-add] コマンドを使用してインストールします。 または、[az extension update][az-extension-update] コマンドを使用すると、使用可能な更新プログラムをインストールできます。

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-httpproxyconfigpreview-preview-feature"></a>`HTTPProxyConfigPreview` プレビュー機能を登録する

この機能を使用するには、サブスクリプションで `HTTPProxyConfigPreview` 機能フラグも有効にする必要があります。

`HTTPProxyConfigPreview` 機能フラグは、次の例のとおり、[az feature register][az-feature-register] コマンドを使用して登録します。

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "HTTPProxyConfigPreview"
```

状態が *[登録済み]* と表示されるまでに数分かかります。 登録の状態は、[az feature list][az-feature-list] コマンドで確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/HTTPProxyConfigPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configuring-an-http-proxy-using-azure-cli"></a>Azure CLI を使用した HTTP プロキシの構成 

HTTP プロキシでの AKS の使用は、クラスターの作成時に [az aks create][az-aks-create] コマンドを使用して行われ、JSON ファイルとして構成が渡されます。

構成ファイルのスキーマは次のようになります。

```json
{
  "httpProxy": "string",
  "httpsProxy": "string",
  "noProxy": [
    "string"
  ],
  "trustedCa": "string"
}
```

`httpProxy`: クラスターの外部で HTTP 接続を作成するために使用するプロキシ URL。 URL スキームは `http` である必要があります。
`httpsProxy`: クラスターの外部で HTTPS 接続を作成するために使用するプロキシ URL。 これを指定しない場合、`httpProxy` が HTTP と HTTPS の両方の接続に使用されます。
`noProxy`: プロキシを除外する宛先ドメイン名、ドメイン、IP アドレス、または他のネットワーク CIDR の一覧。
`trustedCa`: `base64 encoded` 代替 CA 証明書の内容を含む文字列。 現在、`PEM` 形式のみサポートされています。 もう 1 つの注意点は、k8s システムの一部である Go ベースのコンポーネントとの互換性のために、証明書によって、非推奨の共通名証明書ではなく、`Subject Alternative Names(SANs)` がサポートされている必要があることです。

入力例: CA 証明書は、PEM 形式の証明書コンテンツの base64 でエンコードされた文字列である必要があります。

```json
{
  "httpProxy": "http://myproxy.server.com:8080/", 
  "httpsProxy": "https://myproxy.server.com:8080/", 
  "noProxy": [
    "localhost",
    "127.0.0.1"
  ],
  "trustedCA": "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUgvVENDQmVXZ0F3SUJB...b3Rpbk15RGszaWFyCkYxMFlscWNPbWVYMXVGbUtiZGkvWG9yR2xrQ29NRjNURHg4cm1wOURCaUIvCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0="
}
```

ファイルを作成し、*httpProxy*、*httpsProxy*、*noProxy* の値を指定します。 ご自身の環境で必要な場合は、*trustedCa* の値も指定します。 次に、`http-proxy-config` フラグを使ってファイル名を渡して、クラスターをデプロイします。

```azurecli
az aks create -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config.json
```

クラスターは、ノードに構成されている HTTP プロキシを使用して初期化されます。

## <a name="configuring-an-http-proxy-using-azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) テンプレートを使用した HTTP プロキシの構成

ARM テンプレートを使用して構成された HTTP プロキシを使用して AKS クラスターをデプロイするのは簡単です。 CLI でのデプロイに使用するのと同じスキーマが、`Microsoft.ContainerService/managedClusters` 定義のプロパティに存在します。

```json
"properties": {
    ...,
    "httpProxyConfig": {
        "httpProxy": "string",
        "httpsProxy": "string",
        "noProxy": [
            "string"
        ],
        "trustedCa": "string"
    }
}
```

テンプレート内で、*httpProxy*、*httpsProxy*、*noProxy* の値を指定します。 必要に応じて、*trustedCa* の値も指定します。 テンプレートをデプロイします。クラスターは、ノードで構成されている HTTP プロキシを使用して初期化されます。

## <a name="handling-ca-rollover"></a>CA のロールオーバーの処理

*httpProxy*、*httpsProxy*、*noProxy* の値は、クラスター作成後に変更することはできません。 ただし、CA 証明書のローリングをサポートするために、[az aks update][az-aks-update] コマンドを使って、*trustedCa* の値を変更してクラスターに適用できます。

たとえば、*aks-proxy-config-2.json* という新しい CA 証明書の base64 エンコードされた文字列を使用して新しいファイルが作成されたと仮定すると、次の操作によってクラスターが更新されます。

```azurecli
az aks update -n $clusterName -g $resourceGroup --http-proxy-config aks-proxy-config-2.json
```

## <a name="next-steps"></a>次の手順
- AKS クラスターのネットワーク要件の詳細については、「[AKS でクラスター ノードに対するエグレス トラフィックを制御する][aks-egress]」を参照してください。


<!-- LINKS - internal -->
[aks-egress]: ./limit-egress-traffic.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az-extension-update
