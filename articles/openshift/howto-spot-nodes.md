---
title: Azure Red Hat OpenShift (ARO) クラスターでの Azure スポット Virtual Machines の使用
description: Azure Red Hat OpenShift (ARO) で Azure スポット Virtual Machines を使用する方法について説明します
author: nilsanderselde
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: スポット、ノード、aro、デプロイ、openshift、red hat
ms.topic: how-to
ms.date: 10/21/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 941db40b96d9e7a00c32b42817a3f2a93e17643a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084428"
---
# <a name="use-azure-spot-virtual-machines-in-an-azure-red-hat-openshift-aro-cluster"></a>Azure Red Hat OpenShift (ARO) クラスターでの Azure スポット Virtual Machines の使用

この記事では、azure Red Hat OpenShift クラスター (ARO) を Azure スポット Virtual Machines を使用するように構成するために必要な詳細について説明します。

Azure Spot Virtual Machines を使用すると、大幅にコストを削減して未使用の容量を利用できます。 Azure で容量の回復が必要になると、Azure インフラストラクチャによって Azure Spot Virtual Machines が削除されます。 スポットインスタンスの詳細については、「 [スポット Virtual Machines](../virtual-machines/spot-vms.md)」を参照してください。

## <a name="before-you-begin"></a>開始する前に

作業を開始する前に、Azure Red Hat Openshift クラスターがデプロイされていることを確認します。 ARO クラスターが必要な場合、パブリック クラスターについては [ARO クイックスタート](tutorial-create-cluster.md)を、プライベート クラスターについては [プライベート クラスターのチュートリアル](howto-create-private-cluster-4x.md)を参照してください。 スポット Vm を使用するようにクラスターを構成する手順は、プライベートクラスターとパブリッククラスターの両方で同じです。

## <a name="add-spot-vms"></a>スポット Vm の追加

Azure でのマシン管理 Red Hat Openshift は、MachineSet を使用して実現されます。 MachineSet リソースは、マシンのグループです。 MachineSets は、ポッドへの ReplicaSets のようにマシンに対して設定されます。 より多くのマシンが必要な場合、またはコンピューターをスケールダウンする必要がある場合は、コンピューティングのニーズに合わせてマシンセットの [ *レプリカ* ] フィールドを変更します。 詳細については、OpenShift [Machineset のドキュメント](https://docs.openshift.com/container-platform/4.8/machine_management/creating_machinesets/creating-machineset-azure.html)を参照してください。

スポット Vm の使用は、 `spotVMOptions` MachineSet のテンプレート仕様内にフィールドを追加することによって指定します。
この MachineSet を作成するには、次の操作を行います。

1. クラスターで実行されている MachineSet のコピーを取得します。
2. 変更された MachineSet 構成を作成します。
3. この MachineSet をクラスターにデプロイする

まず、 [CLI を使用して OpenShift クラスターに接続](tutorial-connect-cluster.md)します。

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

次に、クラスター上の MachineSets を一覧表示します。 既定のクラスターには、3つの MachineSets が展開されます。 
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

このコマンドからの出力例を次に示します。 
```
NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus2   1         1         1       1           2d22h
aro-cluster-5t2dj-worker-eastus3   1         1         1       1           2d22h
```

次に、デプロイされている MachineSet について説明します。 \<machineset\>上に示したいずれかの MachineSets で置き換え、これをファイルに出力します。

```azurecli-interactive
oc get machineset <machineset> -n openshift-machine-api -o yaml > spotmachineset.yaml
```

MachineSet の次のパラメーターを変更する必要があります。
- `metadata.name`
- `spec.selector.matchLabels.machine.openshift.io/cluster-api-machineset`
- `spec.template.metadata.labels.machine.openshift.io/cluster-api-machineset`
- `spec.template.spec.providerSpec.value.spotVMOptions` (このフィールドを追加し、をに設定し `{}` ます)。


スポット MachineSet YAML の要約の例を以下に示します。 ここでは、コンテキストに関する追加情報を含め、既存の worker MachineSet に新しいスポットマシンを設定するときに行う必要がある主な変更点について説明します。 (これは、機能 MachineSet 全体を表しているわけではありません。多くのフィールドは次のように省略されています)。

```
apiVersion: machine.openshift.io/v1beta1
kind: MachineSet
metadata:
  name: aro-cluster-abcd1-spot-eastus
spec:
  replicas: 2
  selector:
    matchLabels:
      machine.openshift.io/cluster-api-cluster: aro-cluster-abcd1
      machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
  template:
    metadata:
        machine.openshift.io/cluster-api-machineset: aro-cluster-abcd1-spot-eastus
    spec:
      providerSpec:
        value:
          spotVMOptions: {}
      taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

ファイルが更新されたら、それを適用します。

```azurecli-interactive
oc create -f spotmachineset.yaml
```

MachineSet が正常に作成されたことを確認するには、次のコマンドを実行します。
```azurecli-interactive
oc get machinesets -n openshift-machine-api
```

出力例を次に示します。 マシンが "準備完了" 状態になると、Machineset の準備が整います。
```
  NAME                                    DESIRED   CURRENT   READY   AVAILABLE   AGE
aro-cluster-5t2dj-worker-eastus1           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus2           1         1         1       1           3d1h
aro-cluster-5t2dj-worker-eastus3           1         1         1       1           3d1h
spot                                       1         1         1       1           2m47s
```

## <a name="schedule-interruptible-workloads"></a>割り込み可能ワークロードのスケジュール設定

及びをスポットノードに追加して、割り込み不可能なノードがスケジュールされないようにし、この及びの tolerations を、スケジュールするポッドに追加することをお勧めします。 MachineSet 仕様を使用してノードを及びすることができます。

たとえば、次の YAML をに追加でき `spec.template.spec` ます。

```
     taints:
        - effect: NoExecute
          key: spot
          value: 'true'
```

これにより、及びの toleration がない場合に、結果のノードでポッドがスケジュールされなくなり、 `spot='true'` その toleration を持たないポッドが削除されます。

Taints と tolerations の適用の詳細については、「 [node taints を使用してポッドの配置を制御](https://docs.openshift.com/container-platform/4.7/nodes/scheduling/nodes-scheduler-taints-tolerations.html)する」を参照してください。

## <a name="quota"></a>Quota

使用しているマシンの種類のクォータが短時間でも少なすぎる場合 (たとえば、別のノードが作成されているときに1つのノードがまだ削除されている場合など)、クォータの問題によってマシンがエラー状態になることがあります。 このため、スポットインスタンスに使用するコンピューターの種類のクォータは、必要なものよりも若干高くなるように設定することをお勧めします (2 * n の場合もあります。ここで、n はコンピューターで使用されるコアの数です)。 このオーバーヘッドによって、障害が発生したマシンを修正する必要がなくなります。ただし、比較的単純ですが、手動での介入が必要になります。

## <a name="node-readiness"></a>ノードの準備

上のリンクされているスポット VM のドキュメントで説明したように、Vm は使用できなくなった場合、または指定された最大価格で利用できなくなった場合に、割り当て解除されたプロビジョニング状態になります。

これにより、OpenShift には準備ができて **いない** ノードとしてマニフェストが表示されます。 **ノードとしてプロビジョニング** されたフェーズでは、マシンは正常な状態を維持します。

Vm が再び使用可能になると、 **準備完了** に戻ります。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="node-stuck-in-not-ready-state-underlying-vm-deallocated"></a>ノードが準備未完了状態になり、基になる VM の割り当てが解除されました

VM の割り当てが解除された後に長時間にわたってノードが準備されていない状態になった場合は、そのノードを削除するか、対応する OpenShift machine オブジェクトを削除してみてください。

### <a name="spot-machine-stuck-in-failed-state"></a>コンピューターが失敗した状態でスタックする

スポット VM を使用するマシン (OpenShift オブジェクト) がエラー状態でスタックしている場合は、手動で削除してみてください。 VM が存在しないために403が原因で削除できない場合は、マシンを編集し、ファイナライザーを削除します。
