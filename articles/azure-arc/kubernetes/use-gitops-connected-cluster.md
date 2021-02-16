---
title: Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps を使用して Azure Arc 対応の Kubernetes クラスターを構成する (プレビュー)
keywords: GitOps、Kubernetes、K8s、Azure、Arc、Azure Kubernetes Service、AKS、コンテナー
ms.openlocfilehash: 72dc42fffb3653de81477fa504c11b9b0328d2eb
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988700"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)

Kubernetes に関連する GitOps では、Git リポジトリ内で Kubernetes 構成 (デプロイ、名前空間など) の望ましい状態を宣言した後、ポーリングしたり、演算子を使用してこれらの構成をクラスターにプルベースで展開したりします。 このドキュメントでは、Azure Arc 対応 Kubernetes クラスターでのこのようなワークフローのセットアップについて説明します。

クラスターと Git リポジトリの間の接続は、Azure Resource Manager で `Microsoft.KubernetesConfiguration/sourceControlConfigurations` 拡張リソースとして作成されます。 `sourceControlConfiguration` リソースのプロパティは、Kubernetes リソースが Git からお客様のクラスターへとフローする場所と方法を表します。 `sourceControlConfiguration` データの保存中は Azure Cosmos DB データベースに暗号化された状態で格納されるので、データの機密性が確保されます。

クラスターで実行されている `config-agent` は、Azure Arc 対応 Kubernetes リソースで新規または更新された `sourceControlConfiguration` の拡張リソースを監視し、各 `sourceControlConfiguration` について Git リポジトリを監視するための Flux 演算子をデプロイし、`sourceControlConfiguration` に加えられた更新を適用する役割を担います。 複数の `sourceControlConfiguration` リソースを同じ Azure Arc 対応 Kubernetes クラスター上に作成し、マルチテナントを実現できます。 各 `sourceControlConfiguration` を異なる `namespace` スコープで作成し、それぞれの名前空間内のデプロイを制限することができます。

Git リポジトリには、Namespace、ConfigMap、Deployment、DaemonSet などの有効な Kubernetes リソースを記述する YAML 形式のマニフェストを含めることができます。また、アプリケーションをデプロイするための Helm グラフを含めることもできます。 一般的なシナリオ セットとして、組織のベースライン構成の定義が含まれます。これには、一般的な Azure ロールとバインド、監視またはログ エージェント、またはクラスター全体のサービスが含まれる場合があります。

同じパターンを使用して、異機種混合の環境全体にデプロイされているような、より大規模なクラスターのコレクションを管理できます。 たとえば、組織のベースライン構成を定義するリポジトリが 1 つあり、それを一度に数十の Kubernetes クラスターに適用しているとします。 [Azure Policy では、スコープ (サブスクリプションまたはリソース グループ) にあるすべての Azure Arc 対応 Kubernetes リソースで、特定のパラメーター セットを使用して `sourceControlConfiguration` の作成を自動化](use-azure-policy.md)できます。

このファースト ステップ ガイドでは、クラスターと管理者のスコープを使用して一連の構成を適用する方法について説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure Arc 対応 Kubernetes に接続されたクラスターが既に存在することを前提としています。 接続されたクラスターが必要な場合は、[クラスターの接続についてのクイックスタート](./connect-cluster.md)のページを参照してください。

## <a name="create-a-configuration"></a>構成を作成する

本ドキュメントで使用されている[リポジトリの例](https://github.com/Azure/arc-k8s-demo)は、いくつかの名前空間をプロビジョニングし、共通のワークロードをデプロイし、チーム固有の構成を提供するクラスター オペレーターのペルソナを中心に構成されています。 このリポジトリを使用すると、クラスターに次のリソースが作成されます。

**Namespace:** `cluster-config`、`team-a`、`team-b`
**Deployment:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

`config-agent` は、新規または更新された `sourceControlConfiguration` を 30 秒ごと (`config-agent` が新規または更新された構成を取得するためにかかる最大時間) に Azure でポーリングします。
`sourceControlConfiguration` を持つプライベート リポジトリを関連付ける場合は、[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)手順も完了するようにします。

### <a name="using-azure-cli"></a>Azure CLI の使用

`k8sconfiguration` の Azure CLI 拡張機能を使用して、接続されたクラスターを [Git リポジトリの例](https://github.com/Azure/arc-k8s-demo)にリンクしてください。 この構成に `cluster-config` と名前を付け、`cluster-config` 名前空間にオペレーターを配置するようにエージェントに指示し、オペレーターに `cluster-admin` アクセス許可を与えます。

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Pending",
    "lastConfigApplied": "0001-01-01T00:00:00",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": null,
  "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
  "resourceGroup": "MyRG",
  "sshKnownHostsContents": "",
  "systemData": {
    "createdAt": "2020-11-24T21:22:01.542801+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
  },
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
  ```

#### <a name="use-a-public-git-repo"></a>パブリック Git リポジトリを使用する

| パラメーター | Format |
| ------------- | ------------- |
| --repository-url | http[s]://server/repo[.git] or git://server/repo[.git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>SSH および Flux で作成されたキーを使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes
| ------------- | ------------- | ------------- |
| --repository-url | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能

> [!NOTE]
> Flux によって生成される公開キーは、Git サービス プロバイダーのユーザー アカウントに追加する必要があります。 キーがユーザー アカウントではなくリポジトリに追加された場合は、`user@` の代わりに `git@` を URL で使用します。 [詳細を表示](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>SSH および ユーザー指定のキーを使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| --ssh-private-key | base64-encoded キー ([PEM 形式](https://aka.ms/PEMformat)) | キーを直接指定 |
| --ssh-private-key-file | ローカル ファイルへの完全なパス | PEM 形式のキーを含むローカル ファイルへの完全なパスを指定

> [!NOTE]
> 独自の秘密キーを直接またはファイルに指定します。 キーは [PEM 形式](https://aka.ms/PEMformat) で、末尾に改行文字 (\n) を使用する必要があります。  関連付けられた公開キーは、Git サービス プロバイダーのユーザー アカウントに追加する必要があります。 キーがユーザー アカウントではなくリポジトリに追加された場合は、`user@` の代わりに `git@` を使用します。 [詳細を表示](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH および ユーザー指定の既知のホストを使用してプライベート Git ホストを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| --ssh-known-hosts | base64-encoded | 直接指定された既知のホストのコンテンツ |
| --ssh-known-hosts-file | ローカル ファイルへの完全なパス | ローカル ファイルに指定された既知のホストのコンテンツ

> [!NOTE]
> Flux 演算子は、SSH 接続を確立する前に Git リポジトリを認証するため、既知のホスト ファイルに一般的な Git ホストの一覧を保持します。 一般的ではない Git リポジトリまたは独自の Git ホストを使用している場合は、ホスト キーを指定して、Flux がリポジトリを識別できるようにする必要があります。 既知のホストのコンテンツは、直接指定することも、ファイルに指定することもできます。 [既知のホストのコンテンツに関する形式の仕様](https://aka.ms/KnownHostsFormat)をご確認ください。
> これは、前述の SSH キー シナリオの 1 つと組み合わせて使用できます。

#### <a name="use-a-private-git-repo-with-https"></a>HTTPS を使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url | https://server/repo [.git] | 基本認証を使用した HTTPS |
| --https-user | raw または base64-encoded | HTTPS のユーザー名 |
| --https-key | raw または base64-encoded | HTTPS の個人用アクセス トークンまたはパスワード

> [!NOTE]
> HTTPS Helm リリースのプライベート認証は、バージョン 1.2.0 以上の Helm 演算子グラフでのみサポートされます。  既定ではバージョン 1.2.0 が使用されます。
> HTTPS Helm リリースのプライベート認証は現在、Azure Kubernetes Services のマネージド クラスターではサポートされていません。
> プロキシを介して Git リポジトリにアクセスするために Flux が必要な場合は、プロキシ設定を使用して Azure Arc エージェントを更新する必要があります。 [詳細情報](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>追加のパラメーター

構成をカスタマイズするために使用できるその他のパラメーターは次のとおりです。

`--enable-helm-operator`:*省略可能*: Helm グラフの配置のサポートを有効にするように切り替えます。

`--helm-operator-params`:*省略可能*: Helm 演算子 (有効な場合) のグラフの値。  たとえば、'--set helm.versions=v3' です。

`--helm-operator-version`:*省略可能*: Helm 演算子 (有効な場合) のグラフのバージョン。 '1.2.0' 以降を使用します。 既定値は1.2.0 です。

`--operator-namespace`:*省略可能*: オペレーターの名前空間の名前。 既定値は 'default' です。 最大 23 文字。

`--operator-params`:*省略可能*: オペレーターのパラメーター。 単一引用符で囲む必要があります。 たとえば、```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` のように指定します。

--operator-params でサポートされているオプション

| オプション | 説明 |
| ------------- | ------------- |
| --git-branch  | Kubernetes マニフェストに使用する Git リポジトリのブランチ。 既定値は 'master' です。 新しいリポジトリではルート ブランチの名前が 'main' になっていますが、その場合は --git-branch=main を設定する必要があります。 |
| --git-path  | Flux から Kubernetes マニフェストを特定するための Git リポジトリ内の相対パス。 |
| --git-readonly | Git リポジトリは読み取り専用と見なされます。Flux では書き込みが試行されなくなります。 |
| --manifest-generation  | 有効にすると、Flux によって .flux.yaml が検索され、Kustomize または他のマニフェスト ジェネレーターが実行されます。 |
| --git-poll-interval  | 新しいコミットに対して Git リポジトリをポーリングする期間。 既定は '5m' (5 分) です。 |
| --sync-garbage-collection  | 有効にすると、Flux によって作成されたリソースは削除されますが、Git には存在しなくなります。 |
| --git-label  | 同期の進行状況を追跡するためのラベル。Git ブランチのタグ付けに使用されます。  既定値は 'flux-sync' です。 |
| --git-user  | Git コミットのユーザー名。 |
| --git-email  | Git コミットに使用するメール アドレス。 |

* '--git-user' または '--git-email' が設定されていない場合 (つまり、Flux を使用してリポジトリに書き込みたくない場合)、--git-readonly が自動的に設定されます (まだ設定していない場合)。

詳細については、[Flux のドキュメント](https://aka.ms/FluxcdReadme)を参照してください。

> [!TIP]
> sourceControlConfiguration は、Azure portal の Azure Arc 対応 Kubernetes リソースの **[GitOps]** タブで作成できます。

## <a name="validate-the-sourcecontrolconfiguration"></a>sourceControlConfiguration を確認する

Azure CLI を使用して、`sourceControlConfiguration` の作成が成功したことを確認します。

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration` リソースは、コンプライアンスの状態、メッセージ、デバッグの情報によって更新されることにご注意ください。

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

`sourceControlConfiguration` が作成または更新されると、内部でいくつかの処理が行われます。

1. Azure Arc `config-agent` によって新規または更新された構成 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) の Azure Resource Manager が監視され、新しい `Pending` 構成が認識されます。
1. `config-agent` によって構成プロパティが読み取られ、ターゲットの名前空間が作成されます。
1. Azure Arc `controller-manager` によって適切なアクセス許可 (`cluster` または `namespace` スコープ) を持つ Kubernetes サービス アカウントが準備され、`flux` のインスタンスがデプロイされます。
1. Flux が生成したキーで SSH のオプションを使用している場合、`flux` を使用すると、SSH キーが生成され、公開キーがログに記録されます。
1. `config-agent` により、Azure の `sourceControlConfiguration` リソースに状態が報告されます。

プロビジョニング プロセスが実行されている間、`sourceControlConfiguration` はいくつかの状態に遷移します。 上記の `az k8sconfiguration show ...` のコマンドを使用して進行状況を監視します。

1. `complianceStatus` -> `Pending`: 初期状態と進行中の状態を表します。
1. `complianceStatus` -> `Installed`: `config-agent` によってクラスターが正常に構成され、エラーなしで `flux` がデプロイされました
1. `complianceStatus` -> `Failed`: `config-agent` で `flux` のデプロイ中にエラーが発生しました。詳細は `complianceStatus.message` 応答本文で確認できます

## <a name="apply-configuration-from-a-private-git-repository"></a>プライベート Git リポジトリから構成を適用する

プライベート Git リポジトリを使用している場合は、リポジトリで SSH 公開キーを構成する必要があります。 公開キーは、特定の Git リポジトリで構成するか、リポジトリにアクセスできる Git ユーザーが構成します。 SSH 公開キーは、自分で指定するか、または Flux によって生成されます。

**独自の公開キーを取得する**

独自の SSH キーを生成した場合は、既に秘密キーと公開キーを持っています。

**Azure CLI を使用して公開キーを取得する (Flux によってキーが生成される場合に便利)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Azure portal から公開キーを取得する (Flux によってキーが生成される場合に便利)**

1. Azure portal で、接続されているクラスター リソースに移動します。
2. [リソース] ページで GitOps を選択し、このクラスターの構成の一覧を表示します。
3. プライベート Git リポジトリを使用する構成を選択します。
4. 開いたコンテキスト ウィンドウで、ウィンドウの下部にある **[リポジトリの公開キー]** をコピーします。

GitHub を使用している場合は、次の 2 つのオプションのいずれかを使用します。

**オプション 1: ユーザー アカウントに公開キーを追加する (アカウントのすべてのリポジトリに適用)**

1. GitHub を開き、ページの右上隅にあるプロファイル アイコンをクリックします。
2. **[設定]** をクリックします。
3. **[SSH and GPG keys]\(SSH と GPG キー\)** をクリックします
4. **[New SSH key]\(新しい SSH キー\)** をクリックします
5. [タイトル] を入力します
6. 公開キーを貼り付けます (囲んでいる引用符は除きます)
7. **[Add SSH key]\(SSH キーの追加\)** をクリックします

**オプション 2: 公開キーをデプロイ キーとして Git リポジトリに追加する (このリポジトリにのみ適用)**

1. GitHub を開き、リポジトリに移動して、 **[設定]** 、 **[Deploy keys]\(デプロイ キー\)** の順に移動します
2. **[Add deploy key]\(デプロイ キーの追加\)** をクリックします
3. [タイトル] を入力します
4. **[Allow write access]\(書き込みアクセスを許可する\)** をオンにします
5. 公開キーを貼り付けます (囲んでいる引用符は除きます)
6. **[キーの追加]** をクリックします

**Azure DevOps リポジトリを使用している場合は SSH キーにキーを追加する**

1. 右上の (プロファイル画像の横にある) **[ユーザー設定]** で、 **[SSH public keys]\(SSH 公開キー\)** をクリックします
1. **[+ New Key]\(+ 新しいキー\)** を選択します
1. 名前を入力します
1. 囲んでいる引用符を含めずに公開キーを貼り付けます
1. **[追加]** をクリックします。

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes の構成を確認する

`config-agent` で `flux` インスタンスがインストールされた後、Git リポジトリに保持されているリソースがクラスターにフローし始めます。 名前空間、デプロイ、およびリソースが作成されていることを確認します。

```console
kubectl get ns --show-labels
```

**出力:**

```console
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

`team-a`、`team-b`、`itops`、および`cluster-config` の名前空間が作成されていることがわかります。

`sourceControlConfig` の指示に従って、`flux` 演算子が `cluster-config` 名前空間にデプロイされました。

```console
kubectl -n cluster-config get deploy  -o wide
```

**出力:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>さらに探索する

構成リポジトリの一部としてデプロイされた他のリソースを探索することができます。

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>構成を削除する

`sourceControlConfiguration` を削除するには、Azure CLI または Azure portal を使用します。  delete コマンドを開始すると、Azure の `sourceControlConfiguration` リソースはすぐに削除され、10 分以内に関連オブジェクトがクラスターから完全に削除されます。  `sourceControlConfiguration` が、削除されたときにエラー状態の場合、関連オブジェクトの完全な削除には最大 1 時間かかることがあります。

> [!NOTE]
> 名前空間スコープを持つ sourceControlConfiguration を作成した後、名前空間で `edit` ロールがバインドされているユーザーは、この名前空間にワークロードをデプロイできます。 名前空間スコープを持つこの `sourceControlConfiguration` が削除されると、その名前空間はそのまま残り、他のワークロードが中断されることを防ぐために削除されません。  必要に応じて、kubectl を使用してこの名前空間を手動で削除できます。
> `sourceControlConfiguration` が削除されても、追跡対象の Git リポジトリからのデプロイの結果としてクラスターに加えられた変更は削除されません。

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>次のステップ

- [ソース管理の構成で Helm を使用する](./use-gitops-with-helm.md)
- [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)