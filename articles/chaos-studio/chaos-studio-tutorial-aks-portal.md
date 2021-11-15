---
title: Azure portal で Azure Chaos Studio を利用し、AKS Chaos Mesh 障害を利用する実験を作成する
description: Azure portal で Azure Chaos Mesh 障害を利用する実験を作成する
author: johnkemnetz
ms.topic: how-to
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7ae8da70739ded629753bdf3e3288a6b4ed279b8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373431"
---
# <a name="create-a-chaos-experiment-that-uses-a-chaos-mesh-fault-to-kill-aks-pods-with-the-azure-portal"></a>AKS ポッドを停止する Chaos Mesh 障害を利用するカオス実験を Azure portal で作成する

カオス実験を使用して、制御された環境でそれらの障害を引き起こすことで、アプリケーションに障害に対する回復性があることを確認できます。 このガイドでは、カオス実験と Azure Chaos Studio を使用して、名前空間に対して Azure Kubernetes Service ポッドの定期的な障害を発生させます。 この実験を実行することは、散発的な障害が発生したときにサービスを利用できなくなるのを防ぐのに役立つ場合があります。

Azure Chaos Studio では [Chaos Mesh](https://chaos-mesh.org/) を使用します。これは、Kubernetes で AKS クラスターにフォールトを挿入するための無料のオープンソース カオス エンジニアリング プラットフォームです。 Chaos Mesh フォールトは、AKS クラスターに Chaos Mesh をインストールする必要がある[サービス直接](chaos-studio-tutorial-aks-portal.md)フォールトです。 これらの同じ手順を使用して、AKS Chaos Mesh フォールトに対する実験を設定して実行できます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- AKS クラスター。 AKS クラスターがない場合は、[これらの手順に従って作成](../aks/kubernetes-walkthrough-portal.md)できます。

## <a name="set-up-chaos-mesh-on-your-aks-cluster"></a>AKS クラスターで Chaos Mesh を設定する

Chaos Studio で Chaos Mesh フォールトを実行する前に、AKS クラスターに Chaos Mesh をインストールする必要があります。

1. アクティブなサブスクリプションが、AKS クラスターがデプロイされているサブスクリプションに設定されている [Azure Cloud Shell](../cloud-shell/overview.md) ウィンドウで、次のコマンドを実行します。 `$RESOURCE_GROUP` と `$CLUSTER_NAME` を、リソース グループとクラスター リソースの名前に置き換えてください。

```bash
az aks get-credentials -g $RESOURCE_GROUP -n $CLUSTER_NAME
helm repo add chaos-mesh https://charts.chaos-mesh.org
helm repo update
kubectl create ns chaos-testing
helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-testing --version 2.0.3 --set chaosDaemon.runtime=containerd --set chaosDaemon.socketPath=/run/containerd/containerd.sock
```

2. 次のコマンドを実行して、Chaos Mesh ポッドがインストールされていることを確認します。

```bash
kubectl get po -n chaos-testing
```

次のような出力 (chaos-controller-manager と 1 つまたは複数の chaos-daemon) が表示されるはずです。

```bash
NAME                                        READY   STATUS    RESTARTS   AGE
chaos-controller-manager-69fd5c46c8-xlqpc   1/1     Running   0          2d5h
chaos-daemon-jb8xh                          1/1     Running   0          2d5h
chaos-dashboard-98c4c5f97-tx5ds             1/1     Running   0          2d5h
```

また、[Chaos Mesh Web サイトのインストール手順を使用する](https://chaos-mesh.org/docs/production-installation-using-helm/)こともできます。


## <a name="enable-chaos-studio-on-your-aks-cluster"></a>AKS クラスターで Chaos Studio を有効にする

Chaos Studio では、最初にリソースが Chaos Studio にオンボードされていない限り、リソースに対してフォールトを挿入することはできません。 リソースに[ターゲットと機能](chaos-studio-targets-capabilities.md)を作成することによって、リソースを Chaos Studio にオンボードします。 AKS クラスターのターゲットの種類は 1 つだけ (サービス直接) ですが、その他のリソースには最大 2 つのターゲットの種類がある場合があります。つまり、サービス直接フォールト用に 1 つとエージェントベース フォールト用に 1 つです。 Chaos Mesh フォールトの種類はそれぞれ機能 (PodChaos、NetworkChaos、IOChaos など) として表されます。

1. [Azure Portal](https://portal.azure.com)を開きます。
2. 検索バーで **Chaos Studio (プレビュー)** を検索します。
3. **[ターゲット]** をクリックし、AKS クラスターに移動します。
![Azure portal での [ターゲット] ビュー](images/tutorial-aks-targets.png)
4. AKS クラスターの横にあるチェックボックスをオンにして **[Enable targets]\(ターゲットを有効にする\)** をクリックし、ドロップダウン メニューから **[Enable service-direct targets]\(サービス直接ターゲットを有効にする\)** をクリックします。
![Azure portal でのターゲットの有効化](images/tutorial-aks-targets-enable.png)
5. 選択したリソースが正常に有効化されたことを示す通知が表示されます。
![ターゲットが正常に有効化されたことを示す通知](images/tutorial-aks-targets-enable-confirm.png)

これで、AKS クラスターが Chaos Studio に正常にオンボードされました。 また、 **[ターゲット]** ビューで、このリソースで有効になっている機能を管理できます。 リソースの横にある **[アクションの管理]** リンクをクリックすると、そのリソースに対して有効になっている機能が表示されます。

## <a name="create-an-experiment"></a>実験の作成
これで AKS クラスターがオンボードされたので、実験を作成できます。 カオス実験では、ターゲット リソースに対して実行するアクションを定義します。これらは、順次実行されるステップと、並列で実行される分岐に分けられます。

1. Chaos Studio ナビゲーションの **[実験]** タブをクリックします。 このビューでは、すべてのカオス実験を表示および管理できます。 **[実験の追加]** 
![Azure portal の実験ビュー](images/tutorial-aks-add.png) をクリックします
2. **[サブスクリプション]** 、 **[リソース グループ]** 、カオス実験をデプロイする **[場所]** に情報を入力します。 実験に **名前** を付けます。 **[次へ: 実験デザイナー >]** 
![基本的な実験の詳細の追加](images/tutorial-aks-add-basics.png) をクリックします
3. これで、Chaos Studio 実験デザイナーが表示されます。 実験デザイナーでは、ステップ、分岐、障害などを追加して実験を構築することができます。 **ステップ** と **分岐** にフレンドリ名を付け、 **[障害の追加]** をクリックします。
![実験デザイナー](images/tutorial-aks-add-designer.png)
4. ドロップダウンから **[AKS Chaos Mesh Pod Chaos]** を選択し、 **[期間]** に障害が継続する時間を分単位で入力し、 **[jsonSpec]** に以下の情報を入力します。

    Chaos Mesh jsonSpec を作成するには、次のようにします。
    1. フォールトの種類 (たとえば、[PodChaos](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files) という種類) については、Chaos Mesh のドキュメントを参照してください。
    2. Chaos Mesh のドキュメントを使用して、そのフォールトの種類の YAML 構成を作成します。

        ```yaml
        apiVersion: chaos-mesh.org/v1alpha1
        kind: PodChaos
        metadata:
          name: pod-failure-example
          namespace: chaos-testing
        spec:
          action: pod-failure
          mode: one
          duration: '30s'
          selector:
            namespaces:
              - default
        ```
    3. (spec プロパティ名を含む) `spec` の外側の YAML を削除し、spec の詳細のインデントを解除します。

        ```yaml
        action: pod-failure
        mode: one
        duration: '30s'
        selector:
          namespaces:
            - default
        ```
    4. [このような YAML から JSON へのコンバーター](https://www.convertjson.com/yaml-to-json.htm)を使用して、Chaos Mesh YAML を JSON に変換し、それを最小化します。

        ```json
        {"action":"pod-failure","mode":"one","duration":"30s","selector":{"namespaces":["default"]}}
        ```
    5. 最小化された JSON をポータルの **[json]** フィールドに貼り付けます。




**[次へ: ターゲット リソース >]** をクリックします
![フォールト プロパティ](images/tutorial-aks-add-fault.png)
5. AKS クラスターを選択し、 **[次へ]** をクリックします
![ターゲットの追加](images/tutorial-aks-add-targets.png)
6. 実験が正しく表示されていることを確認し、 **[確認と作成]** 、 **[作成]** の順にクリックします。
![実験を確認して作成する](images/tutorial-aks-add-review.png)

## <a name="give-experiment-permission-to-your-aks-cluster"></a>AKS クラスターに実験のアクセス許可を付与する
カオス実験を作成すると、Chaos Studio によって、ターゲット リソースに対してフォールトを実行するシステム割り当てマネージド ID が作成されます。 実験を正常に実行するには、この ID にターゲット リソースへの[適切なアクセス許可](chaos-studio-fault-providers.md)を付与する必要があります。

1. AKS クラスターに移動し、 **[アクセス制御 (IAM)]** をクリックします。
![AKS の [概要] ページ](images/tutorial-aks-access-resource.png)
2. **[追加]** 、 **[ロールの割り当ての追加]** の順にクリックします。
![アクセス制御の概要](images/tutorial-aks-access-iam.png)
3. **Azure Kubernetes Service クラスター管理者ロール** を検索し、そのロールを選択します。 **[次へ]** をクリックします
![ AKS クラスター管理者ロールの割り当て](images/tutorial-aks-access-role.png)
4. **[メンバーの選択]** をクリックし、実験名を検索します。 実験を選択し、 **[選択]** をクリックします。 同じテナント内に同じ名前の実験が複数ある場合、実験名はランダムな文字が追加されて切り詰められます。
![ロールへの実験の追加](images/tutorial-aks-access-experiment.png)
5. **[レビューと割り当て]** をクリックし、もう一度 **[レビューと割り当て]** をクリックします。

## <a name="run-your-experiment"></a>実験を実行する
これで、実験を実行する準備が整いました。 影響を確認するには、AKS クラスターの概要を開き、別のブラウザー タブで **[分析情報]** に移動することをお勧めします。 **[アクティブなポッド数]** のライブ データには、実験を実行した場合の影響が表示されます。

1. **[実験]** ビューで、実験をクリックし、 **[開始]** 、 **[OK]** の順にクリックします。
![実験の開始](images/tutorial-aks-start.png)
2. **[状態]** が **[実行中]** に変わったら、 **[履歴]** の下にある最新の実行の **[詳細]** をクリックして、実行中の実験の詳細を確認します。

## <a name="next-steps"></a>次のステップ
これで AKS Chaos Mesh サービス直接実験を実行したので、次のことを行う準備ができました。
- [エージェントベース フォールトを使用する実験を作成する](chaos-studio-tutorial-agent-based-portal.md)
- [実験を管理する](chaos-studio-run-experiment.md)
