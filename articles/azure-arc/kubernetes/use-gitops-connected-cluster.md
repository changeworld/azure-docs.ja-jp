---
title: Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps を使用して Azure Arc 対応の Kubernetes クラスターを構成する (プレビュー)
keywords: GitOps、Kubernetes、K8s、Azure、Arc、Azure Kubernetes Service、AKS、コンテナー
ms.openlocfilehash: 3cadcdf80abd997ec10aeb9521680944d455898f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560177"
---
# <a name="deploy-configurations-using-gitops-on-an-arc-enabled-kubernetes-cluster-preview"></a>Arc 対応 Kubernetes クラスターに対して GitOps を使用して構成をデプロイする (プレビュー)

この記事では、Azure Arc 対応 Kubernetes クラスターでの構成の適用について説明します。 概念の概要については、[こちら](./conceptual-configurations.md)を参照してください。

## <a name="before-you-begin"></a>始める前に

* Azure Arc 対応 Kubernetes に接続されたクラスターが既に存在することを確認します。 接続されたクラスターが必要な場合は、[Azure Arc 対応 Kubernetes クラスターの接続に関するクイックスタート](./connect-cluster.md)を参照してください。

* この機能の利点とアーキテクチャについては、[Kubernetes 向け Arc での構成と GitOps に関する記事](./conceptual-configurations.md)を参照してください。

## <a name="create-a-configuration"></a>構成を作成する

この記事で使用されている[リポジトリの例](https://github.com/Azure/arc-k8s-demo)は、いくつかの名前空間をプロビジョニングし、共通のワークロードをデプロイし、チーム固有の構成を提供するクラスター オペレーターのペルソナを中心に構成されています。 このリポジトリを使用すると、クラスターに次のリソースが作成されます。

* **Namespaces:** `cluster-config`、`team-a`、`team-b`
* **Deployment:** `cluster-config/azure-vote`
* **ConfigMap:** `team-a/endpoints`

`config-agent` を使用すると、新規または更新された `sourceControlConfiguration` について Azure がポーリングされます。 このタスクには最大 30 秒かかります。

`sourceControlConfiguration` を持つプライベート リポジトリを関連付ける場合は、[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)手順を完了します。

### <a name="using-azure-cli"></a>Azure CLI の使用

`k8sconfiguration` の Azure CLI 拡張機能を使用して、接続されたクラスターを [Git リポジトリの例](https://github.com/Azure/arc-k8s-demo)にリンクしてください。 
1. この構成に `cluster-config` と名前を付けます。
1. `cluster-config` 名前空間にオペレーターを配置するようにエージェントに指示します。
1. オペレーターに `cluster-admin` アクセス許可を付与します。

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
| `--repository-url` | http[s]://server/repo[.git] or git://server/repo[.git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>SSH および Flux で作成されたキーを使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能

> [!NOTE]
> Flux によって生成される公開キーは、Git サービス プロバイダーのユーザー アカウントに追加する必要があります。 キーがユーザー アカウントではなくリポジトリに追加された場合は、`user@` の代わりに `git@` を URL で使用します。 詳細については、「[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)」セクションを参照してください。

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>SSH および ユーザー指定のキーを使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| `--ssh-private-key` | base64-encoded キー ([PEM 形式](https://aka.ms/PEMformat)) | キーを直接指定 |
| `--ssh-private-key-file` | ローカル ファイルへの完全なパス | PEM 形式のキーを含むローカル ファイルへの完全なパスを指定

> [!NOTE]
> 独自の秘密キーを直接またはファイルに指定します。 キーは [PEM 形式](https://aka.ms/PEMformat) で、末尾に改行文字 (\n) を使用する必要があります。  関連付けられた公開キーは、Git サービス プロバイダーのユーザー アカウントに追加する必要があります。 キーがユーザー アカウントではなくリポジトリに追加された場合は、`user@` の代わりに `git@` を使用します。 詳細については、「[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)」セクションを参照してください。

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH および ユーザー指定の既知のホストを使用してプライベート Git ホストを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| `--ssh-known-hosts` | base64-encoded | 既知のホストのコンテンツを直接指定 |
| `--ssh-known-hosts-file` | ローカル ファイルへの完全なパス | 既知のホストのコンテンツをローカル ファイルに指定 |

> [!NOTE]
> Flux 演算子は、SSH 接続を確立する前に Git リポジトリを認証するために、既知のホスト ファイルに一般的な Git ホストの一覧を保持します。 一般的ではない Git リポジトリまたは独自の Git ホストを使用している場合は、ホスト キーを指定して、Flux がリポジトリを識別できるようにする必要があります。 known_host のコンテンツは、直接指定することも、ファイルに指定することもできます。 独自のコンテンツを指定する場合は、[known_hosts のコンテンツ形式の仕様](https://aka.ms/KnownHostsFormat)を、前述の SSH キー シナリオの 1 つと組み合わせて使用します。

#### <a name="use-a-private-git-repo-with-https"></a>HTTPS を使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | 基本認証を使用した HTTPS |
| `--https-user` | raw または base64-encoded | HTTPS のユーザー名 |
| `--https-key` | raw または base64-encoded | HTTPS の個人用アクセス トークンまたはパスワード

> [!NOTE]
> HTTPS Helm リリースのプライベート認証は、バージョン 1.2.0 以上 (既定) の Helm 演算子グラフでのみサポートされます。
> HTTPS Helm リリースのプライベート認証は現在、Azure Kubernetes Services のマネージド クラスターではサポートされていません。
> プロキシを介して Git リポジトリにアクセスするために Flux が必要な場合は、プロキシ設定を使用して Azure Arc エージェントを更新する必要があります。 詳細については、「[送信プロキシ サーバーを使用して接続する](./connect-cluster.md#connect-using-an-outbound-proxy-server)」を参照してください。

#### <a name="additional-parameters"></a>追加のパラメーター

次の省略可能なパラメーターを使用して構成をカスタマイズします。

| パラメーター | [説明] |
| ------------- | ------------- |
| `--enable-helm-operator`| Helm グラフの配置のサポートを有効にするように切り替えます。 |
| `--helm-operator-params` | Helm 演算子 (有効な場合) のグラフの値。 たとえば、「 `--set helm.versions=v3` 」のように入力します。 |
| `--helm-operator-version` | Helm 演算子 (有効な場合) のグラフのバージョン。 バージョン 1.2.0 以降を使用します。 既定値は1.2.0 です。 |
| `--operator-namespace` | 演算子の名前空間の名前。 既定値は 'default' です。 最大:23 文字。 |
| `--operator-params` | 演算子のパラメーター。 単一引用符で囲む必要があります。 たとえば、```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` のように指定します。 

##### <a name="options-supported-in----operator-params"></a>`--operator-params` でサポートされているオプション:

| オプション | [説明] |
| ------------- | ------------- |
| `--git-branch`  | Kubernetes マニフェストに使用する Git リポジトリのブランチ。 既定値は 'master' です。 新しいリポジトリではルート ブランチの名前が `main` になっていますが、その場合は `--git-branch=main` を設定する必要があります。 |
| `--git-path`  | Flux から Kubernetes マニフェストを特定するための Git リポジトリ内の相対パス。 |
| `--git-readonly` | Git リポジトリは読み取り専用と見なされます。Flux では書き込みが試行されなくなります。 |
| `--manifest-generation`  | 有効にすると、Flux によって .flux.yaml が検索され、Kustomize または他のマニフェスト ジェネレーターが実行されます。 |
| `--git-poll-interval`  | 新しいコミットに対して Git リポジトリをポーリングする期間。 既定値は `5m` (5 分間) です。 |
| `--sync-garbage-collection`  | 有効にすると、Flux によって作成されたリソースは削除されますが、Git には存在しなくなります。 |
| `--git-label`  | 同期の進行状況を追跡するためのラベル。 Git ブランチのタグ付けに使用されます。  既定値は `flux-sync` です。 |
| `--git-user`  | Git コミットのユーザー名。 |
| `--git-email`  | Git コミットに使用するメール アドレス。 

Flux でリポジトリに書き込む必要がなく、`--git-user` または `--git-email` が設定されていない場合は、`--git-readonly` が自動的に設定されます。

詳細については、[Flux のドキュメント](https://aka.ms/FluxcdReadme)を参照してください。

> [!TIP]
> `sourceControlConfiguration` は、Azure portal の Azure Arc 対応 Kubernetes リソースの **[GitOps]** タブで作成できます。

## <a name="validate-the-sourcecontrolconfiguration"></a>sourceControlConfiguration を確認する

Azure CLI を使用して、`sourceControlConfiguration` の作成が成功したことを確認します。

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

`sourceControlConfiguration` リソースで、コンプライアンスの状態、メッセージ、およびデバッグの情報が更新されます。

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

| 変更のステージング | [説明] |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 初期状態と進行中の状態を表します。 |
| `complianceStatus` -> `Installed`  | `config-agent` によってクラスターが正常に構成され、エラーなしで `flux` がデプロイされました。 |
| `complianceStatus` -> `Failed` | `config-agent` で `flux` のデプロイ中にエラーが発生しました。詳細は `complianceStatus.message` 応答本文で確認できます。 |

## <a name="apply-configuration-from-a-private-git-repository"></a>プライベート Git リポジトリから構成を適用する

プライベート Git リポジトリを使用している場合は、リポジトリで SSH 公開キーを構成する必要があります。 SSH 公開キーは、Flux によって生成されるか、自分で指定します。 公開キーは、特定の Git リポジトリで構成するか、リポジトリにアクセスできる Git ユーザーが構成します。 

### <a name="get-your-own-public-key"></a>独自の公開キーを取得する

独自の SSH キーを生成した場合は、既に秘密キーと公開キーを持っています。

#### <a name="get-the-public-key-using-azure-cli"></a>Azure CLI を使用して公開キーを取得する 

次の例は、Flux でキーが生成される場合に役立ちます。

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Azure portal から公開キーを取得する

次の例は、Flux でキーが生成される場合に役立ちます。

1. Azure portal で、接続されているクラスター リソースに移動します。
2. [リソース] ページで GitOps を選択し、このクラスターの構成の一覧を表示します。
3. プライベート Git リポジトリを使用する構成を選択します。
4. 開いたコンテキスト ウィンドウで、ウィンドウの下部にある **[リポジトリの公開キー]** をコピーします。

#### <a name="add-public-key-using-github"></a>GitHub を使用して公開キーを追加する

次のいずれかのオプションを使用します。

* オプション 1: ユーザー アカウントに公開キーを追加します (アカウントのすべてのリポジトリに適用)。  
    1. GitHub を開き、ページの右上隅にあるプロファイル アイコンをクリックします。
    2. **[設定]** をクリックします。
    3. **[SSH および GPG キー]** をクリックします。
    4. **[新しい SSH キー]** をクリックします。
    5. [タイトル] を入力します。
    6. 囲んでいる引用符を含めずに公開キーを貼り付けます。
    7. **[SSH キーの追加]** をクリックします。

* オプション 2:公開キーをデプロイ キーとして Git リポジトリに追加します (このリポジトリにのみ適用)。  
    1. GitHub を開き、リポジトリに移動します。
    1. **[設定]** をクリックします。
    1. **[デプロイ キー]** をクリックします。
    1. **[デプロイ キーの追加]** をクリックします。
    1. [タイトル] を入力します。
    1. **[書き込みアクセスを許可する]** をオンにします。
    1. 囲んでいる引用符を含めずに公開キーを貼り付けます。
    1. **[キーの追加]** をクリックします。

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Azure DevOps リポジトリを使用して公開キーを追加する

キーを SSH キーに追加するには、次の手順に従います。

1. 右上の (プロファイル画像の横にある) **[ユーザー設定]** で、 **[SSH 公開キー]** をクリックします。
1. **[+ 新しいキー]** を選択します。
1. 名前を入力します。
1. 囲んでいる引用符を含めずに公開キーを貼り付けます。
1. **[追加]** をクリックします。

## <a name="validate-the-kubernetes-configuration"></a>Kubernetes の構成を確認する

`config-agent` で `flux` インスタンスがインストールされた後、Git リポジトリに保持されているリソースがクラスターにフローし始めます。 次のコマンドを使用して、名前空間、デプロイ、およびリソースが作成されていることを確認します。

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

次を使用して、構成リポジトリの一部としてデプロイされた他のリソースを探索することができます。

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>構成を削除する

`sourceControlConfiguration` を削除するには、Azure CLI または Azure portal を使用します。  delete コマンドを開始すると、Azure の`sourceControlConfiguration` リソースはすぐに削除されます。 10 分以内に関連オブジェクトがクラスターから完全に削除されます。 削除されたときに `sourceControlConfiguration` がエラー状態の場合、関連オブジェクトの完全な削除には最大 1 時間かかることがあります。

> [!NOTE]
> `namespace` スコープを持つ `sourceControlConfiguration` を作成した後、名前空間で `edit` ロールがバインドされているユーザーは、この名前空間にワークロードをデプロイすることができます。 名前空間スコープを持つこの `sourceControlConfiguration` が削除されると、その名前空間はそのまま残り、他のワークロードが中断されることを防ぐために削除されません。 必要に応じて、`kubectl` を使用してこの名前空間を手動で削除できます。  
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
