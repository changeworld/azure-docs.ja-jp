---
title: "Azure Container Service (AKS) クラスター ノードへの SSH 接続"
description: "Azure Container Service (AKS) クラスター ノードとの SSH 接続を作成します"
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/28/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00affc3d1c02c477826261aeac6e092934037e81
ms.sourcegitcommit: 83ea7c4e12fc47b83978a1e9391f8bb808b41f97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="ssh-into-azure-container-service-aks-cluster-nodes"></a>Azure Container Service (AKS) クラスター ノードへの SSH 接続

場合によっては、メンテナンス、ログ収集、またはその他のトラブルシューティング操作のために、Azure Container Service (AKS) ノードにアクセスしなければならない場合があります。 Azure Container Service (AKS) ノードは、インターネットに公開されていません。 このドキュメントで説明されている手順を使用して、AKS ノードとの SSH 接続を作成してください。

## <a name="configure-ssh-access"></a>SSH アクセスの構成

 特定のノードに SSH 接続するために、`hostNetwork` アクセスを持つポッドが作成されます。 ポッド アクセス用に、サービスも作成されます。 この構成には特権が与えられているため、使用後に削除する必要があります。

`aks-ssh.yaml` という名前のファイルを作成し、このマニフェストにコピーします。 ノード名をターゲット AKS ノードの名前に更新します。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: aks-ssh
spec:
  selector:
    app: aks-ssh
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 22
    targetPort: 22
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: aks-ssh
  labels:
    app: aks-ssh
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-ssh
  template:
    metadata:
      labels:
        app: aks-ssh
    spec:
      containers:
      - name: alpine
        image: alpine:latest
        ports:
        - containerPort: 22
        command: ["/bin/sh", "-c", "--"]
        args: ["while true; do sleep 30; done;"]
      hostNetwork: true
      nodeName: aks-nodepool1-42032720-0
```

マニフェストを実行して、ポッドとサービスを作成します。

```azurecli-interactive
$ kubectl apply -f aks-ssh.yaml
```

公開されているサービスの外部 IP アドレスを取得します。 IP アドレスの構成が完了するまでに 1 分ほどかかることがあります。 

```azurecli-interactive
$ kubectl get service

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)        AGE
kubernetes         ClusterIP      10.0.0.1      <none>          443/TCP        1d
aks-ssh            LoadBalancer   10.0.51.173   13.92.154.191   22:31898/TCP   17m
```

SSH 接続を作成します。 

AKS クラスターの既定のユーザー名は、`azureuser` です。 クラスターの作成時にこのアカウントが変更された場合は、適切な管理者ユーザー名を使用してください。 

キーが `~/ssh/id_rsa` にない場合は、`ssh -i` 引数を使用して正しい場所を指定します。

```azurecli-interactive
$ ssh azureuser@13.92.154.191

Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    http://www.ubuntu.com/business/services/cloud

48 packages can be updated.
0 updates are security updates.


*** System restart required ***
Last login: Tue Feb 27 20:10:38 2018 from 167.220.99.8
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@aks-nodepool1-42032720-0:~$
```

## <a name="remove-ssh-access"></a>SSH アクセスの削除

終了したら、SSH アクセスのポッドとサービスを削除します。

```azurecli-interactive
kubectl delete -f aks-ssh.yaml
```