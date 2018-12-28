---
title: Azure Kubernetes Service に関する一般的な問題のトラブルシューティング
description: Azure Kubernetes Service (AKS) を使用するときに発生する一般的な問題をトラブルシューティングおよび解決する方法について説明します
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: c20f2cc03565ce861dfc6317be8459fdafeef0bf
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384107"
---
# <a name="aks-troubleshooting"></a>AKS のトラブルシューティング
AKS クラスターを作成および管理するとき、場合によっては問題が発生することがあります。 この記事では、お問い合わせの多い問題とトラブルシューティングの手順について詳しく説明します。

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>通常、Kubernetes に関する問題のデバッグ情報はどこにありますか。

Kubernetes クラスターのトラブルシューティングへの公式のリンクは[こちら](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)です。
Microsoft のエンジニアによって公開された、ポッド、ノード、クラスターのトラブルシューティングに関するトラブルシューティング ガイドは[こちら](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)です。

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>作成またはアップグレード中にクォータを超過したというエラーが発生します。 どうすればよいですか。 

[こちら](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)からコアを要求する必要があります。

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>AKS におけるノードあたりの最大ポッド数はいくつに設定されていますか。

Azure portal で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 30 に設定されます。
Azure CLI で AKS クラスターをデプロイする場合、ノードあたりの最大ポッド数は既定で 110 に設定されます  (最新バージョンの Azure CLI を使用していることを確認してください)。 この既定の設定を変更するには、az aks create コマンドで –max-nodes-per-pod フラグを使用します。

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>高度なネットワークで AKS クラスターをデプロイしているときに、"insufficientSubnetSize" エラーが発生します。 どうすればよいですか。

AKS 作成時にネットワークに対して選択されたカスタム VNET オプションでは、IPAM に対して Azure CNI が使用されています。 AKS クラスター内では、1 個から 100 個までの範囲で、いくつでもノードを使用できます。 上記の 2) に基づいて、サブネット サイズは、ノード数とノードあたりの最大ポッド数の積よりも大きくなければなりません (サブネット サイズ > クラスター内のノード数 x ノードあたりの最大ポッド数)。

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>自分のポッドが "CrashLoopBackOff" モードでスタックします。 どうすればよいですか。

このモードでポッドがスタックする理由は複数あります。 以下を調べてください 
* `kubectl describe pod <pod-name>` が使用されているポッド自体
* `kubectl log <pod-name>` が使用されているログ

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>既存のクラスターで RBAC を有効にしようとしています。 その方法を教えてください。

残念ながら、現時点では、既存のクラスターで RBAC を有効にすることはできません。 新しいクラスターを明示的に作成する必要があります。 CLI を使用する場合は、RBAC が既定で有効になっていますが、これを有効にするトグル ボタンは、AKS ポータル作成ワークフローで使用できます。

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Azure CLI の既定の設定で、または Azure portal で RBAC を有効にしてクラスターを作成しましたが、Kubernetes ダッシュボードで多数の警告が表示されます。 以前ダッシュボードは警告なしで動作していました。 どうすればよいですか。

ダッシュボードに警告が表示されるのは、ダッシュボードが RBAC で有効になっており、そのダッシュボードへのアクセスが既定で無効になっているためです。 ダッシュボードを、クラスターのすべてのユーザーに既定で公開すると、セキュリティの脅威につながる可能性があるため、一般的にこのアプローチは推奨プラクティスと見なされます。 それでもダッシュボードを有効にする場合は、こちらの[ブログ](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)に従って有効にしてください。

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>ダッシュボードに接続できないようです。 どうすればよいですか。

クラスター外にあるサービスにアクセスする最も簡単な方法は、kubectl プロキシを実行することです。これにより、ご自身の localhost ポート 8001 への要求が Kubernetes API サーバーにプロキシされます。 そこから、apiserver によって、ご自身のサービス http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default にプロキシできます

Kubernetes ダッシュボードが表示されない場合は、kube-proxy ポッドが kube-system 名前空間で実行されているかどうかを確認します。 実行状態でない場合は、ポッドを削除します。これにより再起動されます。

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Kubectl ログを使用してログを取得できませんでした。また、API サーバーに接続できず、"サーバーからのエラー: バックエンド ダイヤル中のエラー: dial tcp..." というエラーが発生します。 どうすればよいですか。

既定の NSG が変更されていないこと、および API サーバーへの接続に対してポート 22 が開いていることを確認します。 kube-system 名前空間で tunnelfront ポッドが実行されているかどうかを確認します。 実行されていない場合は、強制的に削除します。これにより再起動されます。

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>アップグレードまたはスケーリングを行おうとしていますが、"プロパティ "imageReference" の変更は許可されていません" という  エラーが発生します。  この問題を解決するにはどうすればよいですか?

AKS クラスター内のエージェント ノードのタグを変更したことが原因で、このエラーが発生している可能性があります。 MC_* リソース グループのリソースのタグやその他のプロパティを変更または削除すると、予期しない結果につながる可能性があります。 AKS クラスターの MC_* でリソースを変更すると、SLO が中断されます。

### <a name="how-do-i-renew-the-service-principal-secret-on-my-aks-cluster"></a>AKS クラスターでサービス プリンシパルのシークレットを更新するにはどうすればよいですか?

既定では、有効期限が 1 年のサービス プリンシパルと共に AKS クラスターが作成されます。 1 年の期限が近づいたら、資格情報をリセットしてサービス プリンシパルの期限を延長することができます。

次の手順を実行する例を示します:

1. [az aks show](/cli/azure/aks#az-aks-show) コマンドを使用して、クラスターのサービス プリンシパル ID を取得します。
1. [az ad sp credential list](/cli/azure/ad/sp/credential#az-ad-sp-credential-list) を使用して、サービス プリンシパルのクライアント シークレットを一覧表示します
1. [az ad sp credential-reset](/cli/azure/ad/sp/credential#az-ad-sp-credential-reset) コマンドを使用して、サービス プリンシパルの期限を 1 年間延長します。 AKS クラスターが正しく動作するためには、サービス プリンシパルのクライアント シークレットが同じままである必要があります。

```azurecli
# Get the service principal ID of your AKS cluster
sp_id=$(az aks show -g myResourceGroup -n myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)

# Get the existing service principal client secret
key_secret=$(az ad sp credential list --id $sp_id --query [].keyId -o tsv)

# Reset the credentials for your AKS service principal and extend for 1 year
az ad sp credential reset \
    --name $sp_id \
    --password $key_secret \
    --years 1
```
