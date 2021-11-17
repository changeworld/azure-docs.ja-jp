---
title: Azure Kubernetes Service (AKS) 上のシークレット ストア CSI ドライバー用 Azure Key Vault プロバイダーのトラブルシューティング
description: シークレット ストア CSI ドライバー用 Azure Key Vault プロバイダーを Azure Kubernetes Service (AKS) と共に使用する際の一般的な問題をトラブルシューティングして解決する方法について説明します。
author: nickomang
ms.service: container-service
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: nickoman
ms.openlocfilehash: c67d23d03b4d21ce1eeb8438d0787c0a2b5e5061
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511303"
---
# <a name="troubleshooting-azure-key-vault-provider-for-secrets-store-csi-driver"></a>シークレット ストア CSI ドライバー用 Azure Key Vault プロバイダーのトラブルシューティング

この記事では、トラブルシューティングに役立つコンポーネントの概要、一般的な問題とその解決策の一覧を示します。

## <a name="logging"></a>ログの記録

Azure Key Vault プロバイダー ログは、プロバイダー ポッドで入手できます。 プロバイダーに関する問題をトラブルシューティングする場合、アプリケーション ポッドと同じノードで実行されているプロバイダー ポッドのログを確認できます。

```bash
# find the secrets-store-provider-azure pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-provider-azure -n kube-system -o wide
kubectl logs -l app=secrets-store-provider-azure -n kube-system --since=1h | grep ^E
```

シークレット ストア CSI ドライバー ログにもアクセスできます。

```bash
# find the secrets-store-csi-driver pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-csi-driver -n kube-system -o wide
kubectl logs -l app=secrets-store-csi-driver -n kube-system --since=1h | grep ^E
```

## <a name="common-issues"></a>一般的な問題

### <a name="failed-to-get-key-vault-token-nmi-response-failed-with-status-code-404"></a>Failed to get key vault token: nmi response failed with status code: 404 

ログまたはイベントで次のエラー メッセージが表示された場合:

```bash
Warning  FailedMount  74s    kubelet            MountVolume.SetUp failed for volume "secrets-store-inline" : kubernetes.io/csi: mounter.SetupAt failed: rpc error: code = Unknown desc = failed to mount secrets store objects for pod default/test, err: rpc error: code = Unknown desc = failed to mount objects, error: failed to get keyvault client: failed to get key vault token: nmi response failed with status code: 404, err: <nil>
```

これは、aad-pod-identity の NMI コンポーネントによって、トークン要求のエラーが返されたことを意味します。 エラーの詳細を取得するには、NMI ポッド ログを確認し、AAD Pod ID の[トラブルシューティング ガイド][aad-troubleshooting]を参照して問題を解決します。

### <a name="keyvaultbaseclientgetsecret-failure-sending-request-statuscode0--original-error-context-canceled"></a>keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 – Original Error: context canceled” 

ログまたはイベントで次のエラー メッセージが表示された場合:

```bash
E1029 17:37:42.461313       1 server.go:54] failed to process mount request, error: keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 -- Original Error: context deadline exceeded
```

これは、以下の理由により、プロバイダー ポッドが AKV インスタンスにアクセスできないことを意味します
- プロバイダーからのエグレス トラフィックをブロックするファイアウォール規則があります。
- エグレス トラフィックをブロックしているクラスター内で構成されているネットワーク ポリシー。
- プロバイダー ポッドが hostNetwork で実行されています。 そのため、このトラフィックをブロックするポリシーがある場合や、ノードにネットワーク ジッターがある場合は、上記のエラーが発生する可能性があります。 トラフィックをブロックするように構成されているポリシーがないか確認し、プロバイダー ポッドを許可リストに登録します。 また、ノードから keyvault と AAD への接続が存在している必要があります。

ホスト ネットワーク上で実行されるポッドからの Azure Key Vault 接続は、次のようにテストできます。
- ポッドを作成します

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: curl
spec:
  hostNetwork: true
  containers:
  - args:
    - tail
    - -f
    - /dev/null
    image: curlimages/curl:7.75.0
    name: curl
  dnsPolicy: ClusterFirst
  restartPolicy: Always
EOF
```

- 上で作成したポッドに Exec で接続します

```bash
kubectl exec -it curl -- sh
```

- AKV で認証します

```bash
curl -X POST 'https://login.microsoftonline.com/<AAD_TENANT_ID>/oauth2/v2.0/token' -d 'grant_type=client_credentials&client_id=<AZURE_CLIENT_ID>&client_secret=<AZURE_CLIENT_SECRET>&scope=https://vault.azure.net/.default'
```

- AKV で既に作成されているシークレットの取得を試みます

```bash
curl -X GET 'https://<KEY_VAULT_NAME>.vault.azure.net/secrets/<SECRET_NAME>?api-version=7.2' -H "Authorization: Bearer <ACCESS_TOKEN_ACQUIRED_ABOVE>"
```

<!-- LINKS EXTERNAL -->
[aad-troubleshooting]: https://azure.github.io/aad-pod-identity/docs/troubleshooting/