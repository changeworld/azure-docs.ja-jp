---
title: Azure Kubernetes Service (AKS) クラスター ノードへの SSH 接続
description: トラブルシューティングやメンテナンスのタスクのために Azure Kubernetes Service (AKS) クラスター ノードとの SSH 接続を作成する方法について説明します。
services: container-service
ms.topic: article
ms.date: 05/17/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 87e548cd39c7c064b11131c28790d8335bd942fb
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418412"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>メンテナンスまたはトラブルシューティングのために SSH を使用して Azure Kubernetes Service (AKS) クラスター ノードに接続する

Azure Kubernetes Service (AKS) クラスターのライフサイクル全体を通して、AKS ノードへのアクセスが必要になる場合があります。 メンテナンス、ログ収集、その他のトラブルシューティング操作のための接続です。 Windows Server ノードを含め、AKS ノードには SSH を使用してアクセスできます。 [リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続する][aks-windows-rdp]こともできます。 セキュリティのため、AKS ノードはインターネットに公開されません。 AKS ノードに SSH 接続するには、`kubectl debug` またはプライベート IP アドレスを使用します。

この記事では、AKS ノードとの SSH 接続を作成する方法を示します。

## <a name="before-you-begin"></a>開始する前に

この記事は、AKS クラスターがすでに存在していることを前提としています。 AKS クラスターが必要な場合は、[Azure CLI を使用した場合][aks-quickstart-cli]または [Azure portal を使用した場合][aks-quickstart-portal]の AKS のクイックスタートを参照してください。

この記事では、SSH キーを持っていることも前提とします。 [macOS または Linux][ssh-nix]、あるいは [Windows][ssh-windows] を使用して、SSH キーを作成できます。 PuTTY を使用してキーの組を作成する場合、そのキーの組は、既定の PuTTy 秘密キー形式 (.ppk ファイル) ではなく、OpenSSH 形式で保存します。

また、Azure CLI バージョン 2.0.64 以降がインストールされ、構成されている必要もあります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][install-azure-cli]に関するページを参照してください。

## <a name="create-the-ssh-connection-to-a-linux-node"></a>Linux ノードへの SSH 接続の作成

AKS ノードへの SSH 接続を作成するには、`kubectl debug` を使用して、ノード上で特権コンテナーを実行します。 ノードの一覧を表示するには、`kubectl get nodes` を使用します。

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

ノード上でコンテナー イメージを実行して接続するには、`kubectl debug` を使用します。

```azurecli-interactive
kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

このコマンドは、ノード上で特権コンテナーを起動し、SSH 経由で接続します。

```output
$ kubectl debug node/aks-nodepool1-12345678-vmss000000 -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
Creating debugging pod node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx with container debugger on node aks-nodepool1-12345678-vmss000000.
If you don't see a command prompt, try pressing enter.
root@aks-nodepool1-12345678-vmss000000:/#
```

この特権コンテナーにより、ノードへのアクセスが提供されます。

## <a name="create-the-ssh-connection-to-a-windows-node"></a>Windows ノードへの SSH 接続の作成

現時点では、`kubectl debug` を使用して、SSH で Windows Server ノードに直接接続することはできません。 代わりに、まずクラスター内の別のノードに接続し、そのノードから SSH を使用して Windows Server ノードに接続する必要があります。 または、SSH を使用する代わりに、[リモート デスクトップ プロトコル (RDP) 接続を使用して、Windows Server ノードに接続する][aks-windows-rdp]ことができます。

クラスター内の別のノードに接続するには、`kubectl debug` を使用します。 詳細については、「[Linux ノードへの SSH 接続の作成][ssh-linux-kubectl-debug]」のセクションを参照してください。

別のノードから Windows Server ノードへの SSH 接続を作成するには、AKS クラスターの作成時に提供された SSH キーと、Windows Server ノードの内部 IP アドレスを使用します。

新しいターミナル ウィンドウを開き、`kubectl get pods` を使用して、`kubectl debug` によって起動されたポッドの名前を取得します。

```output
$ kubectl get pods

NAME                                                    READY   STATUS    RESTARTS   AGE
node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx   1/1     Running   0          21s
```

上の例では、*node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx* が、`kubectl debug` によって起動されたポッドの名前です。

`kubectl port-forward` を使用すると、デプロイされているポッドへの接続を開くことができます。

```
$ kubectl port-forward node-debugger-aks-nodepool1-12345678-vmss000000-bkmmx 2022:22
Forwarding from 127.0.0.1:2022 -> 22
Forwarding from [::1]:2022 -> 22
```

上記の例では、お使いの開発用コンピューターのポート 2022 から、デプロイされているポッドのポート 22 にネットワーク トラフィックの転送を開始します。 `kubectl port-forward` を使用して接続を開き、ネットワーク トラフィックを転送する場合、接続は `kubectl port-forward` コマンドを停止するまで開いたままです。

新しいターミナルを開き、次のように `kubectl get nodes` を使用して、Windows Server ノードの内部 IP アドレスを表示します。

```output
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                         KERNEL-VERSION     CONTAINER-RUNTIME
aks-nodepool1-12345678-vmss000000   Ready    agent   13m     v1.19.9   10.240.0.4    <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aks-nodepool1-12345678-vmss000001   Ready    agent   13m     v1.19.9   10.240.0.35   <none>        Ubuntu 18.04.5 LTS               5.4.0-1046-azure   containerd://1.4.4+azure
aksnpwin000000                      Ready    agent   87s     v1.19.9   10.240.0.67   <none>        Windows Server 2019 Datacenter   10.0.17763.1935    docker://19.3.1
```

上の例では、*10.240.0.67* が、Windows Server ノードの内部 IP アドレスです。

内部 IP アドレスを使用して、Windows Server ノードへの SSH 接続を作成します。 AKS ノードの既定のユーザー名は、*azureuser* です。 接続を続行するプロンプトを受け入れます。 次に、Windows Server ノードの bash プロンプトが提供されます。

```output
$ ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' azureuser@10.240.0.67

The authenticity of host '10.240.0.67 (10.240.0.67)' can't be established.
ECDSA key fingerprint is SHA256:1234567890abcdefghijklmnopqrstuvwxyzABCDEFG.
Are you sure you want to continue connecting (yes/no)? yes

[...]

Microsoft Windows [Version 10.0.17763.1935]
(c) 2018 Microsoft Corporation. All rights reserved.

azureuser@aksnpwin000000 C:\Users\azureuser>
```

上の例では、お使いの開発用コンピューターのポート 2022 を Windows サーバー ノードのポート 22 に接続します。

> [!NOTE]
> パスワード認証を使用する場合は、`-o PreferredAuthentications=password` を使用します。 例:
>
> ```console
>  ssh -o 'ProxyCommand ssh -p 2022 -W %h:%p azureuser@127.0.0.1' -o PreferredAuthentications=password azureuser@10.240.0.67
> ```

## <a name="remove-ssh-access"></a>SSH アクセスの削除

終了したら、SSH セッションを `exit` し、ポートからの転送をすべて停止し、対話型のコンテナー セッションを `exit` します。 対話型のコンテナー セッションを終了すると、AKS クラスターからの SSH アクセスに使用されたポッドが削除されます。

## <a name="next-steps"></a>次のステップ

トラブルシューティングのデータがさらに必要な場合は、[kubelet ログを表示][view-kubelet-logs]するか、[Kubernetes マスター ノードのログを表示][view-master-logs]することができます。


<!-- INTERNAL LINKS -->
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: monitor-aks-reference.md#resource-logs
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[ssh-linux-kubectl-debug]: #create-the-ssh-connection-to-a-linux-node