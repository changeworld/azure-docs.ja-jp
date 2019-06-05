---
title: Azure Kubernetes Service (AKS) でエグレス トラフィックを制限する
description: Azure Kubernetes Service (AKS) でエグレス トラフィックを制御するために必要なポートとアドレスについて説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: de0ba13a527569e446a44c275b7323d4487f53b6
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780309"
---
# <a name="preview---limit-egress-traffic-for-cluster-nodes-and-control-access-to-required-ports-and-services-in-azure-kubernetes-service-aks"></a>プレビュー - Azure Kubernetes Service (AKS) でクラスター ノードのエグレス トラフィックを制限し、必要なポートとサービスへのアクセスを制御する

既定で、AKS クラスターは、送信 (エグレス) インターネット アクセスが無制限です。 このレベルのネットワーク アクセスでは、実行しているノードやサービスから必要に応じて外部リソースにアクセスできます。 エグレス トラフィックを制限する場合は、正常なクラスター メンテナンス タスクを維持するために、アクセスできるポートとアドレスの数を制限する必要があります。 次に、外部パブリック リポジトリではなく、Microsoft Container Registry (MCR) または Azure Container Registry (ACR) のベース システム コンテナー イメージのみを使用するようにクラスターを構成します。

この記事では、AKS クラスターでエグレス トラフィックを制限する場合に必要なネットワーク ポートと完全修飾ドメイン名 (FQDN) について説明します。  現在、この機能はプレビュー段階にあります。

> [!IMPORTANT]
> AKS のプレビュー機能は、セルフサービスかつオプトインです。 プレビューは、コミュニティからフィードバックやバグを収集するために提供されます。 ただし、これらは Azure テクニカル サポートではサポートされません。 クラスターを作成するか、または既存のクラスターにこれらの機能を追加した場合、そのクラスターは、この機能がプレビューでなくなり、一般提供 (GA) となるまでサポートされません。
>
> プレビュー機能に関する問題が発生した場合は、バグ タイトルにプレビュー機能の名前を使用して、[AKS GitHub リポジトリで問題をオープンします][aks-github]。

## <a name="before-you-begin"></a>開始する前に

Azure CLI バージョン 2.0.61 以降がインストールされて構成されている必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

エグレス トラフィックを制限できる AKS クラスターを作成するには、まずサブスクリプションの機能フラグを有効にします。 この機能の登録によって、作成する AKS クラスターは、MCR または ACR のベース システム コンテナー イメージを使用するように構成されます。 *AKSLockingDownEgressPreview* 機能フラグを登録するには、次の例に示すように [az feature register][az-feature-register] コマンドを使用します。

```azurecli-interactive
az feature register --name AKSLockingDownEgressPreview --namespace Microsoft.ContainerService
```

状態が *[登録済み]* と表示されるまでに数分かかります。 [az feature list][az-feature-list] コマンドを使用して登録状態を確認できます。

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSLockingDownEgressPreview')].{Name:name,State:properties.state}"
```

準備ができたら、[az provider register][az-provider-register] コマンドを使用して、*Microsoft.ContainerService* リソース プロバイダーの登録を更新します。

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="egress-traffic-overview"></a>エグレス トラフィックの概要

管理と運用上の目的から、AKS クラスター内のノードは特定のポートと完全修飾ドメイン名 (FQDN) にアクセスする必要があります。 このようなアクションには、API サーバーと通信することや、コア Kubernetes クラスター コンポーネントとノード セキュリティの更新プログラムをダウンロードしてからインストールすることがあります。 既定では、エグレス (送信) インターネット トラフィックは AKS クラスター内のノードに制限されていません。 クラスターでは、外部リポジトリからベース システム コンテナー イメージをプルすることがあります。

AKS クラスターのセキュリティを強化するために、エグレス トラフィックを制限する場合があります。 クラスターは、MCR または ACR からベース システム コンテナー イメージをプルするように構成されています。 この方法でエグレス トラフィックをロック ダウンする場合は、AKS ノードが必要な外部サービスと正しく通信できるように、特定のポートと FQDN を定義する必要があります。 このような承認済みのポートと FQDN がない場合、AKS ノードは API サーバーと通信できず、コア コンポーネントをインストールできません。

[Azure Firewall][azure-firewall] またはサードパーティ製ファイアウォール アプライアンスを使用して、エグレス トラフィックをセキュリティで保護し、これらの必要なポートとアドレスを定義することができます。

AKS には、2 組のポートとアドレスがあります。

* 「[AKS クラスターの必要なポートとアドレス](#required-ports-and-addresses-for-aks-clusters)」では、承認されるエグレス トラフィックの最小要件について詳しく説明しています。
* 「[AKS クラスターの省略可能な推奨されるアドレスとポート](#optional-recommended-addresses-and-ports-for-aks-clusters)」は、すべてのシナリオに必要ではなく、Azure Monitor など、他のサービスとの統合は正しく機能しません。 この省略可能なポートと FQDN の一覧を確認し、AKS クラスターに使用されるサービスとコンポーネントをすべて承認します。

> [!NOTE]
> エグレス トラフィックの制限は、機能フラグ登録を有効にした後に作成された新しい AKS クラスターにのみ機能します。 既存のクラスターの場合は、エグレス トラフィックを制限する前に、`az aks upgrade` コマンドを使用して[クラスターのアップグレード操作を実行][aks-upgrade]します。

## <a name="required-ports-and-addresses-for-aks-clusters"></a>AKS クラスターの必要なポートとアドレス

AKS クラスターには、次の送信ポート/ネットワーク規則が必要です。

* TCP ポート *443*
* TCP ポート *9000*

次の FQDN/アプリケーション規則が必要です。

| FQDN                      | Port      | 用途      |
|---------------------------|-----------|----------|
| *.azmk8s.io               | HTTPS: 443 | このアドレスは API サーバー エンドポイントです。 |
| aksrepos.azurecr.io       | HTTPS: 443 | このアドレスは、Azure Container Registry (ACR) 内のイメージにアクセスするために必要です。 |
| *.blob.core.windows.net   | HTTPS: 443 | このアドレスは ACR に保存されているイメージのバックエンド ストアです。 |
| mcr.microsoft.com         | HTTPS: 443 | このアドレスは、Microsoft Container Registry (MCR) のイメージにアクセスするために必要です。 |
| management.azure.com      | HTTPS: 443 | このアドレスは、Kubernetes の GET/PUT 操作に必要です。 |
| login.microsoftonline.com | HTTPS: 443 | このアドレスは Azure Active Directory 認証に必要です。 |

## <a name="optional-recommended-addresses-and-ports-for-aks-clusters"></a>AKS クラスターの省略可能な推奨されるアドレスとポート

次の送信ポート/ネットワーク規則は、AKS クラスターが正常に機能するために必要ではありませんが、推奨されます。

* NTP 時刻同期用の UDP ポート *123*
* DNS 用の UDP ポート *53*

AKS クラスターが正常に機能するためには、以下の FQDN/アプリケーション規則が推奨されます。

| FQDN                                    | Port      | 用途      |
|-----------------------------------------|-----------|----------|
| *.ubuntu.com                            | HTTP: 80   | このアドレスを使用すると、Linux クラスター ノードから必要なセキュリティ パッチと更新プログラムをダウンロードできます。 |
| packages.microsoft.com                  | HTTPS: 443 | このアドレスは、キャッシュされた *apt-get* 操作に使用される Microsoft パッケージ リポジトリです。 |
| dc.services.visualstudio.com            | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| *.opinsights.azure.com                  | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| *.monitoring.azure.com                  | HTTPS: 443 | Azure Monitor を使用した適切なメトリックと監視のために推奨されます。 |
| gov-prod-policy-data.trafficmanager.net | HTTPS: 443 | このアドレスは、Azure Policy (現在 AKS でプレビュー段階) の適切な操作のために使用されます。 |
| apt.dockerproject.org                   | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |
| nvidia.github.io                        | HTTPS: 443 | このアドレスは、GPU ベースのノード上のドライバーの適切なインストールと操作に使用されます。 |

## <a name="next-steps"></a>次の手順

この記事では、クラスターのエグレス トラフィックを制限する場合に許可するポートとアドレスについて学習しました。 また、ポッド自体が通信する方法と、それらのクラスター内の制限を定義することもできます。 詳細については、[AKS のネットワーク ポリシーを使用したポッド間のトラフィックの保護][network-policy]に関する記事を参照してください。

<!-- LINKS - external -->
[aks-github]: https://github.com/azure/aks/issues]

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policy]: use-network-policies.md
[azure-firewall]: ../firewall/overview.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-upgrade]: upgrade-cluster.md
