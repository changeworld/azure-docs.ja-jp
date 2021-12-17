---
title: Azure Red Hat OpenShift (ARO) クラスター内でカスタム DNS リソースを構成する
description: Azure Red Hat OpenShift (ARO) 内でお使いのすべてのノード上で、カスタム DNS サーバーを追加する方法について説明します。
author: bryanro92
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/02/2021
ms.openlocfilehash: 842e0a4d57254c4ec27bdadf2ff168fe2f4c0424
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442250"
---
# <a name="configure-custom-dns-for-your-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) クラスターのカスタム DNS リソースを構成する

この記事では、カスタム DNS サーバーを使用するように Azure Red Hat OpenShift クラスター (ARO) を構成するうえで必要な情報について詳しく説明します。 基本的な ARO デプロイのクラスターの要件が記載されています。

## <a name="before-you-begin"></a>開始する前に

この記事では、新しいクラスターを作成していること、または最新の更新プログラムが適用されている既存のクラスターがあることを前提としています。 ARO クラスターが必要な場合、パブリック クラスターについては [ARO クイックスタート](./tutorial-create-cluster.md)を、プライベート クラスターについては [プライベート クラスターのチュートリアル](./howto-create-private-cluster-4x.md)を参照してください。 ご自身のクラスターを、カスタム DNS サーバーを使用するように設定するこれらの手順は、プライベート クラスターとパブリック クラスターのどちらでも同じです。

### <a name="confirm-cluster-compatibility-with-custom-dns"></a>カスタム DNS とのクラスターの互換性を確認する

クラスターがこの機能に対応していることを確認するには、`99-master-aro-dns` と `99-worker-aro-dns` の `machineconfigs` があるかどうかを確認します。

```
oc get machineconfig
```

上記のコマンドの結果に次の machineconfigs が含まれている場合、お使いのクラスターはカスタム DNS サポートの対象です。

```
NAME                 GENERATEDBYCONTROLLER                      IGNITIONVERSION   AGE
...
99-master-aro-dns                                               2.2.0             54d
99-worker-aro-dns                                               2.2.0             54d
...
```

## <a name="dns-architecture-overview"></a>DNS アーキテクチャの概要

Azure Red Hat OpenShift クラスター内の各ノードの電源がオンになり、ネットワークに参加すると、DHCP が、IP アドレスや使用する DNS サーバーなどの情報を使って仮想マシンを構成します。

以下のプロセス フローは、構成の取得方法の概要を示しています。

![DNS](media/concepts-networking/custom-dns-pfd.jpg)

仮想ネットワークで既定の DNS サーバーではなく独自の DNS サーバーを使用すると、重要なトレードオフとして、DNS サーバーが提供していた構成が失われます。 仮想マシン名は、ネットワーク上の DNS によって解決されなくなります。

### <a name="update-process-overview"></a>アップグレード プロセスの概要

クラスターのカスタム DNS サーバーの構成手順は、2 つに分けられます。

1. Virtual Network DNS サーバーの構成設定を変更する。
2. クラスター内のノードを再起動して変更を適用する。


## <a name="configure-a-custom-dns-server"></a>カスタム DNS サーバーを構成する

次の手順はコマンド ラインでも実行できますが、このドキュメントでは、ポータル Web インターフェイスを使用する方法について説明します。

### <a name="update-dns-configuration-in-virtual-network"></a>仮想ネットワーク内の DNS 構成を更新する

Azure portal にログインし、更新する必要のある仮想ネットワークに移動します。 仮想ネットワークの設定リストから **[DNS サーバー]** を選択します。

![DNS ゾーンを選択する](media/concepts-networking/vnet-dns-config.png)

DNS 構成画面に移動し、 **[カスタム]** オプション ボタンを選択します。 お使いの DNS サーバーの IP アドレスを入力します。

>[!IMPORTANT]
> カスタム DNS サーバーを指定することを選択すると、DNS 経由で仮想ネットワーク内のノード名を解決できなくなります。 ノードには、IP アドレス経由でのみアクセスできます。

![カスタム DNS サーバーを指定する](media/concepts-networking/vnet-custom-dns.png)

**[保存]** を選択します。

>[!NOTE]
> ポータル インターフェイスに示されているように、変更を適用するには、すべての仮想マシンを再起動する必要があります。

ご自身の更新が正常に完了したことを示す通知が表示されます。

![DNS の変更を確認する](media/concepts-networking/vnet-dns-confirm-saved.png)

### <a name="gracefully-reboot-your-cluster"></a>クラスターを適切に再起動する

これらの手順には、お使いのクラスターに対して有効な kubeconfig が必要です。kubeconfig を取得する方法の詳細については、[こちらのチュートリアル](./tutorial-connect-cluster.md)を参照してください。

次のコード スニペットは、マスター ノードとワーカー ノードに対して noop `machineconfig` を作成しています。 これにより、ワーカー ノードまたはマスター ノードのローリング再起動を開始できます。 Machine Config Operator (MCO) の詳細については、[ソース コード](https://github.com/openshift/machine-config-operator)または [MCOの OpenShift ドキュメント](https://docs.openshift.com/container-platform/4.6/architecture/control-plane.html)を参照してください。
#### <a name="machineconfig-definitions"></a>MachineConfig の定義

ワーカーが再起動します。

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 25-machineconfig-worker-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-noop-worker-restart.txt
```

マスターが再起動します。

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 25-machineconfig-master-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-master-noop-restart.txt
```

#### <a name="reboot-worker-nodes"></a>ワーカー ノードを再起動する

ワーカーの再起動ファイルを作成します。この例では、ファイル `worker-restarts.yml` を呼び出して適用します。

```
[user@bastion ~]$ vim worker-restarts.yml
[user@bastion ~]$ oc apply -f worker-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-worker-reboot created
```

MCO はワークロードを移動し、各ノードを一度に 1 つずつ再起動します。 ワーカーがオンラインに戻ったら、同じ手順に従って、マスター ノードを再起動します。 ワーカーの状態を確認するには、ノードに対してクエリを実行します。すべてのノードが `Ready` 状態であることを検証できます。

>[!NOTE]
> クラスター内のワークロードのサイズによっては、ノードが再起動されるまでに数分かかることがあります。


完全には準備できていないワーカー ノードの例は次のとおりです。

```
NAME                                  STATUS                     ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready                      worker   5h35m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready,SchedulingDisabled   worker   5h34m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready                      worker   5h35m   v1.19.0+a5a0987
```

ノードが再起動されると、NotReady 状態が変わるのがわかります。

```
dns-docs-tm45t-worker-eastus2-ln2kq   NotReady,SchedulingDisabled   worker   5h38m   v1.19.0+a5a0987
```

完全に準備が完了すると次のようになります。

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready    master   5h45m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   5h41m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   5h41m   v1.19.0+a5a0987
```

#### <a name="reboot-master-nodes"></a>マスター ノードを再起動する

次に、マスター ノードに対して同じプロセスを繰り返します。

```
[user@bastion ~]$ vim master-restarts.yml
[user@bastion ~]$ oc apply -f master-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-master-reboot created
```

すべてのノードが `Ready` 状態に戻ったことを確認します。

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
dns-docs-tm45t-master-0               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   6h3m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   6h2m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   6h3m   v1.19.0+a5a0987
```

#### <a name="confirm-changes-on-a-node-optional"></a>ノード上の変更を確認する (省略可能)

ノード上の新しい DNS サーバーを検証するには、`oc debug` ポッドを使用します。

```
[user@bastion ~]$ oc debug node/dns-docs-tm45t-worker-eastus2-ln2kq
Starting pod/dns-docs-tm45t-worker-eastus2-ln2kq-debug ...
To use host binaries, run `chroot /host`
chroot Pod IP: 10.0.2.6
If you don't see a command prompt, try pressing enter.
sh-4.4# chroot /host
sh-4.4# uptime
 18:40:16 up 1 min,  0 users,  load average: 0.82, 0.32, 0.12
sh-4.4# cat /etc/resolv.conf.dnsmasq
# Generated by NetworkManager
search reddog.microsoft.com
nameserver 192.168.0.1
```
## <a name="modifying-the-custom-dns-server"></a>カスタム DNS サーバーの変更

カスタム DNS が既に存在するクラスター上でカスタム DNS を変更する手順は、こちらの[プロセス](#update-process-overview)と同じです。

### <a name="modify-dns"></a>DNS を変更する

[こちら](#update-dns-configuration-in-virtual-network)の手順に従って、仮想ネットワーク上の DNS 構成を更新します。

### <a name="reboot-nodes"></a>ノードを再起動する

`machineconfig` を作成するのではなく、最初に作成した `machineconfig` を削除します。 ワーカー ノードから開始します。

```
oc delete machineconfig 25-machineconfig-worker-reboot
```

出力は次のようになります。
```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-worker-reboot" deleted
```

すべてのワーカー ノードが再起動されるのを待ちます。 これは、上記の[ワーカー ノードの再起動](#reboot-worker-nodes)に似ています。

次に、マスター ノードを再起動します。

```
oc delete machineconfig 25-machineconfig-master-reboot
```

出力は次のようになります。

```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-master-reboot" deleted
```

すべてのマスター ノードが再起動され、準備完了状態に戻るのを待ちます。