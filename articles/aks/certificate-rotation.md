---
title: Azure Kubernetes Service (AKS) での証明書のローテーション
description: Azure Kubernetes Service (AKS) クラスターで証明書をローテーションする方法について説明します。
services: container-service
ms.topic: article
ms.date: 11/03/2021
ms.openlocfilehash: 7651af1bc1b3229fa206dbb507a918d611b2eafc
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131575771"
---
# <a name="rotate-certificates-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) での証明書のローテーション

Azure Kubernetes Service (AKS) では、そのコンポーネントの多くで認証に証明書が使用されています。 セキュリティやポリシー上の理由から、定期的にこれらの証明書のローテーションが必要になる場合があります。 たとえば、90 日おきにすべての証明書をローテーションするポリシーがある場合などです。

この記事では、AKS クラスターで証明書をローテーションする方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure CLI バージョン 2.0.77 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="aks-certificates-certificate-authorities-and-service-accounts"></a>AKS 証明書、証明機関、サービス アカウント

AKS では、次の証明書、証明機関、およびサービス アカウントが生成されて使用されます。

* AKS API サーバーでは、クラスター CA と呼ばれる証明機関 (CA) が作成されます。
* API サーバーには、API サーバーから kubelets への一方向の通信に使用する証明書に署名するクラスター CA があります。
* また、各 kubelet では、kubelet から API サーバーへの通信のために、クラスター CA によって署名される証明書署名要求 (CSR) も作成されます。
* API アグリゲーターでは、他の API との通信に証明書を発行するためにクラスター CA が使用されます。 API アグリゲーターでは、これらの証明書を発行するための独自の CA を持つこともできますが、現在はクラスター CA が使用されています。
* 各ノードでは、クラスター CA によって署名されるサービスア カウント (SA) トークンが使用されます。
* `kubectl` クライアントには、AKS クラスターと通信するための証明書があります。

> [!NOTE]
> 2019 年 5 月より前に作成された AKS クラスターには、2 年後に期限切れになる証明書があります。 2019 年 5 月以降に作成されたすべてのクラスター、またはその証明書がローテーションされているすべてのクラスターには、30 年後に期限切れになるクラスター CA 証明書があります。 署名にクラスター CA を使用する他のすべての AKS 証明書は、2 年後に有効期限が切れ、AKS バージョンのアップグレード中に自動的にローテーションされます。 クラスターがいつ作成されたかを確認するには、`kubectl get nodes` を使用して、ノード プールの *Age* を確認します。
> 
> また、クラスターの証明書の有効期限を確認することもできます。 たとえば、次の bash コマンドを使用すると、リソース グループ *rg* 内の *myAKSCluster* クラスターのクライアント証明書の詳細が表示されます
> ```console
> kubectl config view --raw -o jsonpath="{.users[?(@.name == 'clusterUser_rg_myAKSCluster')].user.client-certificate-data}" | base64 -d | openssl x509 -text | grep -A2 Validity
> ```

* apiserver 証明書の有効期限を確認する
```console
curl https://{apiserver-fqdn} -k -v 2>&1 |grep expire
```

* VMAS エージェント ノードで証明書の有効期限を確認する
```console
az vm run-command invoke -g MC_rg_myAKSCluster_region -n vm-name --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

* VMSS エージェント ノードで証明書の有効期限を確認する
```console
az vmss run-command invoke -g MC_rg_myAKSCluster_region -n vmss-name --instance-id 0 --command-id RunShellScript --query 'value[0].message' -otsv --scripts "openssl x509 -in /etc/kubernetes/certs/apiserver.crt -noout -enddate"
```

## <a name="certificate-auto-rotation"></a>証明書の自動ローテーション

Azure Kubernetes Service では、非 CA 証明書の有効期限が切れてダウンタイムが発生する前に、コントロール プレーンとエージェント ノードの両方でそれらを自動的にローテーションします。

AKS で非 CA 証明書を自動的にローテーションするには、クラスターに [TLS ブートストラップ](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet-tls-bootstrapping/)が必要です。 TLS ブートストラップは現在、次のリージョンでご利用いただけます。

* eastus2euap
* centraluseuap
* westcentralus
* uksouth
* eastus
* australiacentral
* australiaest

> [!IMPORTANT]
>リージョンが構成されたら、新しいクラスターを作成するか、"az aks upgrade -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME" を使用して既存のクラスターにアップグレードし、そのクラスターに証明書の自動ローテーションを設定します。 

### <a name="limititation"></a>制限事項

証明書の自動ローテーションは、非 RBAC クラスターでは有効にできません。


## <a name="rotate-your-cluster-certificates"></a>クラスター証明書をローテーションする

> [!WARNING]
> `az aks rotate-certs` を使用して証明書をローテーションすると、すべてのノードが再作成され、AKS クラスターに最大 30 分間のダウンタイムが生じる可能性があります。

[az aks get-credentials][az-aks-get-credentials] を使用して、AKS クラスターにサインインします。 また、このコマンドにより、ご使用のローカル コンピューターに `kubectl` クライアント証明書がダウンロードされて構成されます。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

`az aks rotate-certs` を使用して、ご使用のクラスター上のすべての証明書、CA、および SA をローテーションします。

```azurecli
az aks rotate-certs -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME
```

> [!IMPORTANT]
> `az aks rotate-certs` が完了するまでに最大で 30 分かかる場合があります。 コマンドが完了する前に失敗した場合は、`az aks show` を使用して、クラスターの状態が *証明書のローテーション中* になっていることを確認します。 クラスターがエラー状態になっている場合は、`az aks rotate-certs` を再実行して、証明書をもう一度ローテーションします。

`kubectl` コマンドを実行して、古い証明書が無効になっていることを確認します。 `kubectl` によって使用される証明書を更新していないため、エラーが表示されます。  次に例を示します。

```console
$ kubectl get no
Unable to connect to the server: x509: certificate signed by unknown authority (possibly because of "crypto/rsa: verification error" while trying to verify candidate authority certificate "ca")
```

`az aks get-credentials` を実行して、`kubectl` によって使用される証明書を更新します。

```azurecli
az aks get-credentials -g $RESOURCE_GROUP_NAME -n $CLUSTER_NAME --overwrite-existing
```

`kubectl` コマンドを実行して証明書が更新されていることを確認します。今度は成功します。 次に例を示します。

```console
kubectl get no
```

> [!NOTE]
> AKS 上で実行されるサービスがある場合は、それらのサービスに関連する証明書の更新も必要になることがあります。

## <a name="next-steps"></a>次のステップ

この記事では、クラスターの証明書、CA、および SA を自動的にローテーションする方法について説明しました。 AKS のセキュリティのベスト プラクティスについては、「[Azure Kubernetes Service (AKS) でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][aks-best-practices-security-upgrades]」を参照してください。


[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[aks-best-practices-security-upgrades]: operator-best-practices-cluster-security.md
