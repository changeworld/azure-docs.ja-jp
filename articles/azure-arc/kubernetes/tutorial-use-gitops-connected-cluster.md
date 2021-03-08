---
title: 'チュートリアル: GitOps を使用して Azure Arc 対応 Kubernetes クラスターに構成をデプロイする'
description: このチュートリアルでは、Azure Arc 対応 Kubernetes クラスターに対する構成の適用について説明します。 このプロセスの概念については、Azure Arc 対応 Kubernetes での構成と GitOps に関する記事をご覧ください。
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 64299bd05e82cf6f5452cde3f3da5622eff25e56
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121475"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>チュートリアル: GitOps を使用して Azure Arc 対応 Kubernetes クラスターに構成をデプロイする 

このチュートリアルでは、GitOps を使用して Azure Arc 対応 Kubernetes クラスターに構成を適用します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * サンプルの Git リポジトリを使用して、Azure Arc 対応 Kubernetes クラスターの構成を作成する。
> * 構成が正常に作成されたことを確認する。
> * プライベート Git リポジトリから構成を適用する。
> * Kubernetes の構成を確認する。

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料でアカウントを作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- Azure Arc 対応 Kubernetes に接続された既存のクラスター。
    - まだクラスターを接続していない場合は、[Azure Arc 対応 Kubernetes クラスターの接続に関するクイックスタート](quickstart-connect-cluster.md)をご覧ください。
- この機能の利点とアーキテクチャについて理解していること。 詳細については、[Azure Arc 対応 Kubernetes での構成と GitOps に関する記事](conceptual-configurations.md)をご覧ください。

## <a name="create-a-configuration"></a>構成を作成する

この記事で使用される[リポジトリの例](https://github.com/Azure/arc-k8s-demo)は、クラスター オペレーターのペルソナに基づいて構成されています。 このリポジトリのマニフェストでは、いくつかの名前空間をプロビジョニングし、ワークロードをデプロイして、チーム固有の構成を指定します。 このリポジトリを GitOps と共に使用すると、クラスターに次のリソースが作成されます。

* 名前空間: `cluster-config`、`team-a`、`team-b`
* デプロイ: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

`config-agent` を使用すると、新規または更新された構成について、Azure に対してポーリングが行われます。 このタスクには最大 30 秒かかります。

プライベート リポジトリを構成と関連付ける場合は、後の「[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)」に示されている手順を実行してください。

## <a name="use-azure-cli"></a>Azure CLI の使用
`k8s-configuration` の Azure CLI 拡張機能を使用して、接続されたクラスターを [Git リポジトリの例](https://github.com/Azure/arc-k8s-demo)にリンクしてください。 
1. この構成に `cluster-config` と名前を付けます。
1. `cluster-config` 名前空間にオペレーターを配置するようにエージェントに指示します。
1. オペレーターに `cluster-admin` アクセス許可を付与します。

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
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

### <a name="use-a-public-git-repository"></a>パブリック Git リポジトリを使用する

| パラメーター | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] or git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>SSH および Flux で作成されたキーを使用してプライベート Git リポジトリを使用する

Flux によって生成された公開キーを、利用している Git サービス プロバイダーのユーザー アカウントに追加します。 キーをユーザー アカウントではなくリポジトリに追加する場合は、URL で `user@` の代わりに `git@` を使用します。 

詳細については、「[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)」セクションを参照してください。


| パラメーター | Format | Notes
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>SSH およびユーザー指定のキーを使用してプライベート Git リポジトリを使用する

独自の秘密キーを直接またはファイルに指定します。 キーは [PEM 形式](https://aka.ms/PEMformat) で、末尾に改行文字 (\n) を使用する必要があります。 

関連付けられている公開キーを、利用している Git サービス プロバイダーのユーザー アカウントに追加します。 キーをユーザー アカウントではなくリポジトリに追加する場合は、`user@` の代わりに `git@` を使用します。 

詳細については、「[プライベート Git リポジトリから構成を適用する](#apply-configuration-from-a-private-git-repository)」セクションを参照してください。  

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| `--ssh-private-key` | base64-encoded キー ([PEM 形式](https://aka.ms/PEMformat)) | キーを直接指定 |
| `--ssh-private-key-file` | ローカル ファイルへの完全なパス | PEM 形式のキーを含むローカル ファイルへの完全なパスを指定

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>SSH および ユーザー指定の既知のホストを使用してプライベート Git ホストを使用する

Flux オペレーターは、SSH 接続を確立する前に Git リポジトリを認証できるように、既知のホスト ファイルに一般的な Git ホストの一覧を保持します。 "*一般的ではない*" Git リポジトリまたは独自の Git ホストを使用している場合は、ホスト キーを指定して、Flux がリポジトリを識別できるようにする必要があります。 

秘密キーの場合と同様に、known_host の内容は、直接指定することも、ファイルで指定することもできます。 独自の内容を指定する場合は、[known_hosts の内容の形式の仕様](https://aka.ms/KnownHostsFormat)を、前述の SSH キーのシナリオのいずれかと共に使用します。

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] または user@server:repo[.git] | `git@` は `user@` に置き換え可能 |
| `--ssh-known-hosts` | base64-encoded | 既知のホストのコンテンツを直接指定 |
| `--ssh-known-hosts-file` | ローカル ファイルへの完全なパス | 既知のホストのコンテンツをローカル ファイルに指定 |

### <a name="use-a-private-git-repository-with-https"></a>HTTPS を使用してプライベート Git リポジトリを使用する

| パラメーター | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | 基本認証を使用した HTTPS |
| `--https-user` | raw または base64-encoded | HTTPS のユーザー名 |
| `--https-key` | raw または base64-encoded | HTTPS の個人用アクセス トークンまたはパスワード

>[!NOTE]
>* Helm オペレーター チャートのバージョン 1.2.0 以上では、HTTPS Helm リリースのプライベート認証をサポートしています。
>* HTTPS Helm リリースは、AKS マネージド クラスターではサポートされていません。
>* Flux でプロキシを介して Git リポジトリにアクセスする必要がある場合は、Azure Arc エージェントのプロキシ設定を更新する必要があります。 詳細については、「[送信プロキシ サーバーを使用して接続する](./connect-cluster.md#connect-using-an-outbound-proxy-server)」を参照してください。


## <a name="additional-parameters"></a>追加のパラメーター

次の省略可能なパラメーターを使用して構成をカスタマイズします。

| パラメーター | [説明] |
| ------------- | ------------- |
| `--enable-helm-operator`| Helm グラフの配置のサポートを有効にするように切り替えます。 |
| `--helm-operator-params` | Helm 演算子 (有効な場合) のグラフの値。 たとえば、「 `--set helm.versions=v3` 」のように入力します。 |
| `--helm-operator-chart-version` | Helm 演算子 (有効な場合) のグラフのバージョン。 バージョン 1.2.0 以降を使用します。 既定値は1.2.0 です。 |
| `--operator-namespace` | 演算子の名前空間の名前。 既定値は 'default' です。 最大:23 文字。 |
| `--operator-params` | 演算子のパラメーター。 単一引用符で囲む必要があります。 たとえば、```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` のように指定します。 

### <a name="options-supported-in----operator-params"></a>`--operator-params` でサポートされているオプション:

| オプション | 説明 |
| ------------- | ------------- |
| `--git-branch`  | Kubernetes マニフェストに使用する Git リポジトリのブランチ。 既定値は 'master' です。 新しいリポジトリではルート ブランチの名前が `main` になっていますが、その場合は `--git-branch=main` を設定する必要があります。 |
| `--git-path`  | Flux から Kubernetes マニフェストを特定するための Git リポジトリ内の相対パス。 |
| `--git-readonly` | Git リポジトリは読み取り専用と見なされます。 Flux はそこに書き込みは行いません。 |
| `--manifest-generation`  | 有効にすると、Flux によって .flux.yaml が検索され、Kustomize または他のマニフェスト ジェネレーターが実行されます。 |
| `--git-poll-interval`  | 新しいコミットについて Git リポジトリに対してポーリングを行う周期。 既定値は `5m` (5 分間) です。 |
| `--sync-garbage-collection`  | 有効にすると、Flux によって作成されたリソースは削除されますが、Git には存在しなくなります。 |
| `--git-label`  | 同期の進行状況を追跡するためのラベル。 Git ブランチのタグ付けに使用されます。  既定値は `flux-sync` です。 |
| `--git-user`  | Git コミットのユーザー名。 |
| `--git-email`  | Git コミットに使用するメール アドレス。 

Flux でリポジトリに書き込む必要がなく、`--git-user` または `--git-email` が設定されていない場合は、`--git-readonly` が自動的に設定されます。

詳細については、[Flux のドキュメント](https://aka.ms/FluxcdReadme)を参照してください。

> [!TIP]
> 構成は、Azure portal の Azure Arc 対応 Kubernetes リソースの **[GitOps]** タブで作成できます。

## <a name="validate-the-configuration"></a>構成の検証

Azure CLI を使用して、構成の作成が成功したことを確認します。

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

構成リソースで、コンプライアンスの状態、メッセージ、およびデバッグの情報が更新されます。

```output
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

構成が作成または更新されると、いくつかの処理が行われます。

1. Azure Arc `config-agent` によって新規または更新された構成 (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) の Azure Resource Manager が監視され、新しい `Pending` 構成が認識されます。
1. `config-agent` によって構成プロパティが読み取られ、ターゲットの名前空間が作成されます。
1. Azure Arc `controller-manager` によって Kubernetes サービス アカウントが作成され、[ClusterRoleBinding または RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) にマップされます。適切なアクセス許可 (`cluster` または `namespace` スコープ) が得られるようにするためです。 次に、`flux` のインスタンスがデプロイされます。
1. Flux が生成したキーで SSH のオプションを使用している場合、`flux` を使用すると、SSH キーが生成され、公開キーがログに記録されます。
1. `config-agent` により、Azure の構成リソースに状態がレポートされます。

プロビジョニング プロセスが実行されている間、構成リソースはいくつかの状態に遷移します。 上記の `az k8s-configuration show ...` のコマンドを使用して進行状況を監視します。

| 変更のステージング | [説明] |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | 初期状態と進行中の状態を表します。 |
| `complianceStatus` -> `Installed`  | `config-agent` によってクラスターが正常に構成され、エラーなしで `flux` がデプロイされました。 |
| `complianceStatus` -> `Failed` | `flux` のデプロイ中に `config-agent` でエラーが発生しました。 詳細は `complianceStatus.message` 応答本文で確認できます。 |

## <a name="apply-configuration-from-a-private-git-repository"></a>プライベート Git リポジトリから構成を適用する

プライベート Git リポジトリを使用している場合は、リポジトリで SSH 公開キーを構成する必要があります。 SSH 公開キーは自分で指定するか、Flux で生成します。 公開キーは、特定の Git リポジトリに対して構成することも、リポジトリにアクセスできる Git ユーザーに対して構成することもできます。 

### <a name="get-your-own-public-key"></a>独自の公開キーを取得する

独自の SSH キーを生成した場合は、既に秘密キーと公開キーを持っています。

#### <a name="get-the-public-key-using-azure-cli"></a>Azure CLI を使用して公開キーを取得する 

Flux でキーを生成している場合は、Azure CLI で次を使用します。

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Azure portal から公開キーを取得する

Flux でキーを生成している場合は、Azure portal で次の手順を実行します。

1. Azure portal で、接続されているクラスター リソースに移動します。
2. [リソース] ページで GitOps を選択し、このクラスターの構成の一覧を表示します。
3. プライベート Git リポジトリを使用する構成を選択します。
4. 開いたコンテキスト ウィンドウで、ウィンドウの下部にある **[リポジトリの公開キー]** をコピーします。

#### <a name="add-public-key-using-github"></a>GitHub を使用して公開キーを追加する

次のいずれかのオプションを使用します。

* オプション 1: 自分のユーザー アカウントに公開キーを追加します (自分のアカウントの全リポジトリに適用)。  
    1. GitHub を開き、ページの右上隅にあるプロファイル アイコンをクリックします。
    2. **[設定]** をクリックします。
    3. **[SSH および GPG キー]** をクリックします。
    4. **[新しい SSH キー]** をクリックします。
    5. [タイトル] を入力します。
    6. 囲んでいる引用符を含めずに公開キーを貼り付けます。
    7. **[SSH キーの追加]** をクリックします。

* オプション 2: 公開キーをデプロイ キーとして Git リポジトリに追加します (このリポジトリにのみ適用)。  
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

```output
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

構成リソースの指示に従って、`flux` オペレーターが `cluster-config` 名前空間にデプロイされています。

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
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
## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure CLI または Azure portal を使用して、構成を削除します。 delete コマンドを実行すると、構成リソースは Azure からすぐに削除されます。 10 分以内に関連オブジェクトがクラスターから完全に削除されます。 削除時に構成でエラーが発生している場合、関連オブジェクトが完全に削除されるまでに最大 1 時間かかることがあります。

`namespace` スコープが適用された構成を削除しても、Azure Arc によって名前空間が削除されることはありません。既存のワークロードが中断されないようにするためです。 必要に応じて、`kubectl` を使用してこの名前空間を手動で削除できます。

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> 構成を削除しても、追跡対象の Git リポジトリからのデプロイの結果としてクラスターに加えられた変更が削除されることはありません。

## <a name="next-steps"></a>次のステップ

次のチュートリアルに進み、GitOps を使用した CI/CD の実装方法を学習してください。
> [!div class="nextstepaction"]
> [GitOps を使用して CI/CD を実装する](./tutorial-gitops-ci-cd.md)
