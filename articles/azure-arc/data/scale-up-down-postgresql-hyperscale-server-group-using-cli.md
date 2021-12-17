---
title: CLI (az または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン
description: CLI (az または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 1c33c2f4bf89b76abf40d12146965114ba4918b0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564501"
---
# <a name="scale-up-and-down-an-azure-database-for-postgresql-hyperscale-server-group-using-cli-az-or-kubectl"></a>CLI (az または kubectl) を使用した Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアップとスケールダウン

場合によっては、サーバー グループの特性または定義の変更が必要になることがあります。 次に例を示します。

- 各コーディネーター ノードまたはワーカー ノードが使用する仮想コアの数をスケールアップまたはスケールダウンします
- 各コーディネーター ノードまたはワーカー ノードが使用するメモリをスケールアップまたはスケールダウンします

このガイドでは、仮想コアやメモリをスケーリングする方法について説明します。

サーバー グループの仮想コアまたはメモリの設定をスケールアップまたはスケールダウンすると、各仮想コアとメモリの設定に対して最小値または最大値を設定できます。 特定の数の仮想コアまたは特定の量のメモリを使用するようにサーバー グループを構成する場合は、最小設定の値を最大設定の値にします。 仮想コアとメモリに設定された値を大きくする前に、次を確認する必要があります。 
- デプロイをホストする物理インフラストラクチャで十分なリソースを使用できる 
- 同じシステム上に併置されている複数のワークロードが同じ仮想コアまたはメモリに関して競合していない

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="show-the-current-definition-of-the-server-group"></a>サーバー グループの現在の定義を表示する

サーバー グループの現在の定義を表示し、現在の仮想コアとメモリの設定を確認するには、次のコマンドのいずれかを実行します。

### <a name="with-azure-cli-az"></a>Azure CLI (az) の場合

```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```
### <a name="cli-with-kubectl"></a>kubectl を使用した CLI

```console
kubectl describe postgresql/<server group name> -n <namespace name>
```

サーバー グループの構成が返されます。 既定の設定でサーバー グループを作成した場合は、次のように定義が表示されます。

```json
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
...
```

## <a name="interpret-the-definition-of-the-server-group"></a>サーバー グループの定義を解釈する

サーバー グループの定義では、ノードあたりの最小または最大の仮想コアとノードあたりの最小または最大のメモリの各設定を含むセクションが "**スケジュール**" セクションです。 このセクションでは、最大設定が "**制限**" と呼ばれるサブセクションに保存されます。最小設定は "**要求**" と呼ばれるサブセクションに保存されます。

最小設定を最大設定とは異なる値にすると、必要に応じて、サーバー グループに要求されたリソースが割り当てられることが保証されます。 設定した制限を超えることはありません。

サーバー グループで実際に使用されるリソース (仮想コアとメモリ) は最大設定までであり、クラスターで使用可能なワークロードとリソースによって異なります。 設定を最大値で制限しない場合、サーバー グループは、スケジュールされている Kubernetes ノードに対してKubernetes クラスターが割り当てているすべてのリソースを使用できます。

これらの仮想コアとメモリの設定は、PostgreSQL Hyperscale サーバー グループを構成する Postgres インスタンスの各ロール (コーディネーターとワーカー) に適用されます。 ロールごとに要求と制限を定義できます。 ロールごとに異なる要求と制限の設定を定義できます。 これらは、必要に応じて類似させることもできます。

既定の構成では、最小メモリのみが 256 Mi に設定されています。これは、PostgreSQL Hyperscale を実行するために推奨される最小メモリ量です。

> [!NOTE]
> 最小値を設定しても、サーバー グループで必ずしもその最小値が使用されるとは限りません。 つまり、サーバー グループに必要な場合は、少なくともこの最小値が割り当てられることが保証されます。 たとえば、`--minCpu 2` の設定を考えてみましょう。 サーバー グループが常に 2 つ以上の仮想コアを使用することを意味するわけではありません。 これは、その必要がない場合にはサーバー グループは 2 つ未満の仮想コアを使用して起動し、後で必要になったときに少なくとも 2 つの仮想コアが割り当てられることが保証されることを意味します。 これは、Kubernetes クラスターが、必要に応じて 2 つの仮想コアをサーバー グループに割り当てることができるように、他のワークロードにリソースを割り当てることを含意します。 また、スケールアップとスケールダウンは、kubernetes ポッドの再起動が必要になるため、オンライン操作ではありません。

>[!NOTE]
>システムの構成を変更する前に、[こちら](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)で Kubernetes リソース モデルについてよく理解していることを確認してください

## <a name="scale-up-and-down-the-server-group"></a>サーバー グループをスケールアップおよびスケールダウンする

スケールアップとは、サーバー グループの仮想コアやメモリの設定の値を大きくすることです。
スケールダウンとは、サーバー グループの仮想コアやメモリの設定の値を小さくすることです。

設定を行う場合は、Kubernetes クラスターに設定した構成内で考慮する必要があります。 Kubernetes クラスターが満たすことができない値を設定していないことを確認します。 これにより、データベース インスタンスを利用できないといったエラーや予測できない動作が発生する可能性があります。 例として、構成を変更した後にサーバー グループの状態が "_更新中_" のままになっている場合は、以下のパラメーターを Kubernetes クラスターが満たすことができない値に設定している可能性があります。 その場合は、変更を元に戻すか、_troubleshooting_section を参照してください。

設定方法
- 最小仮想コアを設定するには、`--cores-request` を設定します。
- 最大仮想コアを設定するには、`--cores-limit` を設定します。
- 最小メモリを設定するには、`--memory-request` を設定します。
- 最大メモリを設定するには、`--memory-limit` を設定します。

設定を適用するロールを示す方法
- コーディネーター ロールの設定を構成するには、`coordinator=<value>` を指定します。
- ワーカー ロールの設定を構成するには (指定された設定はすべてのワーカーで同じ値に設定されます)、`worker=<value>` を指定します。


> [!CAUTION]
> Kubernetes では、対応する要求の設定を構成せずに制限の設定を構成すると、要求値が制限と同じ値になります。 これによりサーバー グループが使用できなくなる可能性があります。十分なリソースがある Kubernetes ノードがない場合に、そのポッドが再スケジュールされない場合があるためです。 このような状況を回避するために、下の例では、要求と制限の両方の設定を設定する方法を示しています。


**一般的な構文は次のとおりです。**

```azurecli
az postgres arc-server edit -n <servergroup name> --memory-limit/memory-request/cores-request/cores-limit <coordinator=val1,worker=val2> --k8s-namespace <namespace> --use-k8s
```

メモリ設定に指定する値は、数値の後にボリュームの単位が続きます。 たとえば、1 Gb を指定するには、1024 Mi または 1 Gi を指定します。
コアの数を示すには、単位を付けずに数値を渡します。 

### <a name="examples-using-the-azure-cli"></a>Azure CLI を使用した例

**コーディネーター ロールは 2 コアを超えないように構成し、ワーカー ロールは 4 コアを超えないように構成します。**

```azurecli
 az postgres arc-server edit -n postgres01 --cores-request coordinator=1, --cores-limit coordinator=2  --k8s-namespace arc --use-k8s
 az postgres arc-server edit -n postgres01 --cores-request worker=1, --cores-limit worker=4 --k8s-namespace arc --use-k8s
```

or
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator=1,worker=1 --cores-limit coordinator=4,worker=4 --k8s-namespace arc --use-k8s
```

> [!NOTE]
> これらのパラメーターの詳細については、`az postgres arc-server edit --help` を実行してください。

### <a name="example-using-kubernetes-native-tools-like-kubectl"></a>Kubernetes ネイティブ ツール (`kubectl` など) を使用した例

次のコマンドを実行します。 
```console
kubectl edit postgresql/<servergroup name> -n <namespace name>
```

これにより、`vi` エディターが表示されます。ここで、構成の参照や変更を行うことができます。 次のコードを使用して、目的の設定を仕様のフィールド名にマップします。

> [!CAUTION]
> 以下は、この構成の編集方法を解説するための例です。 構成を更新する前に、Kubernetes クラスターで適用できる値にパラメーターを必ず設定してください。

たとえば、次のコーディネーターとワーカー両方のロールの設定を、次の値に設定するとします。
- 最小仮想コア数 = `2` 
- 最大仮想コア数 = `4`
- 最小メモリ = `512Mb`
- 最大メモリ = `1Gb` 

サーバー グループの定義を、下の構成と一致するように設定します。

```json
...
  spec:
  dev: false
  engine:
    extensions:
    - name: citus
    version: 12
  scale:
    replicas: 1
    syncReplicas: "0"
    workers: 4
  scheduling:
    default:
      resources:
        requests:
          memory: 256Mi
    roles:
      coordinator:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
      worker:
        resources:
          limits:
            cpu: "4"
            memory: 1Gi
          requests:
            cpu: "2"
            memory: 256Mi
...
```

`vi` エディターに慣れていない場合は、[こちら](https://www.computerhope.com/unix/uvi.htm)で必要なコマンドの説明を参照してください。
- 編集モード: `i`
- 矢印で移動
- 編集の停止: `esc`
- 保存せずに終了: `:qa!`
- 保存して終了: `:qw!`


## <a name="reset-to-default-values"></a>既定値にリセットする
コア/メモリ制限/要求のパラメーターを既定値にリセットするには、それらのパラメーターを編集して、実際の値ではなく空の文字列を渡します。 たとえば、コア制限パラメーターをリセットする場合は、次のコマンドを実行します。

```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --k8s-namespace arc --use-k8s
az postgres arc-server edit -n postgres01 --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

or 
```azurecli
az postgres arc-server edit -n postgres01 --cores-request coordinator='',worker='' --cores-limit coordinator='',worker='' --k8s-namespace arc --use-k8s
```

## <a name="next-steps"></a>次のステップ

- [Azure Database for PostgreSQL Hyperscale サーバー グループのスケールアウト](scale-out-in-postgresql-hyperscale-server-group.md)
- [ストレージの構成と Kubernetes ストレージの概念](storage-configuration.md)
- [Kubernetes リソース モデル](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
