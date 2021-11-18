---
title: クラスター セキュリティに関するベスト プラクティス
titleSuffix: Azure Kubernetes Service
description: Azure Kubernetes Service (AKS) でクラスターのセキュリティとアップグレードを管理する方法に関するクラスター オペレーターのベスト プラクティスについて説明します
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 73c9ac0780c6a1217e58f40b4e64f290e8904457
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492438"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのクラスターのセキュリティとアップグレードに関するベスト プラクティス

Azure Kubernetes Service (AKS) でクラスターを管理する際には、ワークロードとデータのセキュリティが重要な考慮事項になります。 論理的な分離を使用してマルチテナント クラスターを実行する場合は、特にリソースとワークロードのアクセスをセキュリティで保護する必要があります。 最新の Kubernetes およびノード OS のセキュリティ更新プログラムを適用することで、攻撃のリスクを最小限に抑えます。

この記事では、AKS クラスターをセキュリティで保護する方法について説明します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Azure Active Directory と Kubernetes のロールベースのアクセス制御 (Kubernetes RBAC) を使用して API サーバー アクセスをセキュリティで保護する。
> * ノード リソースへのコンテナー アクセスをセキュリティで保護する。
> * AKS クラスターを最新の Kubernetes バージョンにアップグレードする。
> * ノードを最新の状態に保ち、セキュリティ パッチを自動的に適用する。

また、[コンテナー イメージの管理][best-practices-container-image-management]と[ポッドのセキュリティ][best-practices-pod-security]に関するベスト プラクティスも参照できます。

また、[Azure Kubernetes Services と Defender for Cloud の統合][security-center-aks]を使用して、脅威を検出したり、AKS クラスターをセキュリティで保護するための推奨事項を確認したりすることもできます。

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>API サーバーとクラスター ノードへのアクセスをセキュリティで保護する

> **ベスト プラクティスのガイダンス** 
>
> クラスターをセキュリティで保護する最も重要な方法の 1 つは、Kubernetes API サーバーへのアクセスをセキュリティで保護することです。 API サーバーへのアクセスを制御するには、Kubernetes RBAC と Azure Active Directory (Azure AD) を統合します。 これらのコントロールを使用して、Azure サブスクリプションへのアクセスをセキュリティで保護するのと同じ方法で AKS を保護します。

Kubernetes API サーバーには、クラスター内でアクションを実行する要求向けに単一の接続ポイントが用意されています。 API サーバーへのアクセスをセキュリティで保護および監査するには、アクセスを制限し、可能な最小限のレベルのアクセス許可を付与します。 このアプローチは Kubernetes に固有のものではありませんが、マルチテナントに使用するために AKS クラスターを論理的に分離している場合は特に重要です。

Azure AD には、AKS クラスターと統合できるエンタープライズ対応の ID 管理ソリューションが用意されています。 Kubernetes には ID 管理ソリューションが用意されていないので、API サーバーへのアクセスを細かく制限することが困難な場合があります。 AKS の Azure AD と統合されたクラスターでは、既存のユーザー アカウントとグループ アカウントを使用して API サーバーに対してユーザーを認証します。

![AKS クラスター用の Azure Active Directory 統合](media/operator-best-practices-cluster-security/aad-integration.png)

Kubernetes RBAC と Azure AD 統合を使用すると、API サーバーをセキュリティで保護し、1 つの名前空間と同様に、スコープが指定されたリソース セットに対して必要な最小限のアクセス許可を与えることができます。 異なる Azure AD ユーザーまたはグループに異なる Kubernetes ロールを与えることができます。 細かいアクセス許可を使用することで、API サーバーへのアクセスを制限し、実行されたアクションの明確な監査証跡を提供することができます。

推奨されるベスト プラクティスとして、個々の ID ではなく、"*グループ*" を使用してファイルとフォルダーへのアクセス権を付与します。 たとえば、Azure AD "*グループ*" メンバーシップを使用して、ユーザーを個々の "*ユーザー*" ではなく Kubernetes ロールにバインドします。 ユーザーのグループ メンバーシップが変わると、それに応じて AKS クラスターに対するアクセス許可も変わります。 

ここでは、個々のユーザーをロールに直接バインドし、彼らの職務は変わるとします。 Azure AD グループのメンバーシップには更新が行われますが、AKS クラスターに対するアクセス許可には行われません。 このシナリオでは、最終的に、ユーザーに必要なアクセス許可よりも多くのアクセス許可が付与されることになります。

Azure AD 統合、Kubernetes RBAC、および Azure RBAC の詳細については、[AKS での認証と承認のベスト プラクティス][aks-best-practices-identity]に関する記事を参照してください。

## <a name="secure-container-access-to-resources"></a>リソースへのコンテナー アクセスをセキュリティで保護する

> **ベスト プラクティスのガイダンス** 
> 
> コンテナーで実行できるアクションへのアクセスを制限します。 最小限のアクセス許可を付与し、ルート アクセスまたは特権エスカレーションの使用を避けます。

ユーザーまたはグループに必要最小限の特権を付与するのと同様に、コンテナーも必要なアクションとプロセスのみに制限するようにします。 攻撃のリスクを最小限に抑えるには、昇格された特権またはルート アクセスを必要とするアプリケーションとコンテナーを構成しないようにします。 

たとえば、ポッドのマニフェストには `allowPrivilegeEscalation: false` を設定します。 このような Kubernetes 組み込みの "*ポッドのセキュリティ コンテキスト*" を使用して、実行するユーザーやグループ、公開する Linux 機能など、追加のアクセス許可を定義できます。 その他のベスト プラクティスについては、[リソースへのポッドのアクセスをセキュリティで保護する方法][pod-security-contexts]に関する記事を参照してください。

コンテナー アクションをさらにより細かく制御するには、*AppArmor* や *seccomp* など、組み込みの Linux セキュリティ機能を使用することもできます。 
1. ノード レベルで Linux セキュリティ機能を定義します。
1. ポッド マニフェストを使用して機能を実装します。 

組み込みの Linux セキュリティ機能は、Linux ノードとポッドに対してのみ使用できます。

> [!NOTE]
> 現在、Kubernetes 環境は悪意のあるマルチテナントの使用に対して完全に安全ではありません。 *AppArmor*、*seccomp*、*ポッドのセキュリティ ポリシー*、ノードの Kubernetes RBAC などの追加のセキュリティ機能により、攻撃を効率的にブロックします。 
>
>悪意のあるマルチテナント ワークロードを実行する場合の真のセキュリティを実現するために、ハイパーバイザーのみを信頼してください。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 
>
> この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。

### <a name="app-armor"></a>App Armor

コンテナー アクションを制限するために、[AppArmor][k8s-apparmor] Linux カーネル セキュリティ モジュールを使用できます。 AppArmor は基となる AKS ノード OS に含まれており、既定で有効です。 読み取り、書き込み、実行のアクション、またはファイルシステムのマウントなどのシステム機能を制限する AppArmor プロファイルを作成します。 既定の AppArmor プロファイルでは、さまざまな `/proc` と `/sys` の場所へのアクセスが制限されており、基となるノードからコンテナーを論理的に分離する手段が用意されています。 AppArmor は、Kubernetes ポッドだけでなく、Linux 上で動作するあらゆるアプリケーションに対応しています。

![コンテナーの動作を制限するために AKS クラスターで使用されている AppArmor プロファイル](media/operator-best-practices-container-security/apparmor.png)

AppArmor の動作を確認するために、次の例ではファイルへの書き込みを防止するプロファイルを作成します。 
1. AKS ノードに [SSH][aks-ssh] を接続します。
1. *deny-write.profile* という名前のファイルを作成します。
1. 次の内容を貼り付けます。

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

`apparmor_parser` コマンドを使用して AppArmor プロファイルが追加されます。 
1. プロファイルを AppArmor に追加します。
1. 前の手順で作成したプロファイルの名前を指定します。

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    プロファイルが正しく解析され、AppArmor に適用された場合、出力は表示されず、コマンド プロンプトに戻ります。

1. ローカル コンピューターから、*aks-apparmor.yaml* という名前のポッド マニフェストを作成します。 このマニフェストにより:
    * `container.apparmor.security.beta.kubernetes` の注釈を定義します。
    * 前の手順で作成した *deny-write* プロファイルを参照します。

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: hello-apparmor
      annotations:
        container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
    spec:
      containers:
      - name: hello
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. ポッドを展開した状態で、*hello-apparmor* ポッドが "*ブロック済み*" と表示されていることを確認します。

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

AppArmor の詳細については、[Kubernetes の AppArmor プロファイル][k8s-apparmor]に関する記事を参照してください。

### <a name="secure-computing"></a>セキュア コンピューティング

AppArmor は任意の Linux アプリケーションで機能しますが、[seccomp (*sec* ure *comp* uting)][seccomp] はプロセス レベルで機能します。 seccomp は Linux カーネル セキュリティ モジュールでもあり、AKS ノードで使用される Docker ランタイムでネイティブにサポートされています。 seccomp を使用してコンテナーのプロセス呼び出しを制限できます。 次の方法で、実行する最小限のアクセス許可のみをコンテナーに付与するというベスト プラクティスに従います。
* フィルターを使用して、どのアクションを許可または拒否するかを定義する。
* ポッドの YAML マニフェスト内に、seccomp フィルターに関連付けるための注釈を付ける。 

seccomp の動作を確認するには、ファイルに対するアクセス許可の変更を防止するフィルターを作成します。 
1. AKS ノードに [SSH][aks-ssh] を接続します。
1. */var/lib/kubelet/seccomp/prevent-chmod* という名前の seccomp フィルターを作成します。
1. 次の内容を貼り付けます。

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    バージョン 1.19 以降では、以下を構成する必要があります。

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. ローカル コンピューターから、*aks-seccomp.yaml* という名前のポッド マニフェストを作成し、次の内容を貼り付けます。 このマニフェストにより:
    * `seccomp.security.alpha.kubernetes.io` の注釈を定義します。
    * 前の手順で作成した *prevent-chmod* フィルターを参照します。

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
      annotations:
        seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
    spec:
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    バージョン 1.19 以降では、以下を構成する必要があります。

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. [kubectl apply][kubectl-apply] コマンドを使用してサンプル ポッドを展開します。

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. [kubectl get pods][kubectl-get] コマンドを使用して、ポッドの状態を確認します。 
    * ポッドからはエラーが報告されています。 
    * 次の出力例に示すように、`chmod` コマンドは seccomp フィルターによって実行されません。    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

使用できるフィルターの詳細については、「[Seccomp security profiles for Docker (Docker の Seccomp セキュリティ プロファイル)][seccomp]」を参照してください。

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>最新版の Kubernetes に定期的に更新する

> **ベスト プラクティスのガイダンス** 
> 
> 新機能とバグ修正を常に最新の状態に保つには、AKS クラスターの Kubernetes バージョンを定期的にアップグレードしてください。

Kubernetes は、従来のインフラストラクチャ プラットフォームよりも速いペースで新機能をリリースしています。 Kubernetes の更新プログラムには以下が含まれます。
* 新機能
* バグまたはセキュリティの修正 

新機能は通常、"*アルファ版*" と "*ベータ版*" のステータスを経て "*安定版*" になります。 安定版になると一般提供され、運用環境での使用が推奨されます。 Kubernetes の新機能のリリース サイクルを使用すると、定期的に重大な変更が発生したり、デプロイやテンプレートを調整したりすることなく、Kubernetes を更新できます。

AKS では、Kubernetes の 3 つのマイナー バージョンがサポートされています。 新しいマイナー パッチ バージョンが導入されると、サポートされている最も古いマイナー バージョンと修正プログラムのリリースは、提供終了となります。 Kubernetes のマイナー更新は定期的に行われます。 サポート対象であり続けるために、必要なアップグレードを確認するガバナンス プロセスを用意してください。 詳細については、[AKS でサポートされる Kubernetes のバージョン][aks-supported-versions]に関する記事を参照してください。

実際のクラスターに使用できるバージョンを確認するには、次の例に示すように [az aks get-upgrades][az-aks-get-upgrades] コマンドを使用します。

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

次に、[az aks upgrade][az-aks-upgrade] コマンドを使用して AKS クラスターをアップグレードすることができます。 アップグレード プロセスにより、次のことが安全に行われます。
* ノードの遮断とドレインを一度に 1 つずつ行います。
* 残りのノード上のポッドをスケジュールします。
* 最新の OS および Kubernetes バージョンを実行している新しいノードをデプロイします。

>[!IMPORTANT]
> 新しいマイナー バージョンを開発テスト環境でテストし、ワークロードが新しい Kubernetes バージョンでも正常であり続けることを検証します。 
>
> ワークロードが依存する API (バージョン 1.16 など) が Kubernetes で非推奨になる可能性があります。 新しいバージョンを運用するとき、[個々のバージョンで複数のノード プール](use-multiple-node-pools.md)を使用することを検証してください。そして、個々のプールを一度に 1 つずつアップグレードし、クラスター全体に更新を徐々に展開します。 複数のクラスターを実行している場合、一度に 1 つのクラスターをアップグレードし、影響や変更を段階的に監視します。
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

AKS のアップグレードの詳細については、[AKS でサポートされる Kubernetes のバージョン][aks-supported-versions]と [AKS クラスターのアップグレード][aks-upgrade]に関する記事を参照してください。

## <a name="process-linux-node-updates"></a>Linux ノード更新プログラムの処理

AKS の Linux ノードには、毎晩、ディストリビューション更新チャネルを介してセキュリティ修正プログラムが取得されます。 この動作は、ノードが AKS クラスターにデプロイされるときに自動的に構成されます。 中断や実行中のワークロードへの潜在的な影響を最小限に抑えるために、セキュリティ修正プログラムまたはカーネルの更新プログラムに必要な場合でも、ノードは自動的に再起動されません。 ノードの再起動を処理する方法については、[AKS のノードにセキュリティとカーネルの更新プログラムを適用する方法][aks-kured]に関する記事を参照してください。

### <a name="node-image-upgrades"></a>ノード イメージのアップグレード

無人アップグレードでは、Linux ノード OS にアップデートが適用されますが、クラスターのノードを作成するためのイメージは変更されません。 新しい Linux ノードがクラスターに追加された場合は、元のイメージを使用してノードが作成されます。 この新しいノードは、毎晩の自動チェックで利用可能なすべてのセキュリティおよびカーネル アップデートを受け取りますが、すべてのチェックと再起動が完了するまではパッチは適用されません。 ノード イメージのアップグレードを使用して、クラスターで使用されているノード イメージをチェックして更新することもできます。 ノード イメージのアップグレードに関する詳細については、「[Azure Kubernetes Service (AKS) ノード イメージのアップグレード][node-image-upgrade]」を参照してください。

## <a name="process-windows-server-node-updates"></a>Windows Server ノード更新プログラムの処理

Windows Server ノードでは、ポッドを安全に切断およびドレインし、更新されたノードをデプロイするために、ノード イメージのアップグレード操作を定期的に実行します。

<!-- EXTERNAL LINKS -->
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../defender-for-cloud/defender-for-kubernetes-introduction.md
[node-image-upgrade]: node-image-upgrade.md
