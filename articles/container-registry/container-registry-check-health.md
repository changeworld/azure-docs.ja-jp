---
title: レジストリの正常性をチェックする
description: Azure Container Registry の使用時の一般的な問題 (ローカル Docker の構成やレジストリへの接続性など) を特定するためのクイック診断コマンドを実行する方法について説明します。
ms.topic: article
ms.date: 07/14/2021
ms.openlocfilehash: b0a95179e01de63185092c965b3290017dc96dfb
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114464305"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Azure Container Registry の正常性のチェック

Azure Container Registry を使用しているときに、問題が発生する場合があります。 たとえば、ローカル環境の Docker に問題があるために、コンテナー イメージをプルできない場合があります。 また、ネットワークの問題により、レジストリに接続できない場合もあります。 

最初の診断手順として、[az acr check-health][az-acr-check-health] コマンドを実行して、環境の正常性に関する情報を取得し、必要に応じてターゲット レジストリにアクセスします。 このコマンドは Azure CLI バージョン 2.0.67 以降で使用できます。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli]に関するページを参照してください。

レジストリの追加のトラブルシューティング ガイダンスについては、以下を参照してください。
* [レジストリ ログインのトラブルシューティング](container-registry-troubleshoot-login.md)
* [レジストリに関するネットワークの問題のトラブルシューティング](container-registry-troubleshoot-access.md)
* [レジストリのパフォーマンスのトラブルシューティング](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>az acr check-health を実行する

以下の例は、`az acr check-health` コマンドを実行するさまざまな方法を示しています。

> [!NOTE]
> このコマンドを Azure Cloud Shell で実行した場合、ローカル環境はチェックされません。 ただし、ターゲット レジストリへのアクセスはチェックできます。

### <a name="check-the-environment-only"></a>環境のみをチェックする

ローカル Docker デーモン、CLI のバージョン、Helm クライアントの構成をチェックするには、パラメーターを追加せずにコマンドを実行します。

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>環境とターゲット レジストリをチェックする

レジストリへのアクセスをチェックし、ローカル環境チェックを実行するには、ターゲット レジストリの名前を渡します。 次に例を示します。

```azurecli
az acr check-health --name myregistry
```

### <a name="check-registry-access-in-a-virtual-network"></a>仮想ネットワークでレジストリ アクセスを確認する

プライベート エンドポイントにルーティングするための DNS 設定を確認するには、仮想ネットワークの名前またはリソース ID を渡します。 仮想ネットワークがレジストリとは異なるサブスクリプションまたはリソース グループにある場合は、リソース ID が必要になります。

```azurecli
az acr check-health --name myregistry --vnet myvnet
```

## <a name="error-reporting"></a>エラー報告

このコマンドは標準出力に情報を記録します。 問題が検出されると、エラー コードと説明が示されます。 各コードと考えられる解決策の詳細については、[エラー リファレンス](container-registry-health-error-reference.md)のページをご覧ください。

既定では、コマンドはエラーを検出するたびに停止します。 エラーが検出された場合でも、すべての正常性チェックの出力を提供するようにコマンドを実行することもできます。 次の例に示すように、`--ignore-errors` パラメーターを追加します。

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry; skip confirmation to pull image
az acr check-health --name myregistry --ignore-errors --yes
```      

サンプル出力:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  

## <a name="next-steps"></a>次のステップ

[az acr check-health][az-acr-check-health] コマンドによって返されるエラー コードの詳細については、「[正常性チェックのエラー リファレンス](container-registry-health-error-reference.md)」をご覧ください。

Azure Container Registry に関するよくあるご質問や他の既知の問題については、[FAQ](container-registry-faq.yml) をご覧ください。





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
