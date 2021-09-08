---
title: Azure Arc 対応 Kubernetes クラスター用の Azure RBAC
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Azure Arc 対応 Kubernetes クラスターでの承認チェックに Azure RBAC を使用します。
ms.openlocfilehash: 2607f82e0935ead0b6013bae963e22616c340b80
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2021
ms.locfileid: "123105041"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Azure Active Directory と Azure Arc 対応 Kubernetes クラスターの統合

Kubernetes の [ClusterRoleBinding および RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) オブジェクト型は、Kubernetes でネイティブに承認を定義するのに役立ちます。 この機能を使用すると、Azure Active Directory (Azure AD) と Azure のロールの割り当てを使用して、クラスターでの承認チェックを制御できます。 これは、Azure のロールの割り当てを使用して、デプロイ、ポッド、サービスなどの Kubernetes オブジェクトの読み取り、書き込み、削除を行うことができるユーザーを細かく制御できるようになることを意味します。

この機能の概念の概要については、「[Azure Arc 対応 Kubernetes での Azure RBAC](conceptual-azure-rbac.md)」を参照してください。

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>前提条件

- バージョン 2.16.0 以降の [Azure CLI をインストールするか、このバージョンにアップグレードします](/cli/azure/install-azure-cli)。

- `connectedk8s` Azure CLI 拡張機能バージョン 1.1.0 以降をインストールします。

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    `connectedk8s` 拡張機能が既にインストールされている場合は、次のコマンドを使用して最新バージョンに更新できます。 

    ```azurecli
    az extension update --name connectedk8s
    ```

- 既存の Azure Arc 対応 Kubernetes クラスターを接続します。
    - クラスターをまだ接続していない場合は[クイックスタート](quickstart-connect-cluster.md)を使用します。
    - バージョン 1.1.0 以降に[お使いのエージェントをアップグレードします](agent-upgrade.md#manually-upgrade-agents)。

> [!NOTE]
> この機能は、ユーザーがクラスターの API サーバーにアクセスできない、Elastic Kubernetes Service や Google Kubernetes Engine などのクラウド プロバイダーのマネージド Kubernetes オファリングには設定できません。 Azure Kubernetes Service (AKS) クラスターの場合、この[機能はネイティブで利用可能](../../aks/manage-azure-rbac.md)であり、AKS クラスターを Azure Arc に接続する必要はありません。

## <a name="set-up-azure-ad-applications"></a>Azure AD アプリケーションを設定する

### <a name="create-a-server-application"></a>サーバー アプリケーションを作成する

1. 新しい Azure AD アプリケーションを作成し、その `appId` 値を取得します。 この値は、後の手順で `serverApplicationId` として使用されます。

    ```azurecli
    CLUSTERNAME="<clusterName>"
    SERVER_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Server" --identifier-uris "https://${CLUSTERNAME}Server" --query appId -o tsv)
    echo $SERVER_APP_ID
    ```

1. アプリケーションのグループ メンバーシップ要求を更新します。

    ```azurecli
    az ad app update --id "${SERVER_APP_ID}" --set groupMembershipClaims=All
    ```

1. サービス プリンシパルを作成し、その `password` フィールド値を取得します。 この値は、後ほどクラスターでこの機能を有効にするときに `serverApplicationSecret` として必要になります。

    ```azurecli
    az ad sp create --id "${SERVER_APP_ID}"
    SERVER_APP_SECRET=$(az ad sp credential reset --name "${SERVER_APP_ID}" --credential-description "ArcSecret" --query password -o tsv)
    ```

1. アプリケーションに "サインインとユーザー プロファイルの読み取り" API のアクセス許可を付与します。

    ```azurecli
    az ad app permission add --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id "${SERVER_APP_ID}" --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > この手順は、Azure テナント管理者が実行する必要があります。
    > 
    > 運用環境でこの機能を使用する場合は、クラスターごとに異なるサーバー アプリケーションを作成することをお勧めします。

### <a name="create-a-client-application"></a>クライアント アプリケーションを作成する

1. 新しい Azure AD アプリケーションを作成し、その `appId` 値を取得します。 この値は、後の手順で `clientApplicationId` として使用されます。

    ```azurecli
    CLIENT_APP_ID=$(az ad app create --display-name "${CLUSTERNAME}Client" --native-app --reply-urls "https://${CLUSTERNAME}Client" --query appId -o tsv)
    echo $CLIENT_APP_ID
    ```

2. このクライアント アプリケーション用のサービス プリンシパルを作成します。

    ```azurecli
    az ad sp create --id "${CLIENT_APP_ID}"
    ```

3. サーバー アプリケーションの `oAuthPermissionId` 値を取得します。

    ```azurecli
    az ad app show --id "${SERVER_APP_ID}" --query "oauth2Permissions[0].id" -o tsv
    ```

4. クライアント アプリケーションに必要なアクセス許可を付与します。

    ```azurecli
    az ad app permission add --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}" --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id "${CLIENT_APP_ID}" --api "${SERVER_APP_ID}"
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>サーバー アプリケーション用のロールの割り当てを作成する

要求を行っているユーザーが、要求の一部である Kubernetes オブジェクトで承認されているかどうかを確認するため、サーバー アプリケーションには `Microsoft.Authorization/*/read` アクセス許可が必要です。

1. 次の内容で *accessCheck.json* という名前のファイルを作成します。

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    `<subscription-id>` は実際のサブスクリプション ID に置き換えます。

2. 次のコマンドを実行して、新しいカスタム ロールを作成します。

    ```azurecli
    ROLE_ID=$(az role definition create --role-definition ./accessCheck.json --query id -o tsv)
    ```

3. 作成したロールを使用して、サーバー アプリケーションに `assignee` としてロールの割り当てを作成します。

    ```azurecli
    az role assignment create --role "${ROLE_ID}" --assignee "${SERVER_APP_ID}" --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-the-cluster"></a>クラスターで Azure RBAC を有効にする

次のコマンドを実行して、Arc 対応 Kubernetes クラスターで Azure ロールベースのアクセス制御 (RBAC) を有効にします。

```console
az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id "${SERVER_APP_ID}" --app-secret "${SERVER_APP_SECRET}"
```
    
> [!NOTE]
> 上記のコマンドを実行する前に、マシン上の `kubeconfig` ファイルで、Azure RBAC 機能を有効にするクラスターを参照していることを確認します。
>
> Azure RBAC ではなく、Kubernetes ネイティブの `ClusterRoleBinding` オブジェクトと `RoleBinding` オブジェクトを使用して承認チェックを受けるユーザー名、メール アドレス、OpenID 接続のコンマ区切りリストを表示するには、上記のコマンドで `--skip-azure-rbac-list` を使用します。

### <a name="generic-cluster-where-no-reconciler-is-running-on-the-apiserver-specification"></a>apiserver 仕様で競合回避モジュールが実行されていない汎用クラスター

1. クラスターの各マスター ノードに SSH 接続し、次の手順を実行します。

    1. `apiserver` マニフェストを編集モードで開きます。
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. `volumes` に次の指定を追加します。
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. `volumeMounts` に次の指定を追加します。

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. 次の `apiserver` 引数を追加します。

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Kubernetes クラスターがバージョン 1.19.0 以降の場合は、次の `apiserver` 引数も設定する必要があります。

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. 保存してエディターを閉じ、`apiserver` ポッドを更新します。


### <a name="cluster-created-by-using-cluster-api"></a>Cluster API を使用して作成されたクラスター

1. 認証と承認の Webhook 構成ファイルを含むガード シークレットを、ワークロード クラスターからマシンにコピーします。

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. *azure-arc-guard-manifests.yaml* ファイルの `namespace` フィールドを、ワークロード クラスターを作成するためのカスタム リソースを適用する管理クラスター内の名前空間に変更します。

1. このマニフェストを適用します。

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. `kubectl edit kcp <clustername>-control-plane` を実行して、`KubeadmControlPlane` オブジェクトを編集します。
    
    1. 次のスニペットを `files` に追加します。
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. 次のスニペットを `apiServer` > `extraVolumes` に追加します。
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. 次のスニペットを `apiServer` > `extraArgs` に追加します。
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. 保存して閉じ、`KubeadmControlPlane` オブジェクトを更新します。 ワークロード クラスターにこれらの変更が表示されるまで待ちます。


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>ユーザーがクラスターにアクセスするためのロールの割り当てを作成する

Azure Arc 対応 Kubernetes リソースの所有者は、組み込みロールまたはカスタム ロールを使用して、他のユーザーに Kubernetes クラスターへのアクセス権を付与できます。

### <a name="built-in-roles"></a>組み込みのロール

| ロール | 説明 |
|---|---|
| [Azure Arc Kubernetes ビューアー](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | 名前空間内のほとんどのオブジェクトを表示するための読み取り専用アクセスが許可されます。 このロールでは、シークレットの表示は許可されません。 これは、シークレットに対する `read` アクセス許可によって、名前空間内の `ServiceAccount` の資格情報にアクセスできるようになるためです。 これらの資格情報により、その `ServiceAccount` 値を使用して API アクセスが可能になります (特権エスカレーションの一形式)。 |
| [Azure Arc Kubernetes ライター](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | 名前空間内のほとんどのオブジェクトに対する読み取りと書き込みのアクセスが許可されます。 このロールでは、ロールまたはロールのバインドを表示または変更することはできません。 ただし、このロールでは、名前空間内の任意の `ServiceAccount` 値としてシークレットにアクセスし、ポッドを実行できます。 そのため、これを使用して、名前空間内の任意の `ServiceAccount` 値の API アクセス レベルを取得できます。 |
| [Azure Arc Kubernetes 管理者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | 管理者アクセスを許可します。 これは、`RoleBinding` を使用して名前空間内で付与することを目的としています `RoleBinding` で使用すると、名前空間内でロールとロール バインドを作成できるなど、名前空間内のほとんどのリソースへの読み取り/書き込みアクセスが許可されます。 このロールでは、リソース クォータまたは名前空間自体への書き込みアクセスは許可されません。 |
| [Azure Arc Kubernetes クラスター管理者](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | 任意のリソースに対して任意のアクションを実行できるスーパーユーザー アクセスが許可されます。 `ClusterRoleBinding` で使用すると、クラスター内およびすべての名前空間内のすべてのリソースを完全に制御できます。 `RoleBinding` で使用すると、ロール バインドの名前空間内のすべてのリソース (名前空間自体を含む) を完全に制御できます。|

Azure portal のクラスター リソースの **[アクセス制御 (IAM)]** ペインで、Arc 対応 Kubernetes クラスターをスコープとするロールの割り当てを作成できます。 次の Azure CLI コマンドを使用することもできます。

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

これらのコマンドで、`AZURE-AD-ENTITY-ID` には、ユーザー名 (例: `testuser@mytenant.onmicrosoft.com`) を指定することも、サービス プリンシパルの `appId` 値を指定することもできます。

クラスター内の特定の名前空間をスコープとしたロールの割り当てを作成する別の例を次に示します。

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> クラスターをスコープとしたロールの割り当ては、Azure portal と Azure CLI のどちらでも作成できますが、名前空間をスコープとしたロールの割り当てを作成するには、CLI を使用する必要があります。

### <a name="custom-roles"></a>カスタム ロール

ロールの割り当てで使用する独自のロールの定義を作成することもできます。

ユーザーにデプロイの読み取りだけを許可するロールの定義の例を次に示します。 詳細については、[ロールの定義の作成に使用できるデータ アクションの完全な一覧](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes)を参照してください。

次の JSON オブジェクトを、*custom-role.json* というファイルにコピーします。 `<subscription-id>` プレースホルダーは、実際のサブスクリプション ID に置き換えます。 カスタム ロールでは、いずれかのデータ アクションを使用し、ロールの割り当てが作成されたスコープ (クラスターまたは名前空間) 内のすべてのデプロイを表示できます。

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. *custom-role.json* を保存したフォルダーから次のコマンドを実行して、ロールの定義を作成します。

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. このカスタム ロールの定義を使用して、ロールの割り当てを作成します。

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>ユーザー資格情報を使用して kubectl を構成する

クラスターにアクセスするために必要な *kubeconfig* ファイルを取得するには、次の 2 つの方法があります。

- Azure Arc 対応 Kubernetes クラスターの[クラスター接続](cluster-connect.md)機能 (`az connectedk8s proxy`) を使用します。
- クラスター管理者が、他のすべてのユーザーと *kubeconfig* ファイルを共有します。

### <a name="if-youre-accessing-the-cluster-by-using-the-cluster-connect-feature"></a>クラスター接続機能を使用してクラスターにアクセスする場合

次のコマンドを実行して、プロキシ プロセスを開始します。

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

プロキシ プロセスの実行後、コンソールで別のタブを開いて、[クラスターへの要求の送信を開始](#send-requests-to-the-cluster)できます。

### <a name="if-the-cluster-admin-shared-the-kubeconfig-file-with-you"></a>クラスター管理者と kubeconfig ファイルを共有している場合 

1. 次のコマンドを実行して、ユーザーの資格情報を設定します。

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. 前に作成した *kubeconfig* ファイルを開きます。 `contexts` で、クラスターに関連付けられているコンテキストが、前の手順で作成したユーザー資格情報を参照していることを確認します。

1. `user` > `config` に、**config-mode** 設定を追加します。
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="send-requests-to-the-cluster"></a>クラスターに要求を送信する

1. 任意の `kubectl` コマンドを実行します。 次に例を示します。
   * `kubectl get nodes` 
   * `kubectl get pods`

1. ブラウザーベースの認証を求められたら、デバイス ログイン URL (`https://microsoft.com/devicelogin`) をコピーし、Web ブラウザーで開きます。

1. コンソールに出力されたコードを入力します。 ターミナルのコードをコピーして、デバイス認証入力のプロンプトに貼り付けます。

1. ユーザー名 (`testuser@mytenant.onmicrosoft.com`) と、関連付けられているパスワードを入力します。

1. このようなエラー メッセージが表示された場合は、要求したリソースへのアクセスが承認されていないことを意味します。

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    管理者は、このユーザーがリソースにアクセスすることを承認する新しいロールの割り当てを作成する必要があります。

## <a name="use-conditional-access-with-azure-ad"></a>条件付きアクセスと Azure AD を組み合わせて使用する

Azure AD を Arc 対応 Kubernetes クラスターと統合すると、[条件付きアクセス](../../active-directory/conditional-access/overview.md)を使用してクラスターへのアクセスを制御することもできます。

> [!NOTE]
> Azure AD 条件付きアクセスは Azure AD Premium の機能です。

クラスターで使用する条件付きアクセス ポリシーの例を作成するには、次の手順を行います。

1. Azure portal の上部で、 **[Azure Active Directory]** を検索して選択します。
1. 左側の Azure Active Directory のメニューで、 **[エンタープライズ アプリケーション]** を選択します。
1. 左側のエンタープライズ アプリケーションのメニューで、 **[条件付きアクセス]** を選択します。
1. 左側の条件付きアクセスのメニューで、 **[ポリシー]**  >  **[新しいポリシー]** の順に選択します。
    
    [ ![条件付きアクセス ポリシーを追加するためのボタンを示すスクリーンショット。](./media/azure-rbac/conditional-access-new-policy.png) ](./media/azure-rbac/conditional-access-new-policy.png#lightbox)

1. ポリシーの名前 (例: **arc-k8s-policy**) を入力します。
1. **[ユーザーとグループ]** を選択します。 **[含める]** で、 **[ユーザーとグループの選択]** を選択します。 次に、ポリシーを適用するユーザーとグループを選択します。 この例では、クラスターへの管理アクセス権を持つ同じ Azure AD グループを選択します。

    [ ![条件付きアクセス ポリシーを適用するユーザーまたはグループの選択を示すスクリーンショット。](./media/azure-rbac/conditional-access-users-groups.png) ](./media/azure-rbac/conditional-access-users-groups.png#lightbox)

1. **[クラウド アプリまたは操作]** を選択します。 **[含める]** で、 **[アプリを選択]** を選択します。 次に、前に作成したサーバー アプリケーションを検索して選択します。

    [ ![条件付きアクセス ポリシーを適用するサーバー アプリケーションの選択を示すスクリーンショット。](./media/azure-rbac/conditional-access-apps.png) ](./media/azure-rbac/conditional-access-apps.png#lightbox)

1. **[アクセス制御]** で **[許可]** を選択します。 **[アクセス権の付与]**  >  **[デバイスは準拠としてマーク済みである必要があります]** の順に選択します。

    [ ![準拠デバイスにのみ条件付きアクセス ポリシーを許可する選択を示すスクリーンショット。](./media/azure-rbac/conditional-access-grant-compliant.png) ](./media/azure-rbac/conditional-access-grant-compliant.png#lightbox)
    
1. **[ポリシーの有効化]** で、 **[オン]**  >  **[作成]** の順に選択します。

    [ ![条件付きアクセス ポリシーの有効化を示すスクリーンショット。](./media/azure-rbac/conditional-access-enable-policies.png) ](./media/azure-rbac/conditional-access-enable-policies.png#lightbox)

クラスターに再びアクセスします。 たとえば、`kubectl get nodes` コマンドを実行して、クラスター内のノードを表示します。

```console
kubectl get nodes
```

手順に従ってもう一度サインインします。 エラー メッセージに、正常にログインしたが、管理者の要求により、アクセスを要求しているデバイスは Azure AD で管理されていないとリソースにアクセスできないことが示されます。 次の手順に従います。

1. Azure portal で、**[Azure Active Directory]** に移動します。
1. **[エンタープライズ アプリケーション]** を選択します。 次に、 **[アクティビティ]** で **[サインイン]** を選択します。 
1. 上部のエントリは、 **[状態]** が **[失敗]** 、 **[条件付きアクセス]** が **[成功]** になっています。 このエントリを選択し、 **[詳細]** で **[条件付きアクセス]** を選択します。 条件付きアクセス ポリシーが表示されます。

   [ ![条件付きアクセス ポリシーにより失敗したサインイン エントリを示すスクリーンショット。](./media/azure-rbac/conditional-access-sign-in-activity.png) ](./media/azure-rbac/conditional-access-sign-in-activity.png#lightbox)

## <a name="configure-just-in-time-cluster-access-with-azure-ad"></a>Azure AD を使用して Just-In-Time クラスター アクセスを構成する

クラスター アクセス制御のもう 1 つの選択肢は、Just-In-Time 要求に Privileged Identity Management (PIM) を使用することです。

>[!NOTE]
> PIM は、Premium P2 SKU を必要とする Azure AD Premium の機能です。 Azure AD SKU の詳細については、[料金ガイド](https://azure.microsoft.com/pricing/details/active-directory/)を参照してください。

ご利用のクラスターに対して Just-In-Time アクセス要求を構成するには、次の手順を行います。

1. Azure portal の上部で、 **[Azure Active Directory]** を検索して選択します。
1. テナント ID を書き留めておきます。 この手順の残りの部分では、この ID を `<tenant-id>` と表記しています。

    [ ![Azure Active Directory テナントの詳細を示すスクリーンショット。](./media/azure-rbac/jit-get-tenant-id.png) ](./media/azure-rbac/jit-get-tenant-id.png#lightbox)

1. 左側の Azure Active Directory のメニューの **[管理]** で、 **[グループ]**  >  **[新しいグループ]** の順に選択します。

    [ ![新しいグループを作成するための選択を示すスクリーンショット。](./media/azure-rbac/jit-create-new-group.png) ](./media/azure-rbac/jit-create-new-group.png#lightbox)

1. **[グループの種類]** で **[セキュリティ]** が選択されていることを確認します。  グループ名 (例: **myJITGroup**) を入力します。 **[グループに Azure AD ロールを割り当てることができる (プレビュー)]** で **[はい]** を選択します。 最後に、 **[作成]** を選択します。

    [ ![新しいグループの詳細を示すスクリーンショット。](./media/azure-rbac/jit-new-group-created.png) ](./media/azure-rbac/jit-new-group-created.png#lightbox)

1. **[グループ]** ページに戻ります。 新しく作成したグループを選択し、オブジェクト ID を書き留めておきます。 この手順の残りの部分では、この ID を `<object-id>` と表記しています。

    [ ![作成したグループのオブジェクト識別子を示すスクリーンショット。](./media/azure-rbac/jit-get-object-id.png) ](./media/azure-rbac/jit-get-object-id.png#lightbox)

1. Azure portal に戻り、左側の **[アクティビティ]** のメニューで、 **[特権アクセス (プレビュー)]** を選択します。 次に、 **[特権アクセスの有効化]** を選択します。

    [ ![特権アクセスを有効にするための選択を示すスクリーンショット。](./media/azure-rbac/jit-enabling-priv-access.png) ](./media/azure-rbac/jit-enabling-priv-access.png#lightbox)

1. **[割り当ての追加]** を選択して、アクセス権の付与を開始します。

    [ ![アクティブな割り当てを追加するためのボタンを示すスクリーンショット。](./media/azure-rbac/jit-add-active-assignment.png) ](./media/azure-rbac/jit-add-active-assignment.png#lightbox)

1. **[メンバー]** ロールを選択し、クラスターへのアクセス権を付与するユーザーとグループを選択します。 グループ管理者は、これらの割り当てをいつでも変更できます。 先に進む準備ができたら、 **[次へ]** を選択します。

    [ ![割り当ての追加を示すスクリーンショット。](./media/azure-rbac/jit-adding-assignment.png) ](./media/azure-rbac/jit-adding-assignment.png#lightbox)

1. 割り当ての種類として **[アクティブ]** を選択し、目的の期間を選択して、理由を入力します。 続行する準備ができたら、 **[割り当て]** を選択します。 割り当ての種類の詳細については、「[Privileged Identity Management で特権アクセス グループ (プレビュー) の資格を割り当てる](../../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group)」を参照してください。

    [ ![割り当てのプロパティの選択を示すスクリーンショット。](./media/azure-rbac/jit-set-active-assignment.png) ](./media/azure-rbac/jit-set-active-assignment.png#lightbox)

割り当てが完了したら、クラスターにアクセスして、Just-In-Time アクセスが機能していることを確認します。 たとえば、`kubectl get nodes` コマンドを使用して、クラスター内のノードを表示します。

```console
kubectl get nodes
```

認証要件を確認し、手順に従って認証します。 認証が成功すると、次のような出力が表示されます。

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.

NAME      STATUS   ROLES    AGE      VERSION
node-1    Ready    agent    6m36s    v1.18.14
node-2    Ready    agent    6m42s    v1.18.14
node-3    Ready    agent    6m33s    v1.18.14
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [クラスター接続](cluster-connect.md)を使用してクラスターに安全に接続します。
